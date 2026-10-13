# 🔀 Module 10 — Branches et Pull Requests

> ⏱️ **Durée** : 35 min\
> 🎯 **Objectif** : appliquer le **GitHub Flow** : issue → branche → Pull Request → revue → fusion.

---

## 1. 🌊 Le GitHub Flow

C'est le mode de travail le plus répandu sur GitHub, simple et efficace :

```text
  main ●────────────────────────────────●─────────►   (toujours stable, déployable)
        \                              /
         ●──────●──────●──────────────●
         1. branche   2. commits     4. merge
                       3. Pull Request + revue 👀
```

1. 🐛 Une **issue** décrit le besoin
2. 🌿 On crée une **branche** depuis `main`
3. 💾 On committe et on **pousse** la branche
4. 🔀 On ouvre une **Pull Request** (PR) : « je propose de fusionner ma branche dans `main` »
5. 👀 L'équipe **relit**, commente, demande des corrections
6. ✅ On **fusionne** dans `main` et on supprime la branche

💡 *Pull Request* (GitHub) = *Merge Request* (GitLab). Même concept.

---

## 2. 🐛 Créer une issue

Sur votre dépôt `todo-app` :

1. Onglet **Issues → New issue**
2. Titre : `Pouvoir rechercher une tâche`
3. Description :

```markdown
Avec beaucoup de tâches, la liste devient longue.

Proposition : une commande `rechercher <mot>` qui n'affiche que les tâches contenant le mot.

- [ ] Recherche insensible à la casse
- [ ] Afficher le numéro de la tâche
```

4. **Create** → notez son numéro : **#1**

💡 Les cases `- [ ]` deviennent des cases à cocher cliquables et une barre de progression s'affiche dans la liste des issues.

---

## 3. 🌿 Développer sur une branche

```bash
cd ~/formation/todo-app
git switch main
git pull
git switch -c feature/rechercher
```

Ajoutez la fonction après `vider` :

```python
def rechercher(mot):
    taches = charger()
    for numero, tache in enumerate(taches, start=1):
        if mot in tache["texte"]:
            print(f"{numero}. {tache['texte']}")
```

Dans `main()`, avant `aide` :

```python
    elif commande == "rechercher":
        rechercher(sys.argv[2])
```

Dans `afficher_aide()` :

```python
    print("Commandes : ajouter <texte>, lister, terminer <numéro>, supprimer <numéro>, vider, rechercher <mot>, aide")
```

```bash
python todo.py ajouter "Apprendre Git"
python todo.py rechercher Git
git commit -am "Ajout de la commande rechercher"
git push -u origin feature/rechercher
```

```text
remote: Create a pull request for 'feature/rechercher' on GitHub by visiting:
remote:      https://github.com/pseudo/todo-app/pull/new/feature/rechercher
```

---

## 4. 📬 Ouvrir la Pull Request

Sur GitHub, un bandeau jaune propose **Compare & pull request** : cliquez dessus (ou suivez le lien affiché par `git push`).

| Champ | Valeur |
|---|---|
| **base** | `main` ← la branche qui va **recevoir** les modifications |
| **compare** | `feature/rechercher` ← la branche qui **apporte** les modifications |
| Titre | `Ajout de la commande rechercher` |
| Description | voir ci-dessous |

```markdown
## Description
Ajoute une commande `rechercher <mot>` pour filtrer les tâches.

## Tester
    python todo.py ajouter "Apprendre Git"
    python todo.py rechercher Git

Closes #1
```

🔗 `Closes #1` (ou `Fixes #1`, `Resolves #1`) : l'issue #1 sera **fermée automatiquement** à la fusion de la PR.

Cliquez sur **Create pull request**. 💡 La flèche à côté permet de créer un **Draft pull request** : une PR « brouillon », pour montrer un travail en cours sans qu'il puisse être fusionné.

### Les onglets d'une PR

| Onglet | Contenu |
|---|---|
| 💬 **Conversation** | Description, commentaires, statut de la revue, bouton de fusion |
| 📝 **Commits** | Les commits de la branche |
| ✅ **Checks** | Résultats des tests automatiques (GitHub Actions, hors périmètre) |
| 📄 **Files changed** | Le *diff* complet, c'est là qu'on fait la revue |

---

## 5. 👀 La revue de code

### Inviter son binôme comme relecteur

Pour qu'une personne puisse relire et approuver, elle doit avoir accès au dépôt :

1. *Settings du dépôt → Collaborators → Add people* → pseudo GitHub de votre binôme
2. Le binôme **accepte l'invitation** (reçue par mail ou sur [github.com/notifications](https://github.com/notifications))
3. Dans la PR, colonne de droite : **Reviewers** ⚙️ → sélectionnez votre binôme

💡 Un **collaborateur** peut pousser directement dans votre dépôt. C'est la collaboration « en équipe ». Le **fork** (module 11) permet de contribuer **sans** ces droits.

### 🧪 TP — Relire la PR de son binôme

Chacun relit la PR de l'autre, dans l'onglet **Files changed** :

1. Survolez une ligne → cliquez sur le **+** bleu → laissez un commentaire
2. Essayez le bouton **±** (*Insert a suggestion*) pour proposer directement une correction. Par exemple, sur la ligne `if mot in tache["texte"]:`, suggérez :

    ````markdown
    ```suggestion
            if mot.lower() in tache["texte"].lower():
    ```
    La recherche est sensible à la casse : "git" ne trouve pas "Git".
    ````

3. **Review changes** (en haut à droite) → choisissez :

