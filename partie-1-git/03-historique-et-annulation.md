# ⏪ Module 03 — Historique et retour arrière

> ⏱️ **Durée** : 30 min
> 🎯 **Objectif** : naviguer dans l'historique et savoir annuler **n'importe quelle** erreur sans paniquer.

---

## 1. 🔭 Explorer l'historique

```bash
git log --oneline --graph --all
```

Le graphe est encore une ligne droite, il deviendra intéressant avec les branches (module 04).

### Désigner un commit

| Notation | Signification |
|---|---|
| `b7e9c21` | Un commit par son SHA (7 premiers caractères suffisent) |
| `HEAD` | Le commit sur lequel vous êtes actuellement |
| `HEAD~1` (ou `HEAD^`) | Le parent de `HEAD` |
| `HEAD~3` | 3 commits avant `HEAD` |
| `main` | Le dernier commit de la branche `main` |

### Inspecter

```bash
git show                     # le dernier commit (message + modifications)
git show HEAD~2              # l'avant-avant-dernier
git show HEAD~2 --stat       # seulement les fichiers touchés
git diff HEAD~3 HEAD         # différences entre deux commits
git log --oneline -- README.md   # uniquement les commits qui touchent README.md
git log -S "Total" --oneline     # les commits qui ont ajouté/retiré le texte "Total"
```

💡 `git log -S` (la « pioche ») est redoutable pour retrouver *quand* une ligne de code est apparue ou a disparu.

---

## 2. 🗺️ Quelle commande pour annuler quoi ?

| Situation | Commande | Dangereux ? |
|---|---|---|
| J'ai modifié un fichier et je veux revenir à la dernière version committée | `git restore <fichier>` | ⚠️ Perte des modifs non committées |
| J'ai fait `git add` par erreur | `git restore --staged <fichier>` | ✅ Non |
| J'ai oublié un fichier / fait une faute dans le **dernier** commit | `git commit --amend` | ⚠️ Réécrit l'historique |
| Je veux annuler un commit **déjà partagé** | `git revert <sha>` | ✅ Non, ajoute un commit |
| Je veux supprimer les derniers commits **locaux** | `git reset <sha>` | ⚠️ Réécrit l'historique |
| J'ai tout cassé 😱 | `git reflog` | ✅ C'est la bouée de sauvetage |

---

## 3. 🧹 `git restore` — annuler des modifications non committées

🧪 **TP** : faites une « bêtise » dans `todo.py` — supprimez toute la fonction `lister` et enregistrez.

```bash
git status            # modified: todo.py
git diff              # on voit les lignes supprimées
git restore todo.py
git status            # working tree clean
```

✅ Le fichier est revenu à son état du dernier commit.

⚠️ Les modifications annulées par `git restore` ne sont **pas** récupérables : elles n'ont jamais été committées.

🧪 **TP** : désindexer un fichier

```bash
echo "Notes perso" > notes.txt
git add notes.txt
git status                     # new file: notes.txt (indexé)
git restore --staged notes.txt
git status                     # notes.txt redevient untracked, le fichier existe toujours
rm notes.txt
```

💡 Vous verrez souvent dans des tutos plus anciens `git checkout -- fichier` et `git reset HEAD fichier` : ce sont les anciennes syntaxes de `git restore` et `git restore --staged`.

---

## 4. ✏️ `git commit --amend` — corriger le dernier commit

🧪 **TP** : créez un fichier `CHANGELOG.md` et committez avec une faute :

```bash
echo "# Historique des versions" > CHANGELOG.md
git add CHANGELOG.md
git commit -m "Ajout du chnagelog"
```

Oups, une faute de frappe. Et on voulait aussi y ajouter une ligne :

```bash
echo "" >> CHANGELOG.md
echo "## Non publié" >> CHANGELOG.md
git add CHANGELOG.md
git commit --amend -m "Ajout du changelog"
git log --oneline -2
```

✅ Un seul commit, corrigé. Mais regardez son SHA : il a **changé**. `--amend` ne modifie pas le commit, il le **remplace** par un nouveau.

⚠️ **Règle d'or** : ne jamais réécrire (amend, reset, rebase) des commits **déjà partagés** (pushés). Les autres ont l'ancienne version : vous créeriez des historiques divergents. 💥

---

## 5. ↩️ `git revert` — annuler proprement un commit

`revert` crée un **nouveau commit** qui fait l'inverse d'un commit existant. L'historique n'est pas réécrit : c'est la méthode **sûre** pour annuler un commit déjà partagé.

🧪 **TP** : ajoutez une ligne de debug dans la fonction `ajouter`, juste après `sauvegarder(taches)` :

