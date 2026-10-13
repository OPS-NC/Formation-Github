# 🧠 Module 01 — Comprendre Git

> ⏱️ **Durée** : 20 min\
> 🎯 **Objectif** : comprendre *ce que fait* Git avant d'apprendre *comment* s'en servir.

---

## 1. 😱 Le problème

Qui n'a jamais vu ça ?

```text
rapport.docx
rapport_v2.docx
rapport_v2_final.docx
rapport_v2_final_CORRIGE.docx
rapport_v2_final_CORRIGE_vraiment_final.docx
```

Et en équipe c'est pire : *« Qui a modifié ce fichier ? Pourquoi ? Tu m'envoies ta version par mail ? Ah zut, j'ai écrasé tes modifs… »*

Un **gestionnaire de versions** (VCS — *Version Control System*) répond à ces questions :

- 📜 **Historique** : chaque modification est enregistrée avec son auteur, sa date et sa raison
- ⏪ **Retour arrière** : on peut revenir à n'importe quel état passé
- 🌿 **Travail en parallèle** : plusieurs pistes de développement sans se marcher dessus
- 🤝 **Collaboration** : fusionner automatiquement le travail de plusieurs personnes

---

## 2. 🌍 Git et GitHub : ne pas confondre

| | **Git** 🟠 | **GitHub** 🔵 |
|---|---|---|
| C'est quoi ? | Un **logiciel** de gestion de versions | Un **service web** qui héberge des dépôts Git |
| Où ? | Sur votre poste | Sur Internet (github.com) |
| Créé par | Linus Torvalds, 2005 (pour le noyau Linux) | Société GitHub, 2008 (Microsoft depuis 2018) |
| Fonctionne sans l'autre ? | ✅ Oui, entièrement hors ligne | ❌ Non, repose sur Git |
| Alternatives | Mercurial, SVN (ancien) | GitLab, Bitbucket, Gitea, Azure DevOps |

💡 Git est à GitHub ce que le courrier électronique est à Gmail.

---

## 3. 🕸️ Un système **distribué**

Avec les anciens outils (SVN), un serveur central détenait l'historique : pas de serveur, pas de travail.

Avec Git, **chaque copie d'un dépôt contient tout l'historique**. Vous pouvez committer, consulter l'historique, créer des branches… dans l'avion. Le serveur (GitHub) n'est qu'une copie parmi d'autres, choisie par convention comme référence.

```text
            ┌──────────────────┐
            │  GitHub (origin) │   ← copie complète
            └──────┬─────▲─────┘
           pull    │     │   push
        ┌──────────▼─┐ ┌─┴──────────┐
        │ Poste Alice│ │ Poste Bob  │  ← copies complètes
        └────────────┘ └────────────┘
```

---

## 4. 📸 Un commit = une photo du projet

Git ne stocke pas « des différences » au sens où on l'imagine : chaque **commit** est un **instantané** (*snapshot*) complet du projet à un moment donné, accompagné de :

- 👤 l'auteur et la date
- 💬 un message expliquant **pourquoi** la modification a été faite
- 🔗 un lien vers le commit **parent** (le précédent)

Chaque commit est identifié par une empreinte **SHA-1** de 40 caractères, par exemple `a1b2c3d4e5f6...`. On utilise généralement les 7 premiers : `a1b2c3d`.

```text
  a1b2c3d ◄── 4e5f6a7 ◄── 9b8c7d6     ◄── HEAD -> main
  "Création"  "Ajout liste" "Correction bug"
```

💡 L'empreinte est calculée à partir du contenu : modifier un seul octet d'un ancien commit changerait son identifiant et celui de tous ses descendants. C'est ce qui rend l'historique Git **infalsifiable** discrètement.

---

## 5. 🗂️ Les trois zones — LA notion à retenir

C'est le concept qui déroute le plus les débutants. Git distingue trois espaces :

```text
┌───────────────────┐   git add    ┌───────────────────┐  git commit  ┌───────────────────┐
│  Répertoire de    │ ───────────► │  Zone de          │ ───────────► │  Dépôt            │
│  travail          │              │  préparation      │              │  (historique)     │
│  (working dir)    │ ◄─────────── │  (index/staging)  │              │  dossier .git     │
└───────────────────┘ git restore  └───────────────────┘              └───────────────────┘
```

| Zone | Rôle | Analogie 📦 |
|---|---|---|
| **Répertoire de travail** | Vos fichiers, tels que vous les modifiez | Les objets éparpillés sur votre bureau |
| **Index** (*staging area*) | Ce qui fera partie du **prochain** commit | Le carton que vous remplissez |
| **Dépôt** (`.git`) | L'historique de tous les commits | Les cartons fermés, étiquetés et rangés |

👉 Pourquoi une zone intermédiaire ? Pour **choisir** ce qu'on enregistre. Vous avez modifié 5 fichiers mais seuls 2 concernent la correction de bug ? Vous n'ajoutez que ces 2-là au commit. Un commit = **une** modification logique.

---

## 6. 🚦 Les états d'un fichier

```text
  Non suivi ──git add──► Indexé ──git commit──► Suivi, non modifié
 (untracked)            (staged)                   (unmodified)
                           ▲                             │
                           │                        vous éditez
                        git add                          │
                           │                             ▼
                           └─────────────────────── Modifié
                                                   (modified)
```

La commande `git status` vous dit **toujours** dans quel état sont vos fichiers. C'est la commande que vous taperez le plus : **en cas de doute, `git status`** 🧭.

---

## 7. 🗃️ Le dossier `.git`

Un dépôt Git, c'est simplement un dossier qui contient un sous-dossier caché `.git`. **Tout** l'historique est dedans.

- Supprimer `.git` = supprimer l'historique (les fichiers actuels restent, mais ne sont plus versionnés)
- Copier le dossier projet (avec `.git`) = copier tout l'historique
- ⚠️ On ne modifie **jamais** le contenu de `.git` à la main

---

## 📝 À retenir

- **Git** = outil local et distribué, **GitHub** = hébergement en ligne + collaboration
- Un **commit** = un instantané complet + auteur + message + parent, identifié par un SHA
- **3 zones** : répertoire de travail → `add` → index → `commit` → dépôt
- **`git status`** est votre boussole

---

⬅️ Précédent : [Module 00 — Préparation du poste](00-preparation-poste.md) · ➡️ Suite : [Module 02 — TP Premier dépôt](02-premier-depot.md)