| Choix | Signification |
|---|---|
| 💬 Comment | Remarques générales, sans verdict |
| ✅ Approve | OK pour fusionner |
| ❌ Request changes | Corrections nécessaires avant fusion |

→ Choisissez **Request changes** et **Submit review**.

### Corriger suite à la revue

L'auteur de la PR a deux options :

- **Sur GitHub** : *Commit suggestion* directement sur la suggestion 🪄
- **En local** : corriger, committer, pousser **sur la même branche**

```bash
# en local, toujours sur feature/rechercher
git pull                                    # si une suggestion a été acceptée sur GitHub
# ... corrections éventuelles ...
git commit -am "Recherche insensible à la casse"
git push
```

✅ La PR se **met à jour automatiquement** avec les nouveaux commits. Pas besoin d'en ouvrir une nouvelle.

Le relecteur relit et **Approve** ✅.

---

## 6. ✅ Fusionner la PR

Dans l'onglet **Conversation**, le bouton vert propose 3 stratégies (flèche ▾) :

| Stratégie | Résultat dans `main` | Quand l'utiliser |
|---|---|---|
| **Create a merge commit** | Tous les commits de la branche + un commit de fusion | Garder l'historique détaillé |
| **Squash and merge** | **Un seul** commit regroupant toute la PR | Historique de `main` propre : 1 PR = 1 commit |
| **Rebase and merge** | Les commits rejoués un à un sur `main`, sans commit de fusion | Historique linéaire, commits soignés |

→ Choisissez **Squash and merge** → **Confirm**.

🎉 Constatez :
- La PR passe en violet **Merged**
- L'issue **#1 est fermée** automatiquement
- Bouton **Delete branch** : cliquez pour supprimer `feature/rechercher` sur GitHub

---

## 7. 🧹 Ménage en local

```bash
git switch main
git pull                          # récupère le commit de la PR
git log --oneline -3
git branch -d feature/rechercher
```

⚠️ Avec un *squash*, Git peut répondre `error: the branch 'feature/rechercher' is not fully merged` : les commits d'origine ne sont pas dans `main`, seul leur « résumé » y est. Ayant vérifié que la PR est fusionnée, on force : `git branch -D feature/rechercher`.

```bash
git fetch --prune                 # supprime origin/feature/rechercher, effacée sur GitHub
git branch -a
```

💡 Pour que ce soit automatique : `git config --global fetch.prune true`.

---

## 8. 💥 Conflit dans une Pull Request

Si `main` a évolué pendant la vie de la PR et touche les mêmes lignes, GitHub affiche **This branch has conflicts that must be resolved**.

- **Conflit simple** : bouton **Resolve conflicts** → éditeur web avec les marqueurs `<<<<<<<` / `=======` / `>>>>>>>` → **Mark as resolved** → **Commit merge**
- **Conflit complexe** : en local, on intègre `main` dans la branche :

```bash
git switch ma-branche
git pull origin main          # fetch + merge de main dans ma-branche → conflit
# résoudre comme au module 04, puis :
git add .
git commit --no-edit
git push                      # la PR se met à jour, le conflit disparaît
```

---

## 9. 🛡️ Protéger la branche `main`

Pour **interdire les push directs** sur `main` et imposer les PR :

1. *Settings du dépôt → **Rules → Rulesets** → New ruleset → New branch ruleset*
2. Nom : `Protection main` · **Enforcement status : Active**
3. *Target branches* → **Add target → Include default branch**
4. Cochez :
   - ✅ **Restrict deletions**
   - ✅ **Require a pull request before merging** → *Required approvals : 1*
   - ✅ **Block force pushes**
5. **Create**

🧪 Testez :

```bash
git switch main
echo "- Protection de main" >> CHANGELOG.md
git commit -am "Test push direct"
git push
```

```text
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - Changes must be made through a pull request.
 ! [remote rejected] main -> main (push declined due to repository rule violations)
```

✅ La règle fonctionne. Annulez ce commit local : `git reset --hard origin/main`.

ℹ️ Les rulesets et protections de branches sont gratuits sur les dépôts **publics**. Sur les dépôts **privés**, ils nécessitent un abonnement payant (GitHub Pro / Team).

⚠️ Pour la suite de la formation (module 11), vous pouvez **désactiver** ce ruleset (*Enforcement status : Disabled*) afin de garder de la souplesse.

---

## ✨ Bonnes pratiques des PR

- 🎯 **Petites PR** : une PR = un sujet. 200 lignes se relisent, 2 000 lignes se survolent.
- 📝 **Description claire** : quoi, pourquoi, comment tester
- 🔗 **Lier l'issue** : `Closes #N`
- 👀 **Se relire** dans *Files changed* avant de demander une revue
- 💬 **Revue bienveillante** : on commente le code, pas la personne. Proposer plutôt qu'imposer.

🏆 **Bonus — GitHub CLI** : l'outil `gh` ([cli.github.com](https://cli.github.com)) permet de tout faire depuis le terminal : `gh pr create`, `gh pr list`, `gh pr checkout 12`, `gh pr merge --squash`, `gh issue list`…

---

## 📝 À retenir

```text
  Issue → git switch -c feature/x → commits → git push -u origin feature/x
        → Pull Request (base: main ← compare: feature/x) → revue → corrections (push)
        → Merge (merge / squash / rebase) → supprimer la branche
        → git switch main && git pull && git branch -d feature/x
  "Closes #N" dans la PR ferme l'issue à la fusion
```

---

⬅️ Précédent : [Module 09 — Publier son code](09-remote-push-pull.md) · ➡️ Suite : [Module 11 — Fork et collaboration en binôme](11-fork-et-collaboration.md)
