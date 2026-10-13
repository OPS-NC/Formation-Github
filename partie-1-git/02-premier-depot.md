# 🧪 Module 02 — TP : premier dépôt

> ⏱️ **Durée** : 45 min\
> 🎯 **Objectif** : créer un dépôt, écrire une petite application Python et maîtriser le cycle **modifier → `add` → `commit`**.

---

## Étape 1 — 🐣 Créer le dépôt

```bash
cd ~/formation
mkdir todo-app
cd todo-app
git init
```

Résultat :

```text
Initialized empty Git repository in C:/Users/vous/formation/todo-app/.git/
```

Regardons ce qui a été créé :

```bash
ls -la
```

Un dossier caché `.git` est apparu : c'est **le dépôt**. Le dossier `todo-app` autour est votre **répertoire de travail**.

Premier réflexe à prendre :

```bash
git status
```

```text
On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

💡 Git parle anglais. Le message se lit : *« Sur la branche main. Pas encore de commit. Rien à committer. »*

---

## Étape 2 — 📄 Premier fichier, premier commit

Créez un fichier `README.md` :

```bash
echo "# Todo App" > README.md
git status
```

```text
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
```

Le fichier est **non suivi** (*untracked*) : Git le voit, mais ne s'en occupe pas. Ajoutons-le à l'index :

```bash
git add README.md
git status
```

```text
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   README.md
```

Le fichier est maintenant **indexé** (*staged*) : il est dans le carton 📦. Fermons le carton :

```bash
git commit -m "Ajout du README"
```

```text
[main (root-commit) 3f2a1b4] Ajout du README
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

```bash
git status
```

```text
On branch main
nothing to commit, working tree clean
```

✅ **Vérification** : *working tree clean* = tout ce qui est dans le dossier est enregistré dans l'historique. 🎉 Premier commit !

```bash
git log
```

```text
commit 3f2a1b4c9e... (HEAD -> main)
Author: Prénom Nom <vous@exemple.com>
Date:   Mon Oct 13 09:12:45 2026 +1100

    Ajout du README
```

---

## Étape 3 — 🐍 L'application Python

Ouvrez un nouveau fichier `todo.py` dans votre éditeur (`code todo.py` ou `notepad todo.py`) et collez :

```python
"""Gestionnaire de tâches en ligne de commande."""
import json
import sys
from pathlib import Path

FICHIER = Path("taches.json")


def charger():
    if FICHIER.exists():
        return json.loads(FICHIER.read_text(encoding="utf-8"))
    return []


def sauvegarder(taches):
    FICHIER.write_text(json.dumps(taches, indent=2, ensure_ascii=False), encoding="utf-8")


def ajouter(texte):
    taches = charger()
    taches.append({"texte": texte, "faite": False})
    sauvegarder(taches)
    print(f"Tâche ajoutée : {texte}")


def lister():
    taches = charger()
    if not taches:
        print("Aucune tâche.")
    for numero, tache in enumerate(taches, start=1):
        case = "x" if tache["faite"] else " "
        print(f"{numero}. [{case}] {tache['texte']}")


def main():
    if len(sys.argv) < 2:
        print("Usage : python todo.py [ajouter <texte> | lister]")
        return
    commande = sys.argv[1]
    if commande == "ajouter":
        ajouter(" ".join(sys.argv[2:]))
    elif commande == "lister":
        lister()
    else:
        print(f"Commande inconnue : {commande}")


if __name__ == "__main__":
    main()
```

Testez :

```bash
python todo.py ajouter "Apprendre Git"
python todo.py ajouter "Apprendre GitHub"
python todo.py lister
```

```text
Tâche ajoutée : Apprendre Git
Tâche ajoutée : Apprendre GitHub
1. [ ] Apprendre Git
2. [ ] Apprendre GitHub
```

💡 Les accents s'affichent mal (`TÃ¢che`) ? Voir [Dépannage → accents Python](../annexes/depannage.md#-les-accents-saffichent-mal-dans-la-sortie-python).

```bash
git status
```

```text
Untracked files:
        taches.json
        todo.py
```

🤔 Deux fichiers non suivis. Mais `taches.json` contient **vos données personnelles**, générées à l'exécution : elles n'ont rien à faire dans l'historique du code.

