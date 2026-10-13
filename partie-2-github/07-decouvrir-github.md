# 🐙 Module 07 — Découvrir GitHub

> ⏱️ **Durée** : 15 min\
> 🎯 **Objectif** : prendre ses repères dans l'interface, sécuriser son compte et créer son premier dépôt distant.

---

## 1. 🌐 GitHub en deux mots

GitHub héberge des dépôts Git et ajoute **tout ce qu'il faut pour collaborer** autour :

| Fonction | À quoi ça sert |
|---|---|
| 📦 Dépôts (*repositories*) | Héberger le code, publics ou privés |
| 🔀 Pull Requests | Proposer, relire et discuter des modifications avant fusion |
| 🐛 Issues | Suivre bugs, idées et tâches |
| 🍴 Forks | Copier le dépôt de quelqu'un pour y contribuer |
| 🏷️ Releases | Publier des versions téléchargeables |
| 📋 Projects | Tableaux de suivi type Kanban |
| 🔒 Sécurité | Alertes de dépendances vulnérables, détection de secrets |
| ⚙️ Actions | Automatisation CI/CD *(hors périmètre de cette formation)* |

Pour une personne, un compte gratuit permet des dépôts publics **et privés** illimités. Les **organisations** regroupent les dépôts d'une équipe ou d'une entreprise (comme `github.com/ops-nc`).

---

## 2. 🧭 Tour de l'interface

Connectez-vous sur [github.com](https://github.com) et repérez :

- 🏠 **Dashboard** (logo en haut à gauche) : activité récente, vos dépôts
- 👤 **Votre profil** (avatar en haut à droite → *Your profile*) : vos dépôts, votre graphe de contributions 🟩
- ⚙️ **Settings** (avatar → *Settings*) : réglages du **compte**
- ➕ (en haut à droite) : créer un dépôt, importer un dépôt, créer un gist…
- 🔍 Barre de recherche : chercher du code, des dépôts, des utilisateurs sur tout GitHub

💡 Appuyez sur `?` sur n'importe quelle page GitHub : la liste des raccourcis clavier s'affiche.

### Anatomie d'une page de dépôt

Ouvrez par exemple [github.com/git/git](https://github.com/git/git) :

```text
┌──────────────────────────────────────────────────────────────────┐
│ git / git                    👁 Watch   🍴 Fork   ⭐ Star        │
├──────────────────────────────────────────────────────────────────┤
│ <> Code │ Issues │ Pull requests │ Actions │ Projects │ ... │ ⚙  │
├──────────────────────────────────────────────────────────────────┤
│ [main ▾]  branches  tags      [Go to file]  [ <> Code ▾ ]        │
│                                                                  │
│ 📁 Documentation         dernier commit ...      il y a 2 jours  │
│ 📄 README.md             ...                                     │
│                                                                  │
│ README affiché ici                         │  About / Releases   │
└──────────────────────────────────────────────────────────────────┘
```

| Élément | Rôle |
|---|---|
| **Watch** 👁 | Recevoir les notifications du dépôt |
| **Star** ⭐ | Mettre en favori (et signe de popularité) |
| **Fork** 🍴 | Créer votre propre copie du dépôt (module 11) |
| **Code ▾** (bouton vert) | URL de clonage HTTPS / SSH, téléchargement ZIP |
| Sélecteur de branche | Parcourir le code d'une autre branche ou d'un tag |
| **Commits** (horloge) | Historique, équivalent web de `git log` |

---

## 3. 🔐 Sécuriser son compte

### Authentification à deux facteurs (2FA)

GitHub **impose** la 2FA aux comptes qui contribuent du code. Si ce n'est pas déjà fait :

*Settings → Password and authentication → Two-factor authentication → Enable*

Choisissez une application d'authentification (Microsoft Authenticator, Google Authenticator, 2FAS…) ou une **passkey**.

⚠️ **Téléchargez et conservez les codes de récupération** (*recovery codes*) dans un endroit sûr (gestionnaire de mots de passe). Sans eux, perdre son téléphone = perdre son compte.

### Protéger son adresse e-mail

Chaque commit contient l'e-mail de son auteur, et il devient **public** dès que le commit est poussé sur un dépôt public. GitHub fournit une adresse anonyme :

1. *Settings → Emails*
2. Cochez **Keep my email addresses private**
3. Cochez **Block command line pushes that expose my email**
4. Copiez l'adresse affichée, du type `12345678+pseudo@users.noreply.github.com`

```bash
git config --global user.email "12345678+pseudo@users.noreply.github.com"
```

💡 Les contributions faites avec cette adresse sont bien rattachées à votre profil.

⚠️ Vos commits **déjà faits** gardent l'ancienne adresse. Si l'option 3 est cochée, le push de ces commits sera refusé (erreur `GH007`). Pour la formation, ne cochez l'option 3 **qu'après** le premier push du module 09 (sauf si vous aviez configuré l'adresse noreply dès le module 00).

---

## 4. ➕ Créer le dépôt `todo-app` sur GitHub

1. Cliquez sur **➕ → New repository**
2. Remplissez :

| Champ | Valeur |
|---|---|
| Owner | Votre compte |
| Repository name | `todo-app` |
| Description | `Gestionnaire de tâches en ligne de commande (formation Git)` |
| Visibilité | **Public** 🌍 (nécessaire pour le fork en binôme du module 11) |
| Add a README | ❌ **Non** |
| Add .gitignore | ❌ None |
| Choose a license | ❌ None |

3. **Create repository**

⚠️ **Pourquoi ne rien cocher ?** Notre dépôt local a déjà un historique. Si GitHub crée un commit initial (README, licence…), les deux historiques n'auraient **aucun ancêtre commun** et le premier push serait refusé (`refusing to merge unrelated histories`). On crée donc un dépôt **vide**.

GitHub affiche alors une page d'aide avec les commandes à lancer. Gardez-la ouverte : on s'en sert au module 09, après avoir réglé l'authentification.

### Public ou privé ?

| | 🌍 Public | 🔒 Privé |
|---|---|---|
| Visible par | Tout le monde | Vous et les personnes invitées |
| Forkable par | Tout le monde | Seulement ceux qui y ont accès |
| Usage typique | Open source, portfolio, formation | Code d'entreprise, projets perso |

💡 La visibilité peut être changée plus tard : *Settings du dépôt → General → Danger Zone → Change visibility*.

⚠️ Un dépôt public est **public pour toujours** dans les faits : il a pu être cloné ou indexé. Ne poussez **jamais** de secret (mot de passe, clé d'API, `.env`) dans un dépôt, même privé.

---

## 📝 À retenir

- GitHub = hébergement de dépôts Git + outils de collaboration
- 2FA activée et **codes de récupération** en lieu sûr
- Adresse e-mail `noreply` pour ne pas exposer son e-mail
- Pour pousser un dépôt local existant : créer un dépôt GitHub **vide**

---

⬅️ Précédent : [Module 06 — Cloner et synchroniser en local](../partie-1-git/06-clone-et-depots-distants-locaux.md) · ➡️ Suite : [Module 08 — Authentification HTTPS et SSH](08-authentification-https-ssh.md)
