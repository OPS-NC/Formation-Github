# 🎓 Formation Git & GitHub — de zéro à la collaboration

> **Durée** : 6 heures (≈ 3h Git + ≈ 3h GitHub)\
> **Public** : informaticiens qui n'ont jamais utilisé Git ni GitHub\
> **Format** : 100 % pratique, chaque notion est appliquée immédiatement dans un TP fil rouge

---

## 🎯 Objectifs

À la fin de la formation, vous saurez :

- ✅ Expliquer ce qu'est un gestionnaire de versions et pourquoi Git s'est imposé
- ✅ Créer un dépôt, suivre l'évolution d'un projet avec `status`, `add`, `commit`, `log`, `diff`
- ✅ Revenir en arrière sans paniquer (`restore`, `revert`, `reset`, `reflog`)
- ✅ Travailler avec des branches, fusionner et résoudre des conflits
- ✅ Mettre du travail de côté avec `stash`
- ✅ Cloner un dépôt et synchroniser plusieurs copies (`push`, `fetch`, `pull`)
- ✅ Vous authentifier sur GitHub en HTTPS **et** en SSH (génération de clés)
- ✅ Publier du code sur GitHub, ouvrir et relire des Pull Requests
- ✅ Contribuer au projet d'un autre via un **fork** et garder ce fork à jour
- ✅ Versionner vos livraisons avec des **tags** et des **releases**
- ✅ Utiliser les outils graphiques gratuits sous Windows (GitHub Desktop, Git Extensions)

> ℹ️ GitHub Actions (CI/CD) n'est **pas** abordé dans cette formation.

---

## 🧰 Prérequis

| Élément | Détail |
|---|---|
| 💻 Poste | Windows 10 / 11 |
| 🔧 Git | Git for Windows installé (fournit **Git Bash**) |
| 🐍 Python | Python 3 installé |
| 🌐 Compte | Un compte GitHub personnel |
| 👥 Binôme | La partie *fork* (module 11) se fait à deux |

Le module [00 — Préparation du poste](partie-1-git/00-preparation-poste.md) vérifie tout cela.

---

## 🗺️ Programme

### 🟠 Partie 1 — Git en local (≈ 3h)

| # | Module | Durée | Contenu |
|---|---|---|---|
| 00 | [Préparation du poste](partie-1-git/00-preparation-poste.md) | 15 min | Vérifications, configuration de Git, terminal |
| 01 | [Comprendre Git](partie-1-git/01-comprendre-git.md) | 20 min | Versionner, les 3 zones, commits, SHA |
| 02 | [TP — Premier dépôt](partie-1-git/02-premier-depot.md) | 45 min | `init`, `status`, `add`, `commit`, `diff`, `log`, `.gitignore` |
| 03 | [Historique et retour arrière](partie-1-git/03-historique-et-annulation.md) | 30 min | `log`, `show`, `restore`, `amend`, `revert`, `reset`, `reflog` |
| 04 | [Branches et fusions](partie-1-git/04-branches-et-fusions.md) | 35 min | `branch`, `switch`, `merge`, conflits, `rebase` |
| 05 | [Stash et boîte à outils](partie-1-git/05-stash-et-outils.md) | 15 min | `stash`, alias, `blame`, `grep` |
| 06 | [Cloner et synchroniser en local](partie-1-git/06-clone-et-depots-distants-locaux.md) | 25 min | `clone`, dépôt *bare*, `push`, `fetch`, `pull` |

☕ *Pause de 10 min conseillée après le module 03.*

### 🔵 Partie 2 — GitHub (≈ 3h)

| # | Module | Durée | Contenu |
|---|---|---|---|
| 07 | [Découvrir GitHub](partie-2-github/07-decouvrir-github.md) | 15 min | Interface, 2FA, e-mail privé, créer un dépôt |
| 08 | [Authentification HTTPS et SSH](partie-2-github/08-authentification-https-ssh.md) | 30 min | Credential Manager, tokens, clés SSH ed25519 |
| 09 | [Publier son code](partie-2-github/09-remote-push-pull.md) | 20 min | `remote add`, `push -u`, `pull`, édition web |
| 10 | [Branches et Pull Requests](partie-2-github/10-branches-et-pull-requests.md) | 35 min | GitHub Flow, issues, review, stratégies de merge |
| 11 | [Fork et collaboration en binôme](partie-2-github/11-fork-et-collaboration.md) | 40 min | Fork, `upstream`, PR croisées, synchroniser un fork |
| 12 | [Tags et Releases](partie-2-github/12-tags-et-releases.md) | 15 min | Tags annotés, SemVer, releases GitHub |
| 13 | [Tour des fonctionnalités GitHub](partie-2-github/13-fonctionnalites-github.md) | 15 min | Issues, Projects, Wiki, Gists, README de profil, sécurité |
| 14 | [Outils graphiques sous Windows](partie-2-github/14-outils-graphiques-windows.md) | 20 min | GitHub Desktop, Git Extensions, VS Code |

☕ *Pause de 10 min conseillée après le module 10.*

### 📎 Annexes

- 📋 [Aide-mémoire des commandes](annexes/aide-memoire.md)
- 🚑 [Dépannage — erreurs fréquentes](annexes/depannage.md)
- 📖 [Glossaire](annexes/glossaire.md)

---

## 🧵 Le fil rouge : l'application `todo`

Tout au long de la formation, on construit **une petite application Python en ligne de commande** de gestion de tâches :

```text
$ python todo.py ajouter "Apprendre Git"
Tâche ajoutée : Apprendre Git

$ python todo.py lister
1. [ ] Apprendre Git
```

L'application est volontairement simple : l'objectif n'est **pas** le Python, mais d'avoir du vrai code à versionner, à faire évoluer sur des branches, à faire entrer en conflit, à publier et à partager.

---

## 📐 Conventions utilisées

| Symbole | Signification |
|---|---|
| 🧪 **TP** | Exercice pratique à réaliser |
| 💡 **Astuce** | Bon à savoir |
| ⚠️ **Attention** | Piège fréquent ou commande dangereuse |
| ✅ **Vérification** | Ce que vous devez obtenir |
| 🏆 **Bonus** | Pour ceux qui ont de l'avance |

Les commandes sont écrites pour **Git Bash** (recommandé). Quand la commande diffère sous PowerShell, c'est précisé.

```bash
# Une ligne qui commence par # est un commentaire : ne la tapez pas
git status
```

---

## 👩‍🏫 Notes pour le formateur

- Constituer les **binômes** dès le début : ils serviront au module 11 (fork) et pour les relectures de PR.
- Les dépôts `todo-app` des stagiaires sur GitHub doivent être **publics** pour simplifier les forks croisés (un dépôt privé ne peut être forké que par quelqu'un qui y a accès).
- Si un stagiaire est seul, il peut forker [`octocat/Spoon-Knife`](https://github.com/octocat/Spoon-Knife), dépôt d'entraînement officiel de GitHub.
- Prévoir que l'installation des outils graphiques (module 14) puisse nécessiter les droits administrateur selon la politique du poste : à anticiper avec le support.
