# 🍴 Module 11 — Fork et collaboration en binôme

> ⏱️ **Durée** : 40 min\
> 🎯 **Objectif** : contribuer au dépôt de quelqu'un d'autre **sans avoir de droits dessus**, et garder sa copie à jour.

---

## 1. 🤔 Fork : pourquoi faire ?

Au module 10, votre binôme était **collaborateur** : il pouvait pousser directement dans votre dépôt. Impossible à l'échelle de l'open source : on ne donne pas les droits d'écriture à des inconnus.

Le **fork** résout ce problème : c'est une **copie du dépôt sur votre compte GitHub**. Vous y faites ce que vous voulez, puis vous proposez vos modifications au dépôt d'origine via une **Pull Request**.

```text
                 ┌───────────────────────────┐
                 │  binome/todo-app          │  ← dépôt d'origine = "upstream"
                 │  (vous n'avez pas le droit │     (lecture seule pour vous)
                 │   d'y pousser)             │
                 └──────▲─────────────┬──────┘
           Pull Request │             │ 1. Fork (sur GitHub)
                        │             ▼
                 ┌──────┴───────────────────┐
                 │  vous/todo-app-binome    │  ← votre fork = "origin"
                 │  (vous en êtes maître)   │
                 └──────▲─────────────┬──────┘
                 3. push│             │ 2. clone
                        │             ▼
                 ┌──────┴───────────────────┐
                 │  Votre poste             │
                 └──────────────────────────┘
```

| | 👥 Collaborateur | 🍴 Fork |
|---|---|---|
| Droits nécessaires | Invitation du propriétaire | Aucun (dépôt public) |
| Où pousse-t-on ? | Dans le dépôt d'origine | Dans **sa** copie |
| Usage typique | Équipe interne | Open source, contributeurs externes |
| Intégration | PR (ou push direct si non protégé) | PR obligatoire, depuis le fork |

---

## 2. 👥 Organisation du TP

Le TP se fait **en binôme**, chacun jouant **les deux rôles en même temps** :

- 🧑‍💻 **Contributeur** : vous forkez le dépôt `todo-app` de votre binôme et lui proposez une fonctionnalité
- 🧑‍⚖️ **Mainteneur** : vous relisez et fusionnez la contribution que votre binôme propose sur **votre** dépôt

Dans les commandes, remplacez :
- `vous` → votre pseudo GitHub
- `binome` → le pseudo GitHub de votre binôme

