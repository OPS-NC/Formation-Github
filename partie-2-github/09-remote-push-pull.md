# 🚀 Module 09 — Publier son code sur GitHub

> ⏱️ **Durée** : 20 min\
> 🎯 **Objectif** : relier le dépôt local à GitHub, pousser le code, et synchroniser dans les deux sens.

---

## 1. 🔗 Ajouter le dépôt distant

On reprend le dépôt de la partie 1 :

```bash
cd ~/formation/todo-app
git status          # doit être "clean"
git remote -v       # vide : aucun distant pour l'instant
```

Ajoutons GitHub comme distant nommé `origin`, **en HTTPS** pour commencer (remplacez `pseudo` par votre identifiant GitHub) :

```bash
git remote add origin https://github.com/pseudo/todo-app.git
git remote -v
```

```text
origin  https://github.com/pseudo/todo-app.git (fetch)
origin  https://github.com/pseudo/todo-app.git (push)
```

💡 `origin` n'est qu'un **nom** : c'est un alias pour l'URL, choisi par convention. Un dépôt peut avoir plusieurs distants (on en ajoutera un second au module 11).

| Commande | Effet |
|---|---|
| `git remote add <nom> <url>` | Ajouter un distant |
| `git remote -v` | Lister les distants |
| `git remote rename <ancien> <nouveau>` | Renommer |
| `git remote remove <nom>` | Supprimer (ne touche pas au dépôt GitHub) |
| `git remote set-url <nom> <url>` | Changer l'URL |

---

## 2. ⬆️ Premier push (HTTPS)

```bash
git push -u origin main
```

🪟 La fenêtre **Git Credential Manager** s'ouvre → *Sign in with your browser* → **Authorize**. C'est la seule fois où on vous le demandera.

```text
Enumerating objects: 45, done.
...
To https://github.com/pseudo/todo-app.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

Le `-u` relie `main` local à `origin/main` : désormais `git push` et `git pull` suffisent, sans préciser `origin main`.

🔄 **Rafraîchissez la page GitHub du dépôt** : votre code est en ligne ! 🎉

Explorez :
- 📄 Le `README.md` est **affiché en page d'accueil**, mis en forme (Markdown)
- 🕐 Cliquez sur **N commits** : tout votre historique de la partie 1 est là, avec vos messages
- 🔍 Cliquez sur un commit : le *diff* coloré, comme `git show`
- 📁 Cliquez sur `todo.py` puis **Blame** : l'équivalent web de `git blame`
- ❓ Et `taches.json` ? Absent : il est dans `.gitignore` ✅

---

## 3. 🔐 Basculer en SSH

Maintenant que la clé SSH est déclarée (module 08), passons le dépôt en SSH :

```bash
git remote set-url origin git@github.com:pseudo/todo-app.git
git remote -v
```

💡 L'URL SSH se trouve aussi sur GitHub : bouton vert **<> Code → onglet SSH**.

Testons avec un petit commit. Ajoutez au `README.md` :

```markdown
## Installation

    git clone https://github.com/pseudo/todo-app.git
    cd todo-app
    python todo.py aide
```

```bash
git commit -am "Instructions d'installation dans le README"
git push
```

✅ Pas de fenêtre de connexion : c'est la clé SSH qui a servi (éventuellement la passphrase vous est demandée si l'agent n'est pas lancé).

---

## 4. ✏️ Modifier directement sur GitHub… puis récupérer

GitHub permet d'éditer des fichiers dans le navigateur. Utile pour une petite correction, et parfait pour simuler « un collègue a poussé quelque chose ».

### 4.1 Ajouter une licence depuis GitHub

1. Sur la page du dépôt : **Add file → Create new file**
2. Nom du fichier : `LICENSE` → un bouton **Choose a license template** apparaît
3. Choisissez **MIT License** → *Review and submit*
4. **Commit changes…** → message `Ajout de la licence MIT` → *Commit directly to the main branch* → **Commit changes**

### 4.2 Modifier le README depuis GitHub

1. Ouvrez `README.md` → icône ✏️ (*Edit this file*)
2. Ajoutez une ligne `Licence : MIT` en bas
3. **Commit changes…**

💡 Astuce : appuyez sur la touche `.` (point) sur la page d'un dépôt : un éditeur **VS Code complet** s'ouvre dans le navigateur (github.dev).

### 4.3 Récupérer en local

GitHub a maintenant **2 commits** que votre poste n'a pas.

```bash
git status                      # "up to date"… car pas encore de contact avec GitHub
git fetch
git status                      # behind 'origin/main' by 2 commits
git log --oneline main..origin/main
git pull
ls                              # LICENSE est là
```

---

## 5. 💥 Le push refusé (rappel du module 06)

Reproduisons la situation classique :

1. Sur **GitHub**, modifiez `CHANGELOG.md` (ajoutez `- Publication sur GitHub`) et committez
2. En **local**, sans faire de pull, modifiez `todo.py` (par exemple la docstring de la 1re ligne) et committez
3. Poussez :

```bash
git commit -am "Docstring plus précise"
git push
```

```text
 ! [rejected]        main -> main (fetch first)
```

Même solution qu'au module 06 :

```bash
git pull --no-edit
git push
```

💡 **Bonne habitude** : `git pull` avant de commencer à travailler, `git push` dès qu'un travail cohérent est committé.

---

## 6. 📥 Cloner depuis GitHub

Imaginez que vous changez de poste. Récupérer le projet :

```bash
cd ~/formation
git clone git@github.com:pseudo/todo-app.git todo-app-poste2
cd todo-app-poste2
git log --oneline -3
git remote -v               # origin est déjà configuré
```

💡 Pour un dépôt **public**, n'importe qui peut cloner en HTTPS sans compte : `git clone https://github.com/git/git.git`.

🏆 **Bonus** : cloner seulement l'historique récent d'un gros dépôt : `git clone --depth 1 <url>`.

```bash
cd ~/formation
rm -rf todo-app-poste2
```

---

## 📝 À retenir

```text
  git remote add origin <url>   → déclarer GitHub comme distant
  git push -u origin main       → premier push (relie main à origin/main)
  git push / git pull           → ensuite, au quotidien
  git remote set-url origin …   → passer de HTTPS à SSH
  git clone <url>               → récupérer un dépôt GitHub
  Push refusé → git pull → (conflit ?) → git push
```

---

⬅️ Précédent : [Module 08 — Authentification HTTPS et SSH](08-authentification-https-ssh.md) · ➡️ Suite : [Module 10 — Branches et Pull Requests](10-branches-et-pull-requests.md)
