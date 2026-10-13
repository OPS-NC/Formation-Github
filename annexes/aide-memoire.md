# 📋 Aide-mémoire Git & GitHub

> À imprimer ou garder ouvert à côté du terminal. 🖨️

---

## ⚙️ Configuration

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "id+pseudo@users.noreply.github.com"
git config --global init.defaultBranch main
git config --global core.autocrlf true          # Windows
git config --global core.editor "code --wait"
git config --global pull.rebase false
git config --global fetch.prune true
git config --global --list
```

## 🐣 Démarrer

| Commande | Effet |
|---|---|
| `git init` | Créer un dépôt dans le dossier courant |
| `git clone <url> [dossier]` | Copier un dépôt existant |

## 💾 Le quotidien

| Commande | Effet |
|---|---|
| `git status` / `git status -s` | État des fichiers 🧭 |
| `git add <fichier>` / `git add .` | Indexer |
| `git add -p` | Indexer morceau par morceau |
| `git commit -m "message"` | Enregistrer |
| `git commit -am "message"` | `add` des fichiers suivis + `commit` |
| `git diff` | Modifs non indexées |
| `git diff --staged` | Modifs indexées |
| `git log --oneline --graph --all` | Historique en graphe |
| `git show <sha>` | Détail d'un commit |

## ⏪ Annuler

| Situation | Commande |
|---|---|
| Jeter les modifs d'un fichier | `git restore <fichier>` |
| Désindexer | `git restore --staged <fichier>` |
| Corriger le dernier commit (non poussé) | `git commit --amend` |
| Annuler un commit (poussé) | `git revert <sha>` |
| Reculer la branche, garder les modifs | `git reset <sha>` (`--soft` : modifs indexées) |
| Reculer la branche, tout jeter ⚠️ | `git reset --hard <sha>` |
| Retrouver un commit « perdu » | `git reflog` |
| Récupérer un fichier d'une ancienne version | `git restore --source=<sha> <fichier>` |

## 🌿 Branches

| Commande | Effet |
|---|---|
| `git branch` / `git branch -a` | Lister (locales / toutes) |
| `git switch -c <branche>` | Créer et basculer |
| `git switch <branche>` | Basculer |
| `git merge <branche>` | Fusionner `<branche>` **dans** la branche courante |
| `git merge --abort` | Annuler une fusion en conflit |
| `git rebase <branche>` | Rejouer ses commits au-dessus de `<branche>` |
| `git branch -d <branche>` | Supprimer (`-D` pour forcer) |
| `git cherry-pick <sha>` | Copier un commit sur la branche courante |

**Conflit** : éditer le fichier → retirer `<<<<<<<` `=======` `>>>>>>>` → `git add` → `git commit`

## 🧺 Stash

| Commande | Effet |
|---|---|
| `git stash push -m "msg"` | Mettre de côté (`-u` : inclure les non suivis) |
| `git stash list` | Lister |
| `git stash pop` | Réappliquer et retirer |
| `git stash apply stash@{n}` | Réappliquer en gardant |
| `git stash drop stash@{n}` | Supprimer |

## 🌐 Distants

| Commande | Effet |
|---|---|
| `git remote -v` | Lister les distants |
| `git remote add <nom> <url>` | Ajouter (`origin`, `upstream`…) |
| `git remote set-url origin <url>` | Changer l'URL (HTTPS ↔ SSH) |
| `git fetch [distant]` | Télécharger sans intégrer |
| `git pull` | `fetch` + `merge` |
| `git push` | Envoyer |
| `git push -u origin <branche>` | Publier une nouvelle branche |
| `git push origin --delete <branche>` | Supprimer une branche distante |
| `git fetch --prune` | Nettoyer les branches distantes supprimées |

## 🏷️ Tags

| Commande | Effet |
|---|---|
| `git tag -a v1.0.0 -m "msg"` | Créer un tag annoté |
| `git tag` | Lister |
| `git push origin v1.0.0` / `--tags` | Pousser un tag / tous |
| `git tag -d v1.0.0` + `git push origin --delete v1.0.0` | Supprimer |

## 🍴 Fork

```bash
git clone git@github.com:moi/projet.git
git remote add upstream https://github.com/auteur/projet.git
git switch -c feature/x            # ... commits ...
git push -u origin feature/x       # puis PR sur GitHub
# Synchroniser le fork :
git switch main && git fetch upstream && git merge upstream/main && git push origin main
```

## 🔐 SSH

```bash
ssh-keygen -t ed25519 -C "email"
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
clip < ~/.ssh/id_ed25519.pub       # puis GitHub > Settings > SSH and GPG keys
ssh -T git@github.com
```

## 🧰 Divers

| Commande | Effet |
|---|---|
| `git blame <fichier>` | Qui a modifié chaque ligne |
| `git grep <motif>` | Chercher dans les fichiers suivis |
| `git log -S "texte"` | Commits ayant ajouté/retiré ce texte |
| `git clean -n` / `-f` | Lister / supprimer les fichiers non suivis |
| `git archive -o v1.zip v1.0.0` | Archive propre d'une version |

---

## 🔄 Le cycle GitHub Flow

```text
git switch main && git pull
git switch -c feature/ma-fonctionnalite
# ... coder, git add, git commit (souvent) ...
git push -u origin feature/ma-fonctionnalite
# → Pull Request sur GitHub → revue → corrections (git push) → Merge
git switch main && git pull
git branch -d feature/ma-fonctionnalite
```

---

🏠 [Retour au sommaire](../README.md)
