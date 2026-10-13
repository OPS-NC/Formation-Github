# 🖱️ Module 14 — Outils graphiques sous Windows

> ⏱️ **Durée** : 20 min\
> 🎯 **Objectif** : découvrir deux outils graphiques **100 % gratuits** pour travailler avec Git et GitHub, et savoir quand les préférer au terminal.

---

## 1. 🤔 Terminal ou interface graphique ?

Les deux ! Ce n'est pas l'un **ou** l'autre :

| 💻 Terminal | 🖱️ Interface graphique |
|---|---|
| Toutes les fonctionnalités | Les fonctionnalités courantes |
| Identique partout (serveurs, CI, docs, collègues) | Spécifique à l'outil |
| Scriptable | Visuel : graphe des branches, diff côte à côte |
| Indispensable pour dépanner | Confortable au quotidien, idéal pour indexer ligne par ligne |

👉 Ce que vous avez appris en ligne de commande vous permet de **comprendre** ce que fait l'interface graphique. L'inverse n'est pas vrai.

---

## 2. 🔍 Le choix des outils

Critère imposé : **gratuit, sans version payante**. Panorama des outils Windows :

| Outil | Licence | Verdict |
|---|---|---|
| **GitHub Desktop** | Open source (MIT), éditeur GitHub | ✅ **Retenu** — le plus simple, intégré à GitHub |
| **Git Extensions** | Open source (GPL), communautaire, Windows | ✅ **Retenu** — l'outil visuel Git le plus complet en gratuit |
| TortoiseGit | Open source (GPL) | ✅ Alternative : s'intègre au clic droit de l'Explorateur (familier pour les ex-utilisateurs de TortoiseSVN) |
| VS Code (Source Control) + extension *Git Graph* | Gratuit | ✅ Alternative pour ceux qui codent déjà dans VS Code |
| `gitk` / `git gui` | Fournis avec Git for Windows | ✅ Toujours disponibles, mais austères |
| Sourcetree | Gratuit mais propriétaire, compte Atlassian obligatoire | ❌ Écarté |
| GitKraken | *Freemium* (dépôts privés payants) | ❌ Écarté |
| Fork, SmartGit, Tower | Payants (ou gratuits en usage non commercial seulement) | ❌ Écartés |

Notre recommandation :

- 🐙 **GitHub Desktop** pour débuter et pour tout ce qui touche **GitHub** (clone, push, PR)
- 🧩 **Git Extensions** comme **outil visuel Git** complet (graphe, historique, stash, rebase interactif, blame)

---

## 3. 🐙 GitHub Desktop

### Installation

