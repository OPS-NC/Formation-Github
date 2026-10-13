# 📖 Glossaire

| Terme | Définition |
|---|---|
| **Bare (dépôt nu)** | Dépôt sans répertoire de travail, uniquement le contenu de `.git`. Utilisé côté serveur. |
| **Blame** | Affiche, pour chaque ligne d'un fichier, le dernier commit et l'auteur qui l'a modifiée. |
| **Branche** | Étiquette mobile pointant vers un commit, qui avance à chaque nouveau commit. |
| **Checkout** | Ancienne commande à tout faire, remplacée par `switch` (branches) et `restore` (fichiers). |
| **Cherry-pick** | Copier un commit précis sur la branche courante. |
| **Clone** | Copie complète d'un dépôt, historique compris. |
| **Collaborateur** | Personne ayant les droits d'écriture sur un dépôt GitHub. |
| **Commit** | Instantané du projet, avec auteur, date, message et parent(s), identifié par un SHA. |
| **Conflit** | Situation où Git ne peut pas fusionner automatiquement deux modifications des mêmes lignes. |
| **Detached HEAD** | `HEAD` pointe sur un commit et non sur une branche. |
| **Diff** | Différences entre deux versions de fichiers. |
| **Fast-forward** | Fusion où la branche cible avance simplement, sans commit de fusion. |
| **Fetch** | Télécharger les nouveautés d'un distant sans les intégrer. |
| **Fork** | Copie d'un dépôt GitHub sur son propre compte, pour y contribuer sans droits sur l'original. |
| **GCM (Git Credential Manager)** | Outil qui gère l'authentification HTTPS et stocke les identifiants. |
| **Gist** | Extrait de code partagé sur gist.github.com, versionné comme un mini-dépôt. |
| **GitHub Flow** | Workflow : branche → commits → Pull Request → revue → fusion dans `main`. |
| **`.gitignore`** | Fichier listant les fichiers que Git doit ignorer. |
| **HEAD** | Pointeur vers la position actuelle (en général, la branche courante). |
| **Index (staging area)** | Zone de préparation : ce qui partira dans le prochain commit. |
| **Issue** | Ticket GitHub : bug, idée, tâche. |
| **Merge** | Fusion de l'historique d'une branche dans une autre. |
| **Merge commit** | Commit de fusion, avec deux parents. |
| **`main` / `master`** | Nom conventionnel de la branche principale (`master` dans les anciens dépôts). |
| **Origin** | Nom conventionnel du distant d'où l'on a cloné (ou son propre fork). |
| **PAT (Personal Access Token)** | Jeton remplaçant le mot de passe pour l'authentification HTTPS. |
| **Pull** | `fetch` + `merge` : récupérer et intégrer les nouveautés d'un distant. |
| **Pull Request (PR)** | Demande de fusion d'une branche dans une autre, avec revue et discussion. |
| **Push** | Envoyer ses commits vers un distant. |
| **Rebase** | Rejouer des commits au-dessus d'une autre base, pour un historique linéaire. Réécrit l'historique. |
| **Reflog** | Journal local des déplacements de `HEAD` : permet de retrouver des commits « perdus ». |
| **Release** | Page GitHub associée à un tag : notes de version et fichiers téléchargeables. |
| **Remote (distant)** | Autre copie du dépôt (GitHub, serveur…) avec laquelle on se synchronise. |
| **Remote-tracking branch** | Branche locale en lecture seule (`origin/main`) reflétant l'état du distant au dernier `fetch`. |
| **Repository (dépôt)** | Projet versionné : fichiers + historique complet (dossier `.git`). |
| **Reset** | Déplacer la branche courante vers un autre commit. |
| **Revert** | Créer un commit qui annule les effets d'un commit précédent. |
| **Review** | Relecture de code dans une Pull Request. |
| **Ruleset** | Règles GitHub protégeant des branches (PR obligatoire, pas de force push…). |
| **SemVer** | Versionnage sémantique `MAJEUR.MINEUR.CORRECTIF`. |
| **SHA** | Empreinte unique (40 caractères hexadécimaux) identifiant un commit. |
| **SSH** | Protocole d'authentification par paire de clés publique / privée. |
| **Squash** | Regrouper plusieurs commits en un seul. |
| **Stash** | Pile où mettre temporairement de côté des modifications non committées. |
| **Tag** | Étiquette fixe sur un commit, typiquement pour marquer une version. |
| **Upstream** | Dépôt d'origine d'un fork (par convention) ; plus généralement, la branche distante suivie. |
| **Working directory** | Répertoire de travail : les fichiers tels que vous les éditez. |

---

🏠 [Retour au sommaire](../README.md)
