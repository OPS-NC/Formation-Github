# 🌿 Module 04 — Branches et fusions

> ⏱️ **Durée** : 35 min
> 🎯 **Objectif** : développer en parallèle sur des branches, les fusionner et résoudre un conflit.

---

## 1. 🤔 Une branche, c'est quoi ?

Une branche est simplement une **étiquette mobile** qui pointe vers un commit. Quand vous committez, l'étiquette de la branche courante avance automatiquement.

`HEAD` est une autre étiquette qui indique **sur quelle branche vous êtes**.

```text
                         HEAD
                          │
                          ▼
                        main
                          │
                          ▼
  A ◄─── B ◄─── C ◄───── D
```

Créer une branche ne copie **aucun fichier** : c'est instantané et ne coûte rien. On en crée donc pour tout : une fonctionnalité, une correction, une expérimentation.

```text
                        main
                          │
                          ▼
  A ◄─── B ◄─── C ◄───── D
                          ▲
                          └──── E ◄─── F
                                       ▲
                                feature/terminer  ◄── HEAD
```

Pendant ce temps, `main` reste stable et intacte. 🛡️

---

## 2. 🔧 Les commandes de base

| Commande | Effet |
|---|---|
| `git branch` | Lister les branches locales (`*` = branche courante) |
| `git branch -a` | Lister aussi les branches distantes |
| `git branch <nom>` | Créer une branche (sans s'y déplacer) |
| `git switch <nom>` | Se déplacer sur une branche |
| `git switch -c <nom>` | Créer **et** se déplacer |
| `git branch -d <nom>` | Supprimer une branche fusionnée |
| `git branch -D <nom>` | Forcer la suppression (⚠️ commits non fusionnés perdus) |
| `git branch -m <ancien> <nouveau>` | Renommer |

💡 Dans les tutos anciens : `git checkout <nom>` = `git switch <nom>`, et `git checkout -b <nom>` = `git switch -c <nom>`.

📛 **Nommage** : des conventions fréquentes sont `feature/…`, `fix/…`, `docs/…`. Pas d'espaces ni d'accents.

---

## 3. ⏩ TP 1 — Fusion « fast-forward »

On veut ajouter une commande `terminer` pour cocher une tâche.

```bash
git switch -c feature/terminer
git branch
```

```text
* feature/terminer
  main
```

Dans `todo.py`, ajoutez cette fonction après `lister` :

```python
def terminer(numero):
    taches = charger()
    taches[numero - 1]["faite"] = True
    sauvegarder(taches)
    print(f"Tâche {numero} terminée.")
```

Dans `main()`, ajoutez la commande juste avant le `elif commande == "aide":` :

```python
    elif commande == "terminer":
        terminer(int(sys.argv[2]))
```

Et mettez à jour la ligne des commandes dans `afficher_aide()` :

```python
    print("Commandes : ajouter <texte>, lister, terminer <numéro>, aide")
```

```bash
python todo.py lister
python todo.py terminer 1
python todo.py lister
git commit -am "Ajout de la commande terminer"
```

Retournons sur `main` :

```bash
git switch main
cat todo.py | grep terminer      # rien : sur main, la fonctionnalité n'existe pas encore
```

🤯 Le contenu du dossier change selon la branche. Fusionnons :

```bash
git merge feature/terminer
```

```text
Updating 4d1a0f3..8e2b7c1
Fast-forward
 todo.py | 8 ++++++++
```

**Fast-forward** : `main` n'avait pas bougé depuis la création de la branche, Git a simplement avancé l'étiquette `main`. Aucun commit de fusion n'est nécessaire.

```text
Avant :  A ◄─ B ◄─ C            ◄── main
                    ◄─ E        ◄── feature/terminer

Après :  A ◄─ B ◄─ C ◄─ E       ◄── main, feature/terminer
```

La branche a fini son travail, on la supprime :

```bash
git branch -d feature/terminer
```

---

## 4. 🔀 TP 2 — Fusion à trois points (*merge commit*)

Cette fois, `main` va évoluer **pendant** le développement de la branche.

```bash
git switch -c feature/supprimer
```

Ajoutez la fonction après `terminer` :

```python
def supprimer(numero):
    taches = charger()
    tache = taches.pop(numero - 1)
    sauvegarder(taches)
    print(f"Tâche supprimée : {tache['texte']}")
```

Dans `main()`, juste avant `elif commande == "aide":` :

```python
    elif commande == "supprimer":
        supprimer(int(sys.argv[2]))
```

Et dans `afficher_aide()` :

```python
    print("Commandes : ajouter <texte>, lister, terminer <numéro>, supprimer <numéro>, aide")
```

```bash
git commit -am "Ajout de la commande supprimer"
```

Pendant ce temps, sur `main`, quelqu'un complète la documentation :

```bash
git switch main
```

Ajoutez à la fin de `README.md` :

```markdown
## Prérequis

- Python 3.10 ou supérieur
```

```bash
git commit -am "Ajout des prérequis dans le README"
```

L'historique a maintenant **divergé** :

```bash
git log --oneline --graph --all
```

```text
* 5c6d7e8 (HEAD -> main) Ajout des prérequis dans le README
| * 3a4b5c6 (feature/supprimer) Ajout de la commande supprimer
|/
* 8e2b7c1 Ajout de la commande terminer
```

Fusionnons :

```bash
git merge feature/supprimer
```

Git ouvre votre éditeur avec un message par défaut `Merge branch 'feature/supprimer'` : enregistrez et fermez l'éditeur. (Pour éviter l'éditeur : `git merge feature/supprimer --no-edit`.)

