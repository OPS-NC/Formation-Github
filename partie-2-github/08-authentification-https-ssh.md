# 🔑 Module 08 — Authentification HTTPS et SSH

> ⏱️ **Durée** : 30 min\
> 🎯 **Objectif** : comprendre les deux façons de s'authentifier auprès de GitHub et configurer les deux, dont la génération d'une clé SSH.

---

## 1. 🤔 Pourquoi s'authentifier ?

Lire un dépôt **public** ne demande rien. En revanche, pour **pousser** du code, ou lire un dépôt **privé**, GitHub doit savoir qui vous êtes.

⚠️ Depuis 2021, GitHub **n'accepte plus le mot de passe du compte** pour les opérations Git. Il faut passer par l'une de ces deux méthodes :

| | 🌐 HTTPS | 🔐 SSH |
|---|---|---|
| URL du dépôt | `https://github.com/pseudo/todo-app.git` | `git@github.com:pseudo/todo-app.git` |
| Preuve d'identité | Jeton (token) — géré automatiquement par *Git Credential Manager* | Paire de clés publique / privée |
| Mise en place | ✅ Rien à faire, fenêtre de connexion au 1er push | 🔧 Générer une clé et la déclarer sur GitHub |
| Port réseau | 443 (passe partout) | 22 (parfois bloqué en entreprise, contournable) |
| Usage typique | Débutants, postes d'entreprise avec proxy | Développeurs réguliers, serveurs, plusieurs comptes |

Les deux sont sûres et on peut changer à tout moment. On va configurer **les deux**.

---

## 2. 🌐 HTTPS avec Git Credential Manager

### Le fonctionnement

Git for Windows embarque **Git Credential Manager (GCM)**. Au premier `push` vers GitHub en HTTPS :

1. Une fenêtre *« Connect to GitHub »* s'ouvre → **Sign in with your browser**
2. Le navigateur demande d'autoriser *Git Credential Manager* → **Authorize**
3. GCM reçoit un jeton et le **stocke dans le Gestionnaire d'identification Windows**
4. Les push suivants sont automatiques, sans rien redemander

Vérifiez que GCM est bien configuré :

```bash
git config --show-origin --get-all credential.helper
```

```text
file:C:/Program Files/Git/etc/gitconfig    manager
```

✅ `manager` = Git Credential Manager. On le verra en action au module 09.

### Où sont stockés les identifiants ?

*Panneau de configuration → Gestionnaire d'identification → Informations d'identification Windows* → entrée `git:https://github.com`.

💡 Si vous changez de compte GitHub ou que l'authentification échoue en boucle : supprimez cette entrée, le prochain push redemandera la connexion.

### Alternative : le Personal Access Token (PAT)

Sur un poste sans navigateur (serveur) ou pour un script, on peut créer un jeton manuellement :

1. *Settings → Developer settings → Personal access tokens → **Fine-grained tokens** → Generate new token*
2. Nom, **date d'expiration** (obligatoire, gardez-la courte)
3. *Repository access* : uniquement les dépôts nécessaires
4. *Permissions → Repository permissions → **Contents : Read and write***
5. **Generate token** et copiez-le immédiatement (il ne sera plus jamais affiché)

Lors d'un push, à la demande de mot de passe, collez le **token** à la place du mot de passe.

⚠️ Un token est un **mot de passe** : ne le committez jamais, ne l'envoyez pas par mail ou chat. Si un token fuite : *Developer settings → révoquez-le*.

---

## 3. 🔐 SSH : le principe des clés

SSH repose sur une **paire de clés** :

| Clé | Fichier | Rôle | Partage |
|---|---|---|---|
| 🔑 **Privée** | `~/.ssh/id_ed25519` | Prouve votre identité | ⛔ **Ne quitte JAMAIS votre poste** |
| 🔓 **Publique** | `~/.ssh/id_ed25519.pub` | Permet de vérifier la preuve | ✅ Se donne librement (à GitHub, à un serveur…) |

```text
        Votre poste                                GitHub
  ┌──────────────────────┐                ┌─────────────────────────┐
  │ clé privée (secrète) │ ── signature ─►│ clé publique enregistrée│
  │                      │                │ dans Settings > SSH keys│
  └──────────────────────┘                │ -> "c'est bien vous" ✅  │
                                          └─────────────────────────┘
```

GitHub envoie un défi, votre poste le signe avec la clé privée, GitHub vérifie la signature avec la clé publique. La clé privée ne circule **jamais** sur le réseau. 🛡️

💡 Analogie : la clé publique est un **cadenas** que vous distribuez, la clé privée est la **seule clé** qui l'ouvre.

---

## 4. 🛠️ TP — Générer et déclarer une clé SSH

### 4.1 Vérifier l'existant

```bash
ls -la ~/.ssh
```

Si des fichiers `id_ed25519` et `id_ed25519.pub` existent déjà, vous avez déjà une clé : passez à l'étape 4.3. Sinon (ou *No such file or directory*), on en crée une.

### 4.2 Générer la paire de clés

```bash
ssh-keygen -t ed25519 -C "vous@exemple.com"
```

