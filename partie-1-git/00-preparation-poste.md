# 🛠️ Module 00 — Préparation du poste

> ⏱️ **Durée** : 15 min\
> 🎯 **Objectif** : avoir un poste Windows prêt, un terminal maîtrisé et un Git configuré à votre nom.

---

## 1. 🖥️ Quel terminal utiliser ?

Git for Windows installe plusieurs choses, dont **Git Bash** : un terminal qui accepte les commandes Unix (`ls`, `cd`, `cat`, `touch`, `mkdir -p`…). C'est le terminal utilisé dans toute la formation.

| Terminal | Utilisable ? | Remarque |
|---|---|---|
| **Git Bash** | ✅ Recommandé | Commandes identiques à Linux/macOS, documentation en ligne directement applicable |
| PowerShell | ✅ Oui | Git fonctionne, mais certaines commandes shell diffèrent |
| Invite de commandes (`cmd`) | ⚠️ Possible | À éviter, peu confortable |

👉 Ouvrez Git Bash : menu Démarrer → tapez **Git Bash**, ou clic droit dans un dossier → **Open Git Bash here** (sous Windows 11 : *Afficher d'autres options*).

### Petit rappel de survie dans Git Bash

| Commande | Effet |
|---|---|
| `pwd` | Affiche le dossier courant |
| `ls` / `ls -la` | Liste les fichiers (`-a` montre aussi les fichiers cachés) |
| `cd dossier` / `cd ..` / `cd ~` | Se déplacer / remonter / aller dans son dossier personnel |
| `mkdir -p a/b` | Créer des dossiers |
| `cat fichier` | Afficher un fichier |
| `code .` / `notepad fichier` | Ouvrir dans VS Code / dans le Bloc-notes |
| `clear` ou `Ctrl+L` | Nettoyer l'écran |
| `↑` / `Tab` | Historique des commandes / autocomplétion |

💡 **Astuce** : dans Git Bash, `~` correspond à `C:\Users\<votre-nom>`. Le disque `C:` s'écrit `/c/`.

💡 **Copier / coller** dans Git Bash : `Ctrl+Insert` / `Shift+Insert`, ou clic droit.

---

## 2. ✅ Vérifier les installations

```bash
git --version
python --version
```

✅ **Vérification** : vous obtenez quelque chose comme `git version 2.4x.x.windows.1` et `Python 3.1x.x`.

⚠️ **Attention — Python sous Windows** :
- Avec l'installateur de python.org, la commande est `python` (ou le lanceur `py`). `python3` n'existe généralement **pas**.
- Si `python` ouvre le **Microsoft Store**, c'est un « alias d'exécution » de Windows : *Paramètres → Applications → Paramètres d'applications avancés → Alias d'exécution d'application* → désactivez `python.exe` et `python3.exe`.
- Dans la suite, on écrit `python`. Remplacez par `py` si c'est ce qui fonctionne chez vous.

---

## 3. 🪪 Configurer son identité

Chaque commit enregistre **un nom et un e-mail**. Git refuse de committer tant qu'ils ne sont pas définis.

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "vous@exemple.com"
```

💡 Au module 07, on remplacera éventuellement cet e-mail par l'adresse *noreply* fournie par GitHub pour ne pas exposer votre adresse personnelle.

---

## 4. ⚙️ Réglages recommandés

```bash
# Nom de la branche par défaut des nouveaux dépôts
git config --global init.defaultBranch main

# Fins de ligne : Windows (CRLF) dans vos fichiers, Unix (LF) dans le dépôt
git config --global core.autocrlf true

# Éditeur utilisé par Git pour les messages de commit
# -> Si vous avez VS Code :
git config --global core.editor "code --wait"
# -> Sinon, le Bloc-notes :
# git config --global core.editor "notepad"

# Couleurs dans le terminal
git config --global color.ui auto

# git pull fusionne (comportement le plus simple pour débuter)
git config --global pull.rebase false
```

⚠️ **Attention — l'éditeur par défaut est Vim**. Si vous vous retrouvez un jour dans un écran étrange après un `git commit` sans `-m` :
tapez `Échap`, puis `:wq` puis `Entrée` pour valider (ou `:q!` pour abandonner).

### Vérifier la configuration

```bash
git config --global --list
```

Toute cette configuration est stockée dans le fichier `~/.gitconfig` (`C:\Users\<vous>\.gitconfig`) :

```bash
cat ~/.gitconfig
```

💡 Les trois niveaux de configuration :

| Niveau | Option | Portée | Fichier |
|---|---|---|---|
| Système | `--system` | Tous les utilisateurs du poste | `C:\Program Files\Git\etc\gitconfig` |
| Global | `--global` | Votre utilisateur | `~/.gitconfig` |
| Local | `--local` (défaut) | Un seul dépôt | `.git/config` |

Le niveau le plus précis l'emporte : une config `--local` écrase une config `--global`.

---

## 5. 📁 Préparer l'espace de travail

```bash
mkdir -p ~/formation
cd ~/formation
pwd
```

✅ **Vérification** : `pwd` affiche `/c/Users/<vous>/formation`.

---

## 🆘 Obtenir de l'aide

```bash
git help commit        # ouvre la documentation complète dans le navigateur
git commit -h          # résumé rapide des options dans le terminal
```

📚 La référence : le livre [**Pro Git**](https://git-scm.com/book/fr/v2), gratuit et en français.

---

➡️ Suite : [Module 01 — Comprendre Git](01-comprendre-git.md)
