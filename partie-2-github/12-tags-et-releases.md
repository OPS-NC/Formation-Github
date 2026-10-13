# 🏷️ Module 12 — Tags et Releases

> ⏱️ **Durée** : 15 min\
> 🎯 **Objectif** : marquer les versions de son projet avec des tags et les publier sous forme de releases GitHub.

---

## 1. 🤔 Tag ou branche ?

| | 🌿 Branche | 🏷️ Tag |
|---|---|---|
| Nature | Étiquette **mobile** | Étiquette **fixe** |
| Avance avec les commits ? | ✅ Oui | ❌ Jamais |
| Usage | Développer | Marquer une version livrée : `v1.0.0` |

Un tag répond à la question : *« quel code exactement a été livré en version 1.0.0 ? »*

---

## 2. 🔢 Le versionnage sémantique (SemVer)

Convention quasi universelle : **`MAJEUR.MINEUR.CORRECTIF`** ([semver.org](https://semver.org/lang/fr/))

| Partie | On l'incrémente quand… | Exemple |
|---|---|---|
| **MAJEUR** | Changement **incompatible** (les utilisateurs doivent s'adapter) | `1.4.2` → `2.0.0` |
| **MINEUR** | Nouvelle **fonctionnalité** rétrocompatible | `1.4.2` → `1.5.0` |
| **CORRECTIF** | **Correction de bug** rétrocompatible | `1.4.2` → `1.4.3` |

Versions `0.x.y` : phase de développement initial, tout peut changer. Préfixe `v` d'usage pour les tags : `v1.0.0`.

---

## 3. 🏷️ Créer des tags

Deux types de tags :

| Type | Commande | Contient |
|---|---|---|
| Léger (*lightweight*) | `git tag v1.0.0` | Juste un pointeur vers un commit |
| **Annoté** ✅ | `git tag -a v1.0.0 -m "message"` | Auteur, date, message (un vrai objet Git) |

👉 Pour les versions, utilisez **toujours des tags annotés**.

🧪 **TP** :

```bash
cd ~/formation/todo-app
git switch main
git pull
```

Mettez à jour le `CHANGELOG.md` :

```markdown
# Historique des versions

## v1.0.0

- Commandes : ajouter, lister, terminer, supprimer, vider, rechercher, modifier, aide
- Publication sur GitHub
```

```bash
git commit -am "Changelog de la version 1.0.0"
git push
```

⚠️ Si le ruleset du module 10 est actif, ce push est refusé : passez par une branche + PR, ou désactivez temporairement le ruleset.

```bash
git tag -a v1.0.0 -m "Version 1.0.0 : première version publique"
git tag                       # liste les tags
git show v1.0.0               # infos du tag + commit pointé
git log --oneline -3          # (tag: v1.0.0) apparaît à côté du commit
```

Taguer **après coup** un ancien commit :

```bash
git log --oneline             # repérez le commit "Ajout de la commande terminer"
git tag -a v0.1.0 <sha> -m "Version 0.1.0 : premier prototype"
git log --oneline --decorate
```

---

## 4. ⬆️ Pousser les tags

⚠️ `git push` **n'envoie pas** les tags ! Il faut les pousser explicitement :

```bash
git push origin v1.0.0        # un tag précis
git push origin --tags        # tous les tags locaux
```

💡 `git config --global push.followTags true` : les tags **annotés** accessibles depuis les commits poussés partent automatiquement avec `git push`.

Sur GitHub : sélecteur de branche → onglet **Tags**, ou page `https://github.com/vous/todo-app/tags`.

---

## 5. 🔧 Manipuler les tags

```bash
git switch --detach v0.1.0    # voir le code de la version 0.1.0 (detached HEAD)
python todo.py aide
git switch main

git tag -l "v1.*"             # filtrer
git tag -d v0.1.0             # supprimer en local
git push origin --delete v0.1.0   # supprimer sur GitHub
```

⚠️ Ne déplacez pas et ne supprimez pas un tag de version **déjà publié** : d'autres s'en servent comme référence. En cas d'erreur, publiez plutôt une nouvelle version (`v1.0.1`).

---

## 6. 📦 Les Releases GitHub

Une **release** est une page GitHub construite **sur un tag**, destinée aux **utilisateurs** : notes de version lisibles et fichiers à télécharger.

🧪 **TP** :

1. Sur la page du dépôt, colonne de droite : **Releases → Create a new release** (ou *Draft a new release*)
2. **Choose a tag** → `v1.0.0`
3. **Release title** : `v1.0.0 — Première version publique 🎉`
4. Cliquez sur **Generate release notes** : GitHub liste automatiquement les PR fusionnées et les contributeurs depuis la release précédente 🪄
5. Complétez la description :

```markdown
## ✨ Nouveautés
- Gestion complète des tâches en ligne de commande

## 📥 Installation
Téléchargez l'archive `todo-app-v1.0.0.zip` ci-dessous, puis :

    python todo.py aide
```

6. **Attach binaries** : glissez un fichier à distribuer. Créons-le :

```bash
git archive --format=zip --prefix=todo-app/ -o ~/formation/todo-app-v1.0.0.zip v1.0.0
```

   `git archive` produit une archive **propre** du code au tag `v1.0.0` (sans `.git`, sans `taches.json`). Glissez `todo-app-v1.0.0.zip` dans la zone de dépôt.

7. Laissez coché **Set as the latest release** → **Publish release**

✅ La release apparaît sur la page d'accueil du dépôt. GitHub y ajoute automatiquement les archives **Source code (zip)** et **(tar.gz)**.

| Option | Usage |
|---|---|
| **Set as a pre-release** | Version de test (`v2.0.0-beta.1`), non proposée comme « dernière version » |
| **Save draft** | Préparer la release sans la publier |

💡 Le lien `https://github.com/vous/todo-app/releases/latest` pointe **toujours** vers la dernière release : pratique dans une documentation.

🏆 **Bonus — GitHub CLI** : `gh release create v1.0.1 --generate-notes todo-app.zip`

---

## 🧪 Exercice

1. Corrigez un petit détail (ex. : un message d'aide) via une branche + PR
2. Fusionnez, mettez à jour `main` en local
3. Créez le tag annoté `v1.0.1` (c'est un correctif !), poussez-le
4. Publiez la release `v1.0.1` avec **Generate release notes** : seule votre PR apparaît

---

## 📝 À retenir

```text
  git tag -a v1.2.0 -m "msg"     → créer un tag annoté
  git push origin v1.2.0         → pousser un tag (pas automatique !)
  git push origin --tags         → pousser tous les tags
  git tag -d / git push origin --delete <tag>  → supprimer
  SemVer : MAJEUR (incompatible) . MINEUR (fonctionnalité) . CORRECTIF (bug)
  Release GitHub = tag + notes de version + fichiers téléchargeables
```

---

⬅️ Précédent : [Module 11 — Fork et collaboration](11-fork-et-collaboration.md) · ➡️ Suite : [Module 13 — Tour des fonctionnalités GitHub](13-fonctionnalites-github.md)