| Option | Signification |
|---|---|
| `-t ed25519` | Algorithme moderne, clé courte et très sûre (recommandé par GitHub) |
| `-C "..."` | Commentaire pour identifier la clé (souvent l'e-mail ou `prenom@poste`) |

```text
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/vous/.ssh/id_ed25519):   ← Entrée
Enter passphrase (empty for no passphrase):                            ← phrase secrète
Enter same passphrase again:
Your identification has been saved in /c/Users/vous/.ssh/id_ed25519
Your public key has been saved in /c/Users/vous/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:AbCdEf... vous@exemple.com
```

🔒 **La passphrase** chiffre la clé privée sur le disque : si quelqu'un vole le fichier, il ne peut pas l'utiliser. **Fortement recommandée.** (Rien ne s'affiche quand vous tapez : c'est normal.)

```bash
ls -la ~/.ssh
cat ~/.ssh/id_ed25519.pub
```

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... vous@exemple.com
```

### 4.3 Charger la clé dans l'agent SSH

L'**agent SSH** garde la clé déverrouillée en mémoire : vous tapez la passphrase **une fois par session** au lieu d'à chaque push.

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-add -l          # liste les clés chargées
```

🏆 **Bonus — démarrer l'agent automatiquement** à l'ouverture de Git Bash : ajoutez ceci à la fin du fichier `~/.bashrc` (créez-le s'il n'existe pas : `code ~/.bashrc`) :

```bash
env=~/.ssh/agent.env

agent_load_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent_load_env

# agent_run_state: 0=agent avec clé, 1=agent sans clé, 2=pas d'agent
agent_run_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if [ ! "$SSH_AUTH_SOCK" ] || [ $agent_run_state = 2 ]; then
    agent_start
    ssh-add
elif [ "$SSH_AUTH_SOCK" ] && [ $agent_run_state = 1 ]; then
    ssh-add
fi

unset env
```

*(Script issu de la [documentation officielle GitHub](https://docs.github.com/fr/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases).)*

### 4.4 Déclarer la clé publique sur GitHub

Copiez la clé **publique** dans le presse-papiers :

```bash
clip < ~/.ssh/id_ed25519.pub
```

⚠️ Bien le fichier **`.pub`** ! La clé privée ne se copie jamais nulle part.

Sur GitHub :

1. *Settings → **SSH and GPG keys** → **New SSH key***
2. **Title** : un nom qui identifie le poste, ex. `PC-bureau-Windows`
3. **Key type** : *Authentication Key*
4. **Key** : collez (`Ctrl+V`)
5. **Add SSH key** (GitHub peut redemander une confirmation 2FA)

💡 Bonne pratique : **une clé par poste**. Si un PC est perdu ou volé, on supprime uniquement sa clé sur GitHub.

### 4.5 Tester la connexion

```bash
ssh -T git@github.com
```

La première fois, SSH ne connaît pas encore le serveur :

```text
The authenticity of host 'github.com (140.82.121.4)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

🛡️ Vérifiez que l'empreinte correspond à celle publiée par GitHub : [empreintes SSH officielles](https://docs.github.com/fr/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints). C'est la protection contre un serveur imposteur. Si elle correspond, tapez `yes`.

```text
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
Hi pseudo! You've successfully authenticated, but GitHub does not provide shell access.
```

✅ **Vérification** : le message `Hi <votre pseudo>!` confirme que tout fonctionne. 🎉

L'empreinte du serveur est désormais mémorisée dans `~/.ssh/known_hosts`.

---

## 5. 🧱 Port 22 bloqué ? Passer par le port 443

Sur certains réseaux d'entreprise, le port 22 est bloqué (`ssh: connect to host github.com port 22: Connection timed out`). GitHub accepte SSH sur le port 443 :

```bash
ssh -T -p 443 git@ssh.github.com
```

Si cela fonctionne, rendez-le permanent dans `~/.ssh/config` :

```text
Host github.com
    Hostname ssh.github.com
    Port 443
    User git
```

---

## 6. 🔄 Passer d'HTTPS à SSH (et inversement)

L'URL d'un dépôt distant se change à tout moment :

```bash
git remote -v
git remote set-url origin git@github.com:pseudo/todo-app.git       # vers SSH
git remote set-url origin https://github.com/pseudo/todo-app.git   # vers HTTPS
```

On le pratique au module suivant.

---

## 🏆 Bonus

### Deux comptes GitHub sur le même poste (perso + pro)

Générez une seconde clé (`ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_pro -C "pro"`) puis dans `~/.ssh/config` :

```text
Host github.com
    IdentityFile ~/.ssh/id_ed25519

Host github-pro
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_pro
```

Clonez ensuite les dépôts pro avec `git clone git@github-pro:entreprise/projet.git`.

### Signer ses commits avec sa clé SSH ✍️

GitHub affiche un badge **Verified** sur les commits signés, preuve qu'ils viennent bien de vous :

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

Puis sur GitHub : *Settings → SSH and GPG keys → New SSH key* avec **Key type : Signing Key** (la même clé publique).

---

## 📝 À retenir

```text
  HTTPS : rien à configurer, Git Credential Manager ouvre le navigateur au 1er push
  SSH   : ssh-keygen -t ed25519 -C "email"     → générer
          ssh-add ~/.ssh/id_ed25519             → charger dans l'agent
          clip < ~/.ssh/id_ed25519.pub          → copier la clé PUBLIQUE
          GitHub > Settings > SSH and GPG keys  → déclarer
          ssh -T git@github.com                 → tester
  git remote set-url origin <url>               → changer de protocole
```

🔑 La clé **privée** ne quitte jamais votre poste. Une clé par machine.

---

⬅️ Précédent : [Module 07 — Découvrir GitHub](07-decouvrir-github.md) · ➡️ Suite : [Module 09 — Publier son code](09-remote-push-pull.md)