💡 Seul(e) ? Forkez [`octocat/Spoon-Knife`](https://github.com/octocat/Spoon-Knife), le dépôt d'entraînement officiel de GitHub, et suivez les étapes côté contributeur.

---

## 3. 🍴 Étape 1 — Forker (rôle contributeur)

1. Ouvrez `https://github.com/binome/todo-app`
2. Cliquez sur **Fork** (en haut à droite)
3. ⚠️ **Repository name** : vous avez déjà un dépôt `todo-app` ! Renommez le fork en **`todo-app-binome`** (GitHub signale le conflit de nom)
4. Laissez *Copy the `main` branch only* coché → **Create fork**

✅ Vous arrivez sur `github.com/vous/todo-app-binome`, avec la mention *forked from binome/todo-app*.

---

## 4. 📥 Étape 2 — Cloner son fork et déclarer `upstream`

```bash
cd ~/formation
git clone git@github.com:vous/todo-app-binome.git
cd todo-app-binome
git remote -v
```

```text
origin  git@github.com:vous/todo-app-binome.git (fetch)
origin  git@github.com:vous/todo-app-binome.git (push)
```

`origin` est **votre fork**. Ajoutons le dépôt d'origine sous le nom **`upstream`** (convention), pour pouvoir en récupérer les nouveautés :

```bash
git remote add upstream https://github.com/binome/todo-app.git
git remote -v
```

```text
origin    git@github.com:vous/todo-app-binome.git (fetch)
origin    git@github.com:vous/todo-app-binome.git (push)
upstream  https://github.com/binome/todo-app.git (fetch)
upstream  https://github.com/binome/todo-app.git (push)
```

💡 L'URL HTTPS suffit pour `upstream` : on ne fait qu'y **lire**, sans authentification pour un dépôt public.

| Distant | Pointe vers | On y fait |
|---|---|---|
| `origin` | Votre fork | `push` de vos branches |
| `upstream` | Le dépôt d'origine | `fetch` / `pull` des nouveautés |

---

## 5. 🌿 Étape 3 — Développer et pousser sur son fork

⚠️ **Règle d'or du fork** : on ne travaille **jamais** directement sur `main`. Toujours une branche. `main` sert uniquement à suivre `upstream`.

```bash
git switch -c feature/modifier
```

Ajoutez une commande pour modifier le texte d'une tâche, après `rechercher` :

```python
def modifier(numero, texte):
    taches = charger()
    taches[numero - 1]["texte"] = texte
    sauvegarder(taches)
    print(f"Tâche {numero} modifiée : {texte}")
```

Dans `main()`, avant `aide` :

```python
    elif commande == "modifier":
        modifier(int(sys.argv[2]), " ".join(sys.argv[3:]))
```

Et ajoutez `modifier <numéro> <texte>` dans la liste des commandes d'`afficher_aide()`.

```bash
python todo.py ajouter "Apprendre Gti"
python todo.py modifier 1 "Apprendre Git"
python todo.py lister
git commit -am "Ajout de la commande modifier"
git push -u origin feature/modifier
```

✅ La branche est sur **votre fork**. Le dépôt de votre binôme n'a pas bougé.

---

## 6. 📬 Étape 4 — Ouvrir la Pull Request vers le dépôt d'origine

Sur `github.com/vous/todo-app-binome` : **Compare & pull request**.

Observez l'en-tête : la PR traverse les dépôts 🔀

```text
base repository: binome/todo-app   base: main   ←   head repository: vous/todo-app-binome   compare: feature/modifier
```

- Titre : `Ajout de la commande modifier`
- Description : quoi, pourquoi, comment tester
- ✅ Laissez coché **Allow edits by maintainers** : le mainteneur pourra pousser de petites corrections directement sur votre branche
- **Create pull request**

La PR apparaît dans l'onglet **Pull requests** du dépôt **de votre binôme**.

---

## 7. 🧑‍⚖️ Étape 5 — Relire la contribution reçue (rôle mainteneur)

Passez de l'autre côté : sur **votre** dépôt `github.com/vous/todo-app`, onglet **Pull requests**, la PR de votre binôme vous attend.

### Tester la PR en local avant de fusionner

Chaque PR est accessible via une référence spéciale `pull/<numéro>/head` sur **votre** dépôt :

```bash
cd ~/formation/todo-app
git fetch origin pull/3/head:pr-3       # remplacez 3 par le numéro de la PR
git switch pr-3
python todo.py modifier 1 "Test"
python todo.py modifier 99 "Test"       # 💥 IndexError !
git switch main
git branch -D pr-3
```

💡 Avec la GitHub CLI : `gh pr checkout 3`.

### Demander une correction

Dans **Files changed**, commentez la ligne `taches[numero - 1]["texte"] = texte` :

> Un numéro invalide fait planter le programme. Peux-tu ajouter la même vérification que dans `terminer` ?

→ **Review changes → Request changes**.

---

## 8. 🔧 Étape 6 — Corriger sa PR (rôle contributeur)

Dans votre clone du fork :

```python
def modifier(numero, texte):
    taches = charger()
    if numero < 1 or numero > len(taches):
        print(f"Numéro invalide : {numero}")
        return
    taches[numero - 1]["texte"] = texte
    sauvegarder(taches)
    print(f"Tâche {numero} modifiée : {texte}")
```

```bash
cd ~/formation/todo-app-binome
git commit -am "Vérification du numéro dans modifier"
git push
```

✅ La PR sur le dépôt de votre binôme s'est mise à jour toute seule.

---

## 9. ✅ Étape 7 — Fusionner (rôle mainteneur)

Sur votre dépôt : relisez le nouveau commit → **Approve** → **Squash and merge**.

Puis mettez à jour votre poste :

```bash
cd ~/formation/todo-app
git switch main
git pull
python todo.py aide          # la commande modifier, écrite par votre binôme, est là 🎉
git log --oneline -3
```

🎊 Vous venez de vivre le cycle complet d'une contribution open source, dans les deux sens.

---

## 10. 🔄 Étape 8 — Garder son fork à jour (rôle contributeur)

Votre fork est une **copie figée** : il ne suit pas automatiquement le dépôt d'origine. Or celui-ci a évolué (votre PR fusionnée en squash, d'autres commits…).