---

## Étape 4 — 🙈 Le fichier `.gitignore`

Le fichier `.gitignore` liste ce que Git doit **ignorer**. Créez-le à la racine du projet :

```bash
cat > .gitignore << 'EOF'
# Données locales de l'application
taches.json

# Python
__pycache__/
*.pyc
.venv/

# Système / éditeurs
.vscode/
Thumbs.db
desktop.ini
EOF
```

💡 Sous PowerShell, créez simplement le fichier avec votre éditeur : `code .gitignore`.

```bash
git status
```

```text
Untracked files:
        .gitignore
        todo.py
```

✅ `taches.json` a disparu de la liste : il est ignoré. On ajoute et on committe :

```bash
git add todo.py .gitignore
git commit -m "Ajout de l'application todo (ajouter, lister)"
```

📌 **Règles de syntaxe `.gitignore`**

| Motif | Ignore |
|---|---|
| `taches.json` | Tout fichier nommé ainsi, à n'importe quel niveau |
| `/taches.json` | Uniquement à la racine |
| `logs/` | Tout dossier `logs` |
| `*.log` | Tous les fichiers `.log` |
| `!important.log` | Exception : **ne pas** ignorer ce fichier |

⚠️ `.gitignore` n'agit que sur les fichiers **non suivis**. Un fichier déjà committé continue d'être suivi : il faut d'abord le retirer de l'index avec `git rm --cached fichier`.

💡 GitHub maintient des modèles prêts à l'emploi par langage : [github.com/github/gitignore](https://github.com/github/gitignore).

---

## Étape 5 — ✍️ Bien écrire ses messages de commit

Un bon message explique **quoi** et surtout **pourquoi**, pour la personne qui lira l'historique dans 6 mois (souvent vous-même 😅).

| ❌ À éviter | ✅ Mieux |
|---|---|
| `modif` | `Ajout de la commande lister` |
| `fix` | `Correction du crash quand le fichier JSON est vide` |
| `wip`, `test`, `azerty` | `Affichage du nombre de tâches restantes` |