1. Téléchargez sur [desktop.github.com](https://desktop.github.com)
2. Installez (installation par utilisateur, pas besoin de droits administrateur en général)
3. Au lancement : **Sign in to GitHub.com** → autorisation dans le navigateur
4. *Configure Git* : vérifiez nom et e-mail (reprend votre `~/.gitconfig`)

### Prise en main

```text
┌──────────────────────────────────────────────────────────────────────┐
│ Current repository ▾ │ Current branch ▾ │ Fetch origin / Push origin  │
├────────────────────────┬─────────────────────────────────────────────┤
│ Changes │ History      │                                              │
│ ☑ todo.py              │   diff du fichier sélectionné                │
│ ☐ README.md            │   + lignes ajoutées en vert                   │
│                        │   - lignes supprimées en rouge                │
│ [Summary (required)]   │                                              │
│ [Description        ]  │                                              │
│ [Commit to feature/x]  │                                              │
└────────────────────────┴─────────────────────────────────────────────┘
```

| Action | Où | Équivalent Git |
|---|---|---|
| Ajouter un dépôt existant | *File → Add local repository* → `~/formation/todo-app` | — |
| Cloner | *File → Clone repository* → onglet GitHub.com | `git clone` |
| Choisir quoi committer | Cases ☑ dans **Changes** (et clic sur les lignes du diff !) | `git add` / `git add -p` |
| Committer | *Summary* + **Commit to …** | `git commit -m` |
| Synchroniser | **Fetch origin** → **Pull origin** / **Push origin** | `git fetch` / `pull` / `push` |
| Branches | **Current branch → New branch** | `git switch -c` |
| Fusionner | *Branch → Merge into current branch* | `git merge` |
| Mettre de côté | Clic droit sur *Changes* → **Stash all changes** | `git stash` |
| Annuler un commit | **History** → clic droit → *Revert changes in commit* | `git revert` |
| Ouvrir une PR | **Preview Pull Request / Create Pull Request** | — |
| Ouvrir le terminal | *Repository → Open in Command Prompt / Git Bash* | — |

💡 Dans *File → Options → Advanced*, choisissez **Git Bash** comme shell et votre éditeur (VS Code…).

### 🧪 TP — Un cycle complet sans le terminal

1. **Add local repository** → `~/formation/todo-app`
2. **Current branch → New branch** → `docs/desktop`
3. Dans votre éditeur, ajoutez au `README.md` une section `## Outils` → revenez dans Desktop : le diff s'affiche
4. Faites aussi une modification dans `todo.py` mais **ne la cochez pas** ☐
5. Summary : `Mention des outils graphiques` → **Commit to docs/desktop**
6. **Publish branch** (= `git push -u origin docs/desktop`)
7. **Create Pull Request** → le navigateur s'ouvre sur GitHub → créez et fusionnez la PR
8. Dans Desktop : **Current branch → main** → **Fetch origin** → **Pull origin**
9. La modification non cochée de `todo.py` ? Elle vous a suivi. Clic droit → **Discard changes** (= `git restore`)

---

## 4. 🧩 Git Extensions

### Installation

1. Prérequis : **Git for Windows** (déjà installé ✅) et le **.NET Desktop Runtime** (l'installateur indique la version requise et le lien si elle manque)
2. Téléchargez la dernière version stable sur [gitextensions.github.io](https://gitextensions.github.io) (ou les *Releases* de [github.com/gitextensions/gitextensions](https://github.com/gitextensions/gitextensions))
3. Au premier lancement, l'assistant **Settings** vérifie la configuration : tout doit être vert ✅ (chemin de Git, nom, e-mail). Choisissez la langue de l'interface si vous le souhaitez.

### Prise en main

*Open repository* → `~/formation/todo-app`

```text
┌──────────────────────────────────────────────────────────────────────┐
│ Commit │ Pull ▾ │ Push │ Stash ▾ │ Branche: main ▾ │ 🔍 filtre       │
├──────────────────────────────────────────────────────────────────────┤
│ ●  (main, origin/main, v1.0.1) Changelog ...                          │
│ ●─┐ Merge pull request #5 ...                                         │
│ │ ● Mention des outils graphiques                                     │
│ ●─┘ ...                                                              │
│ ● (v1.0.0) ...                     ← LE graphe de tout l'historique   │
├──────────────────────────────────────────────────────────────────────┤
│ Commit │ Diff │ File tree │ ...    ← détails du commit sélectionné    │
└──────────────────────────────────────────────────────────────────────┘
```

| Fonction | Comment |
|---|---|
| 🌳 **Graphe** de toutes les branches, tags, distants | Fenêtre principale (= `git log --graph --all`) |
| ✅ **Committer** en choisissant fichier par fichier, voire **ligne par ligne** | Bouton **Commit** → *Stage* / *Stage selected lines* |
| 🔀 Créer, fusionner, rebaser des branches | Clic droit sur un commit du graphe |
| 🏷️ Créer un tag | Clic droit sur un commit → *Create new tag* |
| 🧺 Stash | Menu **Stash** (liste, aperçu, apply, drop) |
| 🕵️ Historique et **blame** d'un fichier | Onglet *File tree* → clic droit → *File history* / *Blame* |
| 💥 Conflits | Assistant de résolution avec outil de fusion à 3 volets |
| ✂️ Rebase interactif, cherry-pick, reset | Clic droit sur un commit |
| 🖱️ Intégration Explorateur | Clic droit sur un dossier → *Git Extensions* |
| 🖥️ Console intégrée | Onglet *Console* (Git Bash) |

### 🧪 TP — Explorer son historique

1. Ouvrez `todo-app` : retrouvez visuellement les merges des modules 04 et 10, les tags `v1.0.0` / `v1.0.1`, le commit venu du binôme
2. Sélectionnez `todo.py` dans *File tree* → **Blame** : qui a écrit la fonction `modifier` ?
3. Modifiez deux lignes éloignées de `todo.py` → **Commit** → indexez **une seule** des deux lignes (*Stage selected lines*) → committez
4. Mettez l'autre modification en **stash** depuis le menu, puis ré-appliquez-la, puis annulez-la (*Reset file changes*)
5. Clic droit sur un ancien commit → **Create new tag** `v0.1.0` → observez-le dans le graphe

---

## 5. 🆚 VS Code, pour mémoire

Si vous utilisez VS Code, il intègre déjà Git (icône **Source Control**, `Ctrl+Shift+G`) : indexation, commit, push, branches, et un excellent **éditeur de conflits** (module 04). L'extension gratuite **Git Graph** ajoute le graphe de l'historique.

---

## 📝 À retenir

- 🐙 **GitHub Desktop** : simple, parfait pour le cycle *commit → push → PR*
- 🧩 **Git Extensions** : graphe complet et toutes les opérations Git avancées, gratuitement
- 💻 Le **terminal** reste la référence : en cas de problème, c'est là qu'on comprend et qu'on répare

---

## 🎓 Fin de la formation

Bravo ! 🎉 Vous savez maintenant :

- 🟠 Versionner un projet avec Git, naviguer dans l'historique, annuler, brancher, fusionner, résoudre des conflits
- 🔵 Publier sur GitHub en HTTPS ou SSH, travailler en Pull Requests, contribuer via un fork, livrer des releases

📚 **Pour aller plus loin** :
- 📖 [Pro Git](https://git-scm.com/book/fr/v2) — le livre de référence, gratuit et en français
- 🎮 [Learn Git Branching](https://learngitbranching.js.org/?locale=fr_FR) — exercices interactifs sur les branches et le rebase
- 🎓 [GitHub Skills](https://skills.github.com) — cours interactifs officiels (dont **GitHub Actions**, la suite logique de cette formation)
- 📋 [Aide-mémoire](../annexes/aide-memoire.md) de cette formation

---

⬅️ Précédent : [Module 13 — Tour des fonctionnalités GitHub](13-fonctionnalites-github.md) · 🏠 [Retour au sommaire](../README.md)
