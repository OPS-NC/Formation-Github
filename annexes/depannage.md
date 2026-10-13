# 🚑 Dépannage — erreurs fréquentes

> Premier réflexe, toujours : **lire le message d'erreur** (Git donne souvent la solution dans ses lignes `hint:`) et taper `git status`. 🧭

---

## 🖥️ Poste et terminal

### 🐍 `python` ouvre le Microsoft Store / `python3: command not found`

Sous Windows, la commande est `python` ou `py`. Désactivez les alias du Store : *Paramètres → Applications → Paramètres d'applications avancés → Alias d'exécution d'application* → décochez `python.exe` et `python3.exe`.

### 🔤 Les accents s'affichent mal dans la sortie Python

Ex. : `TÃ¢che ajoutÃ©e`. Le terminal et Python n'utilisent pas le même encodage. Forcez Python en UTF-8 :

```bash
echo 'export PYTHONUTF8=1' >> ~/.bashrc
source ~/.bashrc
```

Sous PowerShell : `[Environment]::SetEnvironmentVariable("PYTHONUTF8", "1", "User")` puis rouvrez le terminal.

### 📝 Je suis coincé dans un éditeur bizarre après `git commit` / `git merge`

C'est **Vim**. `Échap` puis `:wq` + `Entrée` pour valider, `:q!` + `Entrée` pour abandonner. Puis configurez un autre éditeur : `git config --global core.editor "code --wait"`.

### 📜 Le terminal affiche `:` en bas et n'affiche plus rien

C'est le visualiseur (*pager*) de `git log` / `git diff`. Flèches pour défiler, **`q`** pour quitter.

### ⚠️ `warning: LF will be replaced by CRLF`

Simple information sur la conversion des fins de ligne Windows/Unix (`core.autocrlf true`). Sans gravité.

---

## 📁 Dépôt local

### `fatal: not a git repository (or any of the parent directories): .git`

Vous n'êtes pas dans un dépôt. Vérifiez avec `pwd` et faites `cd` vers le bon dossier.

### `Author identity unknown` / `Please tell me who you are`

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "vous@exemple.com"
```

### `nothing added to commit but untracked files present`

Les nouveaux fichiers doivent être ajoutés avec `git add` (`git commit -a` ne prend que les fichiers déjà suivis).

### Mon fichier est dans `.gitignore` mais Git le suit toujours

Il était déjà committé. Retirez-le du suivi sans le supprimer :

```bash
git rm --cached fichier
git commit -m "Arrêt du suivi de fichier"
```

### `You are in 'detached HEAD' state`

Vous êtes sur un commit précis, pas sur une branche. `git switch main` pour revenir. Si vous avez committé dans cet état et voulez garder le travail : `git switch -c nouvelle-branche`.

### `error: Your local changes to the following files would be overwritten by checkout/merge`

Vous avez des modifications non committées qui entrent en conflit. Au choix : les committer, les mettre de côté (`git stash`), ou les jeter (`git restore <fichier>`).

### `CONFLICT (content): Merge conflict in ...`

Normal ! Voir [module 04](../partie-1-git/04-branches-et-fusions.md#5--tp-3--le-conflit). Éditer → retirer les marqueurs → `git add` → `git commit`. Ou `git merge --abort`.

### `error: the branch 'x' is not fully merged`

`git branch -d` refuse de supprimer une branche dont les commits ne sont pas dans la branche courante. Si la PR a été fusionnée en *squash* ou si vous êtes sûr : `git branch -D x`.

### J'ai fait un `reset --hard` / supprimé une branche par erreur 😱

```bash
git reflog                       # trouvez l'état voulu
git reset --hard HEAD@{n}        # ou : git switch -c sauvetage <sha>
```

---

## 🌐 GitHub et distants

### `! [rejected] main -> main (fetch first)` / `(non-fast-forward)`

Le distant a des commits que vous n'avez pas. `git pull`, résolvez les éventuels conflits, puis `git push`.

### `fatal: refusing to merge unrelated histories`

Le dépôt GitHub a été créé **avec** un README/licence, et votre dépôt local a son propre historique. Solution :

```bash
git pull origin main --allow-unrelated-histories
# résoudre les conflits éventuels (souvent README.md), puis :
git push -u origin main
```

### `remote: Support for password authentication was removed`

Le mot de passe GitHub n'est plus accepté. Utilisez Git Credential Manager (navigateur), un **token** à la place du mot de passe, ou SSH ([module 08](../partie-2-github/08-authentification-https-ssh.md)).

### `remote: Permission to x/y.git denied to pseudo` / `403`

- Vous n'avez pas les droits d'écriture : forkez, ou demandez à être collaborateur
- Mauvais compte mémorisé : *Gestionnaire d'identification Windows* → supprimez `git:https://github.com` → nouveau push

### `git@github.com: Permission denied (publickey)`

La clé SSH n'est pas reconnue :

```bash
ls ~/.ssh                        # la clé existe ?
ssh-add -l                       # est-elle chargée ? sinon : ssh-add ~/.ssh/id_ed25519
ssh -vT git@github.com           # mode verbeux pour voir quelle clé est proposée
```

Vérifiez que la clé **publique** est bien dans *GitHub → Settings → SSH and GPG keys*.

### `ssh: connect to host github.com port 22: Connection timed out`

Port 22 bloqué par le réseau : passez par le port 443 ([module 08, §5](../partie-2-github/08-authentification-https-ssh.md#5--port-22-bloqué--passer-par-le-port-443)).

### `remote: error: GH007: Your push would publish a private email address`

Vos commits contiennent votre e-mail personnel alors que l'option *Block command line pushes that expose my email* est active. Configurez l'adresse noreply (`git config --global user.email "id+pseudo@users.noreply.github.com"`), puis pour le dernier commit : `git commit --amend --reset-author --no-edit`. Pour des commits plus anciens, le plus simple pendant la formation est de décocher temporairement l'option.

### `remote: error: GH013: Repository rule violations found`

Un ruleset protège la branche (PR obligatoire…). Passez par une branche et une Pull Request.

### `remote: error: GH013 ... Push cannot contain secrets`

La *push protection* a détecté un secret (clé d'API, token) dans vos commits. **Retirez-le** des commits concernés (`git commit --amend` ou `git reset` puis recommit), et **révoquez** le secret par précaution.

### Mon tag n'apparaît pas sur GitHub

Les tags ne partent pas avec `git push`. `git push origin <tag>` ou `git push origin --tags`.

---

🏠 [Retour au sommaire](../README.md)