Conventions courantes :
- Première ligne **courte** (≤ 50-72 caractères), à l'impératif ou sous forme nominale, sans point final
- Si besoin, une ligne vide puis un paragraphe d'explication (utilisez `git commit` sans `-m` pour ouvrir l'éditeur)
- Certaines équipes utilisent les [**Conventional Commits**](https://www.conventionalcommits.org/fr/) : `feat: ajout de la commande lister`, `fix: …`, `docs: …`

---

## Étape 6 — 🔍 Voir ce qui a changé : `git diff`

Modifions la fonction `lister` pour afficher un total. Remplacez-la par :

```python
def lister():
    taches = charger()
    if not taches:
        print("Aucune tâche.")
    for numero, tache in enumerate(taches, start=1):
        case = "x" if tache["faite"] else " "
        print(f"{numero}. [{case}] {tache['texte']}")
    print(f"Total : {len(taches)} tâche(s)")
```

```bash
git status
```

```text
Changes not staged for commit:
        modified:   todo.py
```

Qu'est-ce qui a changé exactement ?

```bash
git diff
```

```diff
@@ -30,6 +30,7 @@ def lister():
     for numero, tache in enumerate(taches, start=1):
         case = "x" if tache["faite"] else " "
         print(f"{numero}. [{case}] {tache['texte']}")
+    print(f"Total : {len(taches)} tâche(s)")
```

Les lignes en `+` (vert) sont ajoutées, celles en `-` (rouge) supprimées.

💡 Si la sortie est longue, Git ouvre un visualiseur : flèches pour défiler, `q` pour quitter.

Maintenant, indexons et regardons de nouveau :

```bash
git add todo.py
git diff
```

😮 Rien ! Car `git diff` compare le **répertoire de travail** à l'**index**. Pour voir ce qui est indexé (donc ce qui va partir dans le commit) :

```bash
git diff --staged
```

```text
  Répertoire ──git diff──► Index ──git diff --staged──► Dernier commit
```

```bash
git commit -m "Affichage du nombre total de tâches"
```

---

## Étape 7 — 📦 L'intérêt de l'index : des commits ciblés

Faisons **deux modifications sans rapport** :

1. Dans `todo.py`, améliorez le message d'usage :

```python
        print("Usage : python todo.py <commande> [arguments]")
        print("Commandes : ajouter <texte>, lister")
```

2. Dans `README.md`, ajoutez une description :

```markdown
# Todo App

Petit gestionnaire de tâches en ligne de commande, écrit en Python.

## Utilisation

    python todo.py ajouter "Ma tâche"
    python todo.py lister
```

```bash
git status
```

Les deux fichiers sont modifiés. Plutôt qu'un commit fourre-tout, on fait **deux commits logiques** :

```bash
git add todo.py
git commit -m "Message d'usage plus explicite"

git add README.md
git commit -m "Documentation de l'utilisation dans le README"
```

🏆 **Bonus** : `git add -p` permet d'indexer un fichier **morceau par morceau** (`y` = oui, `n` = non, `s` = découper, `q` = quitter). Idéal quand un même fichier contient deux modifications sans rapport.

---

## Étape 8 — ⚡ Raccourcis utiles

| Commande | Effet |
|---|---|
| `git add .` | Indexe **tout** le dossier courant (nouveaux, modifiés, supprimés) |
| `git add -A` | Indexe tout le dépôt |
| `git commit -am "msg"` | `add` + `commit` des fichiers **déjà suivis** (⚠️ ignore les nouveaux fichiers) |
| `git status -s` | Statut condensé |

⚠️ `git add .` est pratique mais dangereux : relisez **toujours** `git status` avant de committer pour ne pas embarquer un fichier de mots de passe ou un fichier de 2 Go.

Exemple de `git status -s` :

```text
 M todo.py       ← modifié, non indexé
M  README.md     ← modifié, indexé
?? notes.txt     ← non suivi
A  nouveau.py    ← nouveau, indexé
```

---

## Étape 9 — 📜 Consulter l'historique

```bash
git log                 # complet
git log --oneline       # une ligne par commit
git log --stat          # avec les fichiers touchés
git log -p              # avec le détail des modifications
git log -3              # les 3 derniers
git log --author="Nom"  # filtrer par auteur
```

```text
$ git log --oneline
b7e9c21 (HEAD -> main) Documentation de l'utilisation dans le README
4d1a0f3 Message d'usage plus explicite
9c3e2b8 Affichage du nombre total de tâches
1a7f6d5 Ajout de l'application todo (ajouter, lister)
3f2a1b4 Ajout du README
```

---

## 🧪 Exercice — À vous de jouer

1. Ajoutez une commande `aide` qui affiche le message d'usage
2. Vérifiez avec `git diff` puis committez avec un message clair
3. Ajoutez une ligne `*.tmp` au `.gitignore`, créez un fichier `essai.tmp` et vérifiez qu'il n'apparaît pas dans `git status`. Committez le `.gitignore`.

<details>
<summary>💡 Solution</summary>

```python
def afficher_aide():
    print("Usage : python todo.py <commande> [arguments]")
    print("Commandes : ajouter <texte>, lister, aide")


def main():
    if len(sys.argv) < 2:
        afficher_aide()
        return
    commande = sys.argv[1]
    if commande == "ajouter":
        ajouter(" ".join(sys.argv[2:]))
    elif commande == "lister":
        lister()
    elif commande == "aide":
        afficher_aide()
    else:
        print(f"Commande inconnue : {commande}")
```

```bash
git diff
git commit -am "Ajout de la commande aide"

echo "*.tmp" >> .gitignore
touch essai.tmp
git status            # essai.tmp n'apparaît pas
git commit -am "Ignorer les fichiers temporaires"
```

</details>

---

## 📝 À retenir

```text
  git init                     → créer un dépôt
  git status                   → où en suis-je ? (à taper sans modération)
  git add <fichier>            → préparer (indexer)
  git commit -m "message"      → enregistrer
  git diff / git diff --staged → voir les modifications
  git log --oneline            → voir l'historique
  .gitignore                   → ce que Git doit ignorer
```

---

⬅️ Précédent : [Module 01 — Comprendre Git](01-comprendre-git.md) · ➡️ Suite : [Module 03 — Historique et retour arrière](03-historique-et-annulation.md)
