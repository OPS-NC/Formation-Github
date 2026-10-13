# 🧬 Module 06 — Cloner et synchroniser en local

> ⏱️ **Durée** : 25 min
> 🎯 **Objectif** : comprendre `clone`, les dépôts distants (*remotes*), `push`, `fetch` et `pull`… **sans Internet**, en simulant un serveur sur votre propre poste.

💡 Tout ce que vous apprenez ici fonctionne **exactement** de la même manière avec GitHub. GitHub n'est qu'un dépôt distant de plus, hébergé ailleurs.

---

## 1. 📋 Cloner un dépôt local

```bash
cd ~/formation
git clone todo-app todo-app-copie
cd todo-app-copie
git log --oneline -5
```

✅ Tout l'historique est là. Un clone est une **copie complète** du dépôt.

```bash
git remote -v
```

```text
origin  C:/Users/vous/formation/todo-app (fetch)
origin  C:/Users/vous/formation/todo-app (push)
```

Le clone se souvient d'où il vient : c'est le dépôt distant nommé **`origin`** (nom par convention).

```bash
git branch -a
```

```text
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
```

`origin/main` est une **branche de suivi distante** (*remote-tracking branch*) : la photo, prise lors de la dernière synchronisation, de ce qu'est `main` sur `origin`. Vous ne travaillez jamais directement dessus : Git la met à jour quand vous communiquez avec le distant.

💡 `taches.json` n'est pas dans le clone : il est ignoré, il n'a jamais été committé. 👍

---

## 2. ⬇️ Récupérer les nouveautés : `fetch` et `pull`

Dans le dépôt **original**, faisons un commit :

```bash
cd ~/formation/todo-app
echo "- Version initiale" >> CHANGELOG.md
git commit -am "Mise à jour du changelog"
```

Revenons dans la copie :

```bash
cd ~/formation/todo-app-copie
git status                  # "up to date"... mais c'est faux !
```

`git status` compare à `origin/main` **tel qu'il était au dernier contact**. Git ne contacte jamais le distant de lui-même. Allons voir :

```bash
git fetch
git status
```

```text
Your branch is behind 'origin/main' by 1 commit, and can be fast-forwarded.
```

`fetch` a **téléchargé** les nouveaux commits et mis à jour `origin/main`, **sans toucher** à vos fichiers ni à votre `main`. On peut inspecter avant d'intégrer :

```bash
git log --oneline main..origin/main     # les commits distants que je n'ai pas
git diff main origin/main               # leurs modifications
git merge origin/main                   # on intègre
```

👉 **`git pull` = `git fetch` + `git merge`** en une commande :

```text
                 git fetch                     git merge
  Distant ─────────────────────► origin/main ─────────────► main (+ fichiers)
          └───────────────────────── git pull ──────────────────┘
```

💡 `fetch` est toujours sans risque. Prenez l'habitude de `fetch` puis regarder, plutôt que `pull` à l'aveugle.

---

## 3. 🚫 Pousser vers un dépôt « normal » ? Refusé

Faisons un commit dans la copie et tentons de l'envoyer à l'original :

```bash
echo "- Ajout de la copie" >> CHANGELOG.md
git commit -am "Test de push"
git push
```

```text
 ! [remote rejected] main -> main (branch is currently checked out)
```

Git refuse : le dépôt original a un **répertoire de travail** sur la branche `main`, quelqu'un pourrait être en train de travailler dedans. Écraser `main` sous ses pieds serait dangereux.

👉 Un **serveur** Git utilise un dépôt **nu** (*bare*) : uniquement le contenu de `.git`, sans répertoire de travail. C'est exactement ce que fait GitHub.

On annule notre commit de test :

```bash
git reset --hard origin/main
```

---

## 4. 🖥️ Simuler un serveur avec un dépôt *bare*

```bash
cd ~/formation
git clone --bare todo-app serveur/todo-app.git
ls serveur/todo-app.git
```

```text
HEAD  config  description  hooks/  info/  objects/  packed-refs  refs/
```

Pas de `todo.py` : seulement la base de données Git. Par convention, les dépôts nus se terminent par `.git`.

Deux développeurs, **Alice** et **Bob**, clonent ce « serveur » :

```bash
git clone serveur/todo-app.git alice
git clone serveur/todo-app.git bob

# Chacun son identité (config --local : uniquement pour ce dépôt)
git -C alice config user.name "Alice"
git -C bob config user.name "Bob"
```

💡 `git -C <dossier>` exécute la commande comme si on était dans `<dossier>`.