```bash
git log --oneline --graph --all
```

```text
*   9f8e7d6 (HEAD -> main) Merge branch 'feature/supprimer'
|\
| * 3a4b5c6 (feature/supprimer) Ajout de la commande supprimer
* | 5c6d7e8 Ajout des prérequis dans le README
|/
* 8e2b7c1 Ajout de la commande terminer
```

Git a créé un **commit de fusion** qui a **deux parents**. Les modifications touchaient des fichiers différents : la fusion est automatique. ✅

```bash
git branch -d feature/supprimer
```

---

## 5. 💥 TP 3 — Le conflit

Un conflit survient quand **deux branches modifient les mêmes lignes** d'un même fichier. Git ne peut pas deviner quelle version garder : il vous demande de trancher. C'est **normal**, pas une erreur.

Provoquons-en un :

```bash
git switch -c feature/message-vide
```

Dans `lister()`, remplacez `print("Aucune tâche.")` par :

```python
        print("Aucune tâche, profitez-en !")
```

```bash
git commit -am "Message plus sympathique quand la liste est vide"
git switch main
```

Sur `main`, remplacez **la même ligne** par :

```python
        print("Rien à faire pour le moment.")
```

```bash
git commit -am "Message neutre quand la liste est vide"
git merge feature/message-vide
```

```text
Auto-merging todo.py
CONFLICT (content): Merge conflict in todo.py
Automatic merge failed; fix conflicts and then commit the result.
```

```bash
git status
```

```text
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
        both modified:   todo.py
```

Ouvrez `todo.py` :

```python
    if not taches:
<<<<<<< HEAD
        print("Rien à faire pour le moment.")
=======
        print("Aucune tâche, profitez-en !")
>>>>>>> feature/message-vide
```

| Marqueur | Signification |
|---|---|
| `<<<<<<< HEAD` | Début de **votre** version (branche courante : `main`) |
| `=======` | Séparateur |
| `>>>>>>> feature/message-vide` | Fin de la version de la branche fusionnée |

### Résoudre

1. Éditez le fichier pour obtenir le résultat voulu (l'une, l'autre, ou un mélange) **et supprimez les 3 marqueurs** :

```python
    if not taches:
        print("Aucune tâche pour le moment, profitez-en !")
```

2. Vérifiez que le programme fonctionne : `python todo.py lister`
3. Indiquez à Git que le conflit est résolu, puis terminez la fusion :

```bash
git add todo.py
git commit --no-edit
git log --oneline --graph -5
git branch -d feature/message-vide
```

🎉 Conflit résolu !

💡 **VS Code** détecte les conflits et propose des boutons *Accept Current Change / Accept Incoming Change / Accept Both* : très pratique.

🆘 Vous êtes perdu au milieu d'un conflit ? `git merge --abort` remet tout comme avant la fusion.

---

## 6. 📏 `git rebase` — l'alternative à `merge` (aperçu)

`rebase` **rejoue** les commits d'une branche au-dessus d'une autre, pour obtenir un historique **linéaire**, sans commit de fusion.

```text
Avant :              C ◄─ D           ◄── feature
                    /
          A ◄─ B ◄─ E                 ◄── main

git switch feature
git rebase main

Après :   A ◄─ B ◄─ E ◄─ C' ◄─ D'     ◄── feature
```

`C'` et `D'` sont de **nouveaux** commits (nouveaux SHA) avec les mêmes modifications. Ensuite, `git switch main && git merge feature` sera un simple fast-forward.

| | `merge` | `rebase` |
|---|---|---|
| Historique | Fidèle, avec commits de fusion | Linéaire, plus lisible |
| Réécrit l'historique ? | ❌ Non | ⚠️ Oui |
| Pour débuter | ✅ Recommandé | Avec prudence |

⚠️ **Règle d'or du rebase** : ne jamais rebaser des commits déjà poussés et utilisés par d'autres.

🏆 **Bonus** : créez une branche `feature/compter`, faites 2 commits, faites 1 commit sur `main`, puis `git switch feature/compter && git rebase main` et observez `git log --oneline --graph --all`.

---

## 📝 À retenir

```text
  git switch -c <branche>   → créer et aller sur une branche
  git switch <branche>      → changer de branche
  git merge <branche>       → fusionner <branche> DANS la branche courante
  git branch -d <branche>   → supprimer une branche fusionnée
  git log --oneline --graph --all → visualiser les branches
  Conflit : éditer → retirer les marqueurs → git add → git commit
  git merge --abort         → annuler une fusion en cours
```

💡 On **fusionne vers soi** : pour intégrer `feature` dans `main`, on se place **sur `main`** puis `git merge feature`.

---

⬅️ Précédent : [Module 03 — Historique et retour arrière](03-historique-et-annulation.md) · ➡️ Suite : [Module 05 — Stash et boîte à outils](05-stash-et-outils.md)