```bash
cd ~/formation/todo-app-binome
git switch main
git log --oneline -1                  # votre main est en retard
```

### Option A — Bouton GitHub « Sync fork »

Sur `github.com/vous/todo-app-binome` : **Sync fork → Update branch**. Puis en local :

```bash
git pull
```

### Option B — En ligne de commande (à connaître ⭐)

```bash
git fetch upstream                    # récupère les nouveautés du dépôt d'origine
git log --oneline main..upstream/main # ce qui nous manque
git merge upstream/main               # met à jour main local (fast-forward)
git push origin main                  # met à jour le fork sur GitHub
```

```text
  upstream ──fetch──► upstream/main ──merge──► main (local) ──push──► origin (fork)
```

Enfin, la branche de la PR est fusionnée : on fait le ménage.

```bash
git branch -D feature/modifier                  # -D car fusionnée en squash
git push origin --delete feature/modifier
```

💡 **Avant chaque nouvelle contribution** : synchroniser `main`, puis créer la branche depuis ce `main` à jour. Cela évite la plupart des conflits.

---

## 11. 📡 Récupérer n'importe quoi d'un dépôt distant

Un dépôt local peut suivre **autant de distants que nécessaire**. Exemple : votre binôme a une branche expérimentale sur son dépôt, que vous voulez tester ou dont vous voulez un seul commit.

🧪 **Côté binôme** (sur son `todo-app`) :

```bash
git switch -c experimentation
echo "print('Bonjour depuis la branche expérimentale')" > bonjour.py
git add bonjour.py
git commit -m "Script de bienvenue expérimental"
git push -u origin experimentation
git switch main
```

🧪 **Côté vous** (dans votre clone du fork, qui connaît `upstream`) :

```bash
cd ~/formation/todo-app-binome
git fetch upstream
git branch -a                                  # remotes/upstream/experimentation
git switch -c test-exp upstream/experimentation
python bonjour.py
```

Récupérer **un seul commit** précis dans votre branche, sans fusionner toute la branche : `git cherry-pick`.

```bash
git switch main
git switch -c feature/bonjour
git log --oneline upstream/experimentation -1  # notez le SHA
git cherry-pick <sha>
git log --oneline -2                           # le commit est copié (nouveau SHA)
```

💡 Vous pouvez aussi ajouter le dépôt **personnel** de n'importe qui comme distant : `git remote add alice https://github.com/alice/todo-app.git` puis `git fetch alice`.

🧹 Ménage :

```bash
git switch main
git branch -D test-exp feature/bonjour
```

---

## 📝 À retenir

```text
  Fork (GitHub) → git clone <mon fork>
  git remote add upstream <dépôt d'origine>
  git switch -c feature/x → commits → git push -u origin feature/x
  PR : base = dépôt d'origine / main  ←  head = mon fork / feature/x
  Corrections → git push sur la même branche → la PR se met à jour
  Sync : git fetch upstream && git merge upstream/main && git push origin main
  Tester une PR : git fetch origin pull/<n>/head:pr-<n>
  Un seul commit : git cherry-pick <sha>
```

---

⬅️ Précédent : [Module 10 — Branches et Pull Requests](10-branches-et-pull-requests.md) · ➡️ Suite : [Module 12 — Tags et Releases](12-tags-et-releases.md)
