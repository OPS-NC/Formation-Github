# 🧺 Module 05 — Stash et boîte à outils

> ⏱️ **Durée** : 15 min
> 🎯 **Objectif** : mettre du travail en cours de côté, et découvrir quelques commandes qui font gagner du temps.

---

## 1. 🧺 `git stash` — mettre de côté

### Le scénario classique

Vous êtes en plein développement, rien n'est prêt à être committé… et on vous demande une **correction urgente** sur `main`. Problème : on ne peut pas changer de branche proprement avec des modifications en cours, et on ne veut pas committer un travail à moitié fini.

👉 `git stash` range vos modifications non committées dans une **pile** et remet le répertoire de travail propre.

```text
  Répertoire modifié ──git stash──► 🧺 pile de stash
  Répertoire propre  ◄─git stash pop── 🧺
```

### 🧪 TP

On commence une fonctionnalité : une commande `vider` pour supprimer les tâches terminées.

```bash
git switch -c feature/vider
```

Ajoutez dans `todo.py`, après `supprimer`, le début du travail (non terminé) :

```python
def vider():
    taches = charger()
    # TODO : ne garder que les tâches non faites
```

🚨 Urgence : le chef signale que `terminer 99` fait planter l'application ! Il faut corriger sur `main` **tout de suite**.

```bash
git status                  # todo.py modifié
git stash push -m "WIP commande vider"
git status                  # working tree clean ✨
git stash list
```

```text
stash@{0}: On feature/vider: WIP commande vider
```

On part corriger :

```bash
git switch main
git switch -c fix/numero-invalide
```

Remplacez la fonction `terminer` :

```python
def terminer(numero):
    taches = charger()
    if numero < 1 or numero > len(taches):
        print(f"Numéro invalide : {numero}")
        return
    taches[numero - 1]["faite"] = True
    sauvegarder(taches)
    print(f"Tâche {numero} terminée.")
```

```bash
python todo.py terminer 99         # Numéro invalide : 99
git commit -am "Correction du crash avec un numéro de tâche invalide"
git switch main
git merge fix/numero-invalide
git branch -d fix/numero-invalide
```

Correction livrée ✅. On reprend notre travail là où on l'avait laissé :

```bash
git switch feature/vider
git merge main                     # on récupère la correction dans notre branche
git stash pop
git status                         # nos modifications sont revenues 🎉
```

Terminez la fonction :

```python
def vider():
    taches = charger()
    restantes = [t for t in taches if not t["faite"]]
    sauvegarder(restantes)
    print(f"{len(taches) - len(restantes)} tâche(s) terminée(s) supprimée(s).")
```

Ajoutez `vider` dans `main()` (avant `aide`) et dans `afficher_aide()`, testez, committez puis fusionnez dans `main` :

```python
    elif commande == "vider":
        vider()
```

```python
    print("Commandes : ajouter <texte>, lister, terminer <numéro>, supprimer <numéro>, vider, aide")
```

```bash
git commit -am "Ajout de la commande vider"
git switch main
git merge feature/vider
git branch -d feature/vider
```

### Les commandes du stash

| Commande | Effet |
|---|---|
| `git stash` | Met de côté les fichiers **suivis** modifiés |
| `git stash push -m "msg"` | Idem avec une description (recommandé) |
| `git stash -u` | Inclut aussi les fichiers **non suivis** |
| `git stash list` | Voir la pile |
| `git stash show -p stash@{0}` | Voir le contenu d'un stash |
| `git stash pop` | Réappliquer le dernier stash **et** le retirer de la pile |
| `git stash apply stash@{1}` | Réappliquer un stash **en le gardant** dans la pile |
| `git stash drop stash@{0}` | Supprimer un stash |
| `git stash clear` | ⚠️ Vider toute la pile |

⚠️ Le stash est un **outil de dépannage temporaire**, pas un lieu de stockage. Un stash oublié depuis 3 semaines est un stash perdu. Pour du travail à garder, faites plutôt un commit sur une branche.

---

## 2. 🏷️ Alias — vos propres raccourcis

```bash
git config --global alias.st "status -s"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.unstage "restore --staged"
```

```bash
git st
git lg
```

---

## 3. 🕵️ `git blame` — qui a écrit cette ligne ?

```bash
git blame todo.py
```

```text
1a7f6d5c (Prénom Nom 2026-10-13 09:20:11 +1100  1) """Gestionnaire de tâches en ligne de commande."""
8e2b7c19 (Prénom Nom 2026-10-13 10:05:42 +1100 42) def terminer(numero):
```

Pour chaque ligne : le commit, l'auteur et la date de la dernière modification. Ensuite `git show <sha>` pour comprendre **pourquoi**.

💡 Malgré son nom, `blame` sert surtout à **comprendre le contexte** d'une ligne, pas à accuser quelqu'un 😇.

```bash
git blame -L 40,50 todo.py      # seulement les lignes 40 à 50
```

---

## 4. 🔎 `git grep` — chercher dans le code suivi

```bash
git grep "charger"              # toutes les occurrences dans les fichiers suivis
git grep -n "def "              # avec les numéros de ligne
git grep "TODO" HEAD~5          # chercher dans une ancienne version
```

---

## 5. 🧽 `git clean` — nettoyer les fichiers non suivis

```bash
touch a.tmp b.log
git clean -n          # simulation : liste ce qui SERAIT supprimé
git clean -f          # supprime les fichiers non suivis (hors fichiers ignorés)
git clean -fdx        # ⚠️ supprime aussi les dossiers et les fichiers IGNORÉS (taches.json !)
```

⚠️ Toujours lancer `git clean -n` avant `-f` : les fichiers supprimés ne sont pas dans Git, donc **irrécupérables**.

---

## 📝 À retenir

```text
  git stash push -m "msg"   → mettre de côté
  git stash list / pop      → lister / reprendre
  git config alias.xx "…"   → créer un raccourci
  git blame <fichier>       → origine de chaque ligne
  git grep <motif>          → chercher dans le code suivi
  git clean -n puis -f      → supprimer les fichiers non suivis
```

---

⬅️ Précédent : [Module 04 — Branches et fusions](04-branches-et-fusions.md) · ➡️ Suite : [Module 06 — Cloner et synchroniser en local](06-clone-et-depots-distants-locaux.md)