```python
    print("DEBUG", taches)
```

```bash
git commit -am "Debug temporaire"
python todo.py ajouter "Test"     # la ligne DEBUG s'affiche
git revert HEAD --no-edit
python todo.py ajouter "Test 2"   # plus de DEBUG
git log --oneline -3
```

```text
e4f5a6b (HEAD -> main) Revert "Debug temporaire"
c3d2e1f Debug temporaire
...
```

✅ Les deux commits restent visibles dans l'historique : on sait ce qui s'est passé.

---

## 6. 🔙 `git reset` — déplacer la branche en arrière

`git reset <commit>` fait **reculer** la branche courante jusqu'à `<commit>`. Les commits suivants sont « détachés » de la branche. Trois modes, selon ce qu'on fait des modifications de ces commits :

| Mode | Historique | Index | Répertoire de travail | Usage |
|---|---|---|---|---|
| `--soft` | reculé | **conserve** les modifs (indexées) | conservé | Refaire le commit autrement |
| `--mixed` (défaut) | reculé | vidé | **conserve** les modifs (non indexées) | Re-choisir quoi committer |
| `--hard` | reculé | vidé | ⚠️ **modifs effacées** | Tout jeter |

🧪 **TP** : soft puis mixed

```bash
echo "Fichier inutile" > essai.txt
git add essai.txt
git commit -m "Essai"

git reset --soft HEAD~1
git status            # essai.txt est indexé : le commit est défait, pas le travail

git reset             # (--mixed, sur HEAD) : on désindexe
git status            # essai.txt est non suivi

rm essai.txt
```

---

## 7. 🛟 `git reflog` — la bouée de sauvetage

Git note dans le **reflog** chaque déplacement de `HEAD` (commit, reset, changement de branche…), pendant 90 jours par défaut. Un commit « perdu » par un `reset --hard` y est encore.

🧪 **TP** : perdre un commit… et le retrouver

```bash
git log --oneline -3          # notez que "Ajout du changelog" est là
git reset --hard HEAD~3       # 😱 on recule de 3 commits
git log --oneline -3          # le changelog et le revert ont disparu
ls                            # CHANGELOG.md a disparu du dossier !
```

Pas de panique :

```bash
git reflog
```

```text
1a2b3c4 (HEAD -> main) HEAD@{0}: reset: moving to HEAD~3
e4f5a6b HEAD@{1}: revert: Revert "Debug temporaire"
c3d2e1f HEAD@{2}: commit: Debug temporaire
...
```

`HEAD@{1}` est l'état juste avant le reset :

```bash
git reset --hard HEAD@{1}
git log --oneline -3
ls                            # CHANGELOG.md est de retour 🎉
```

💡 Tant qu'un travail a été **committé**, il est quasiment impossible de le perdre avec Git. D'où le conseil : **committez souvent**.

---

## 8. 🕰️ Voyager dans le temps

Voir le projet tel qu'il était quelques commits plus tôt :

```bash
git log --oneline
git switch --detach HEAD~4     # ou : git checkout <sha>
cat todo.py
```

Git vous prévient : vous êtes en **`detached HEAD`** — `HEAD` pointe directement sur un commit et non sur une branche. On peut regarder, tester… mais les commits faits ici ne seraient rattachés à aucune branche.

```bash
git switch main                # retour à la normale
```

Récupérer **un seul fichier** dans une ancienne version, sans tout déplacer :

```bash
git restore --source=HEAD~4 README.md
git diff                       # README.md est revenu à l'ancienne version (non committé)
git restore README.md          # on annule
```

---

## 9. 🗑️ Supprimer et renommer des fichiers suivis

```bash
git mv README.md LISEZMOI.md   # renommer (Git le note comme un renommage)
git status
git mv LISEZMOI.md README.md   # on remet comme avant

git rm fichier.txt             # supprime le fichier ET indexe la suppression
git rm --cached fichier.txt    # arrête de suivre le fichier, mais le garde sur le disque
```

---

## 📝 À retenir

```text
  git show / git log -S            → inspecter
  git restore <f>                  → jeter des modifs non committées
  git restore --staged <f>         → désindexer
  git commit --amend               → corriger le dernier commit (local !)
  git revert <sha>                 → annuler un commit en en créant un nouveau (sûr)
  git reset [--soft|--hard] <sha>  → reculer la branche (local !)
  git reflog                       → retrouver ce qu'on croyait perdu
```

⚠️ On ne réécrit pas un historique déjà partagé.

---

⬅️ Précédent : [Module 02 — TP Premier dépôt](02-premier-depot.md) · ➡️ Suite : [Module 04 — Branches et fusions](04-branches-et-fusions.md)