```text
                ┌──────────────────────────┐
                │ serveur/todo-app.git     │
                │ (bare = "GitHub" local)  │
                └─────▲──────────────▲─────┘
              push/pull│              │push/pull
                ┌──────┴─────┐  ┌─────┴──────┐
                │   alice/   │  │    bob/    │
                └────────────┘  └────────────┘
```

---

## 5. 🤝 TP — Alice et Bob collaborent

### 5.1 Alice pousse

```bash
cd ~/formation/alice
echo "# Contributeurs" > CONTRIBUTEURS.md
echo "- Alice" >> CONTRIBUTEURS.md
git add CONTRIBUTEURS.md
git commit -m "Ajout de la liste des contributeurs"
git push
```

```text
To C:/Users/vous/formation/serveur/todo-app.git
   9f8e7d6..a1b2c3d  main -> main
```

✅ Accepté : le serveur est un dépôt nu.

### 5.2 Bob récupère

```bash
cd ~/formation/bob
git pull
cat CONTRIBUTEURS.md
```

### 5.3 Travail simultané → push rejeté

Bob et Alice committent **chacun de leur côté** :

```bash
# Bob
cd ~/formation/bob
echo "- Bob" >> CONTRIBUTEURS.md
git commit -am "Bob s'ajoute aux contributeurs"

# Alice (sur un autre fichier)
cd ~/formation/alice
echo "" >> README.md
echo "Maintenu par l'équipe Todo." >> README.md
git commit -am "Mention de l'équipe dans le README"
git push                         # Alice pousse en premier ✅
```

Bob tente de pousser :

```bash
cd ~/formation/bob
git push
```

```text
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to '.../serveur/todo-app.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. ... You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
```

🛑 Git refuse : le serveur contient un commit (celui d'Alice) que Bob n'a pas. Pousser écraserait le travail d'Alice. Bob doit d'abord **intégrer**, puis pousser :

```bash
git pull --no-edit             # fetch + merge (commit de fusion)
git log --oneline --graph -4
git push                       # ✅
```

💡 Si Alice et Bob avaient modifié les **mêmes lignes**, `git pull` aurait produit un **conflit**, résolu exactement comme au module 04.

### 5.4 Partager une branche

```bash
# Alice crée une branche et la publie
cd ~/formation/alice
git switch -c feature/export
echo "# TODO : export CSV" > export.py
git add export.py
git commit -m "Squelette de l'export CSV"
git push -u origin feature/export
```

`-u` (`--set-upstream`) relie la branche locale à la branche distante : ensuite, un simple `git push` / `git pull` suffit sur cette branche.

```bash
# Bob la récupère
cd ~/formation/bob
git fetch
git branch -a                  # remotes/origin/feature/export apparaît
git switch feature/export      # crée automatiquement la branche locale qui suit origin/feature/export
cat export.py
```

### 5.5 Supprimer une branche distante

```bash
cd ~/formation/alice
git push origin --delete feature/export

cd ~/formation/bob
git fetch --prune              # nettoie les origin/* qui n'existent plus sur le serveur
git branch -a
```

---

## 6. 🧭 Comprendre `ahead` / `behind`

```bash
git status
```

| Message | Signification | Action |
|---|---|---|
| `up to date with 'origin/main'` | Identique au distant (lors du dernier fetch !) | — |
| `ahead of 'origin/main' by 2 commits` | Vous avez 2 commits non poussés | `git push` |
| `behind 'origin/main' by 3 commits` | Le distant a 3 commits que vous n'avez pas | `git pull` |
| `have diverged` | Les deux ont des commits différents | `git pull` puis `git push` |

---

## 🧹 Ménage

Pour la partie GitHub, on reprend notre dépôt principal `~/formation/todo-app`. Les dossiers d'entraînement peuvent être supprimés :

```bash
cd ~/formation
rm -rf todo-app-copie alice bob serveur
```

---

## 📝 À retenir

```text
  git clone <source> [dossier]  → copie complète d'un dépôt (crée le remote "origin")
  git remote -v                 → lister les dépôts distants
  git fetch                     → télécharger sans intégrer (sans risque)
  git pull                      → fetch + merge
  git push                      → envoyer ses commits
  git push -u origin <branche>  → publier une nouvelle branche
  git push origin --delete <b>  → supprimer une branche distante
  Push rejeté ? → git pull, (résoudre), puis git push
```

🎓 **Fin de la partie Git !** Vous maîtrisez l'essentiel. Place à GitHub. 🚀

---

⬅️ Précédent : [Module 05 — Stash et boîte à outils](05-stash-et-outils.md) · ➡️ Suite : [Partie 2 — Module 07 — Découvrir GitHub](../partie-2-github/07-decouvrir-github.md)
