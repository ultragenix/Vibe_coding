# 🎬 Script de Demo — Formation Vibe Coding
## Projet : "SAFER Dashboard — Transactions Foncieres a La Reunion"

> **Objectif strategique** : Le client construit le MEME projet dans les deux sessions.
> Session 1 = vibe coding brut (rapide mais fragile).
> Session 2 = workflow multi-agent (structure, teste, documente).
> A la fin, il compare les deux et comprend POURQUOI le workflow est superieur.

---

# ══════════════════════════════════════════════════════════════
# SESSION 1 — "La magie du vibe coding" (2h)
# ══════════════════════════════════════════════════════════════

## Avant la session (preparation toi)

```bash
# Verifie que son setup fonctionne
wsl --version
code --version
claude --version

# Cree un dossier de travail
mkdir -p ~/formation && cd ~/formation
```

---

## BLOC 1 — La demo "wow" que TU fais devant lui (10 min)

> **But** : Il voit un projet se creer de zero sous ses yeux. Il ne touche pas encore au clavier.

### Ce que tu dis :
> "Avant de commencer, je vais te montrer ce que le vibe coding permet de faire en quelques minutes. Regarde bien, je ne vais ecrire aucune ligne de code moi-meme."

### Etape 1 — Creer le dossier
```bash
mkdir demo-wow && cd demo-wow
claude
```

### Etape 2 — Prompt unique (copie-colle ca)
```
Cree-moi une page HTML complete "index.html" pour un dashboard de transactions
foncieres a La Reunion.

Le dashboard doit contenir :
1. Un header avec le titre "SAFER Reunion — Tableau de Bord Foncier"
2. Un compteur anime qui affiche : nombre de transactions, surface totale (ha),
   et prix moyen au m2
3. Un tableau avec 10 transactions fictives mais realistes pour La Reunion
   (communes reelles : Saint-Denis, Saint-Paul, Saint-Pierre, Le Tampon, etc.)
   Colonnes : Date, Commune, Type (agricole/residentiel), Surface (m2), Prix (EUR), Prix/m2
4. Un graphique en barres (Chart.js CDN) montrant le prix moyen par commune
5. Un filtre par commune (menu deroulant) qui filtre le tableau ET met a jour
   le graphique
6. Style moderne et professionnel : fond sombre (#1a1a2e), cartes avec ombres,
   couleurs SAFER (vert #27ae60, bleu #2e86c1)
7. Responsive, tout dans un seul fichier HTML

Les donnees sont en dur dans le JavaScript (pas de backend).
```

### Etape 3 — Laisser Claude Code travailler
> Commente a voix haute pendant que Claude Code :
> - "Tu vois, il cree la structure HTML..."
> - "La il ajoute le CSS avec les couleurs qu'on a demandees..."
> - "Maintenant les donnees fictives — des vraies communes de La Reunion..."
> - "Et le graphique avec Chart.js..."

### Etape 4 — Ouvrir le resultat
```bash
# Toujours dans Claude Code, demande :
Ouvre le fichier index.html dans le navigateur
```

### Etape 5 — Moment "wow"
> Montre le dashboard : les compteurs, le tableau, le graphique, le filtre.
> "Et tout ca, sans ecrire une seule ligne de code. Juste en decrivant ce que je voulais."

### Etape 6 — Iterer en direct (2-3 min)
> Montre le pouvoir de l'iteration :
```
Ajoute une barre de recherche au-dessus du tableau qui filtre en temps reel
pendant que je tape. Le filtre doit chercher dans toutes les colonnes.
```

> Puis :
```
Change le theme du graphique : utilise un degrade de vert au lieu des barres
bleues, et ajoute les valeurs au-dessus de chaque barre.
```

> **Ce que tu dis** : "Tu vois ? On itere. On decrit, on verifie, on ameliore. C'est ca le vibe coding."

---

## BLOC 2 — Les principes (20 min, theorie legere)

> Donne-lui la fiche recap (le .docx qu'on a cree).
> Passe en revue les 3 regles d'or EN S'APPUYANT SUR CE QU'IL VIENT DE VOIR.

### Les 3 regles illustrees par la demo :

**Regle 1 — Specifier avant de coder**
> "Tu as vu mon prompt ? Il etait precis : j'ai donne les communes, les couleurs,
> le nombre de lignes, le type de graphique. Si j'avais juste dit 'fais un dashboard',
> le resultat aurait ete generique et inutile."

**Regle 2 — Petits pas**
> "Apres le dashboard, j'ai demande la recherche SEPAREMENT, puis le theme du graphique
> SEPAREMENT. Je n'ai pas tout mis dans un seul prompt de 50 lignes."

**Regle 3 — Toujours verifier**
> "A chaque etape, on a ouvert le navigateur pour verifier. On ne fait jamais
> confiance aveuglement."

### L'anatomie d'un bon prompt
> Montre les 4 elements sur la fiche : Contexte → Objectif → Contraintes → Format

### Les phrases magiques
> Parcours la fiche, insiste sur :
> - "Propose-moi un plan avant de coder"
> - "Etape par etape"
> - "Corrige seulement X, ne touche a rien d'autre"

---

## BLOC 3 — C'est son tour ! (1h)

> Il va construire SON dashboard, avec ton guidage.

### Etape 1 — Setup (5 min)
```bash
cd ~/formation
mkdir mon-dashboard && cd mon-dashboard
claude
```

### Etape 2 — Lui faire creer CLAUDE.md (5 min)
> Explique : "Ce fichier, Claude Code le lit automatiquement. C'est ton briefing."

> **Guide-le pour ecrire** (il tape, tu corriges) :
```
Cree un fichier CLAUDE.md avec le contenu suivant :

# Dashboard SAFER Reunion

## Contexte
Application web pour visualiser les transactions foncieres a La Reunion.
Projet de formation, donnees fictives.

## Regles
- Tout en un seul fichier HTML
- Style professionnel, couleurs sombres
- Donnees en dur dans le JavaScript
- Commentaires en francais
- Responsive design
```

### Etape 3 — Premier prompt (10 min)
> **Tu ne lui donnes PAS le prompt. Tu lui demandes de l'ecrire.**
> "Decris-moi ce que tu voudrais voir dans ton dashboard. Pense a :
> qu'est-ce qui serait utile pour la SAFER ?"

> S'il bloque, guide-le :
> - "Quelles colonnes dans le tableau ?"
> - "Quel type de graphique ?"
> - "Quelles communes ?"

> Il envoie son prompt. Vous regardez le resultat ensemble.

### Etape 4 — Iterations guidees (30 min)
> Laisse-le piloter. Il demande des ajouts, des corrections.
> Interviens UNIQUEMENT s'il :
> - Fait un prompt trop vague → "Sois plus precis, dis exactement ce que tu veux"
> - Demande trop d'un coup → "Decoupe ca, demande d'abord X, puis Y"
> - Ne verifie pas → "Ouvre dans le navigateur avant de continuer"

> **Suggestions d'iterations pour le guider** (donne-en 2-3 s'il manque d'idees) :
> - "Ajoute un filtre par type de terrain (agricole, residentiel, commercial)"
> - "Ajoute un total en bas du tableau (surface totale, prix moyen)"
> - "Ajoute un deuxieme graphique en camembert pour la repartition par type"
> - "Ajoute un bouton pour exporter le tableau en CSV"
> - "Ajoute une carte simplifiee de La Reunion avec les communes colorees par prix"

### Etape 5 — Recap (10 min)
> "Tu viens de creer un dashboard complet sans ecrire une ligne de code.
> Mais... est-ce que tu ferais confiance a ce code pour un vrai projet SAFER ?
> Est-ce qu'on a teste quoi que ce soit ? Est-ce qu'on a un plan ?
> Est-ce qu'on sait si le filtre marche dans tous les cas ?"

> **Transition vers Session 2** :
> "La prochaine fois, on va reconstruire ce projet, mais avec une methode
> professionnelle. Tu vas voir la difference de qualite."

---

# ══════════════════════════════════════════════════════════════
# SESSION 2 — "Le workflow multi-agent" (2h)
# ══════════════════════════════════════════════════════════════

## Avant la session (preparation toi)

```bash
# S'assurer que le template est dezippe
cd ~/formation
unzip multi-agent-template.zip   # si pas deja fait
```

---

## BLOC 1 — Rappel + Setup (30 min)

### Etape 1 — Rappel de la Session 1 (5 min)
> "La derniere fois, on a construit un dashboard en vibe coding pur.
> C'etait rapide, impressionnant, mais :
> - Pas de plan → on a improvise
> - Pas de tests → on ne sait pas si tout marche
> - Pas de documentation → personne ne peut reprendre le projet
> - Pas de securite → on n'a rien verifie
>
> Aujourd'hui, meme projet, mais avec une equipe de 6 agents IA."

### Etape 2 — Presenter le systeme (10 min)
> Montre le guide multi-agent v3 (le .docx).
> Explique les 6 agents en 1 phrase chacun :
> - "L'Architecte planifie, il ne code jamais"
> - "Le Developpeur code, rien d'autre"
> - "Le QA teste et cherche les failles"
> - "Le Validateur est le dernier controle"
> - "Le Tech Lead nettoie le code"
> - "Le Documentaliste ecrit la doc"
>
> Montre le schema du workflow : plan → dev → qa → validate

### Etape 3 — Setup du projet (15 min)
> C'est LUI qui tape tout. Tu guides.

```bash
cd ~/formation

# Lancer le script de setup
./multi-agent-template/setup.sh safer-dashboard

# Verifier la structure
cd safer-dashboard
ls -la
ls .claude/agents/     # Il voit les 6 agents
ls .claude/commands/    # Il voit les 7 commandes
cat CLAUDE.md           # Il voit la config

# Ouvrir dans VS Code pour suivre les fichiers
code .
```

> Montre-lui dans VS Code :
> - Le dossier `.claude/agents/` avec les 6 fichiers
> - Le dossier `.claude/commands/` avec les 7 commandes
> - Le `CLAUDE.md` qui configure tout

> "Tu vois ? Tout est en place. Maintenant on lance Claude Code."

```bash
claude
```

---

## BLOC 2 — Le workflow en action (1h)

### PHASE 1 — Planification avec l'Architecte (15 min)

> **Ce que tu dis** : "Premiere etape : on appelle l'Architecte. Il va nous poser des
> questions et creer le plan. Regarde, je tape juste une commande."

```
/project:plan Je veux un dashboard de transactions foncieres pour la SAFER
Reunion. Affichage de transactions avec filtres, graphiques, et statistiques.
Donnees en dur (pas de backend). Un seul fichier HTML. Style professionnel.
```

> L'Architecte va poser des questions par blocs.
> **Laisse le client repondre** — c'est lui qui decide :
> - Quelles communes ? (Saint-Denis, Saint-Paul, Saint-Pierre, Le Tampon, Saint-Andre)
> - Quels types de terrain ? (Agricole, Residentiel, Commercial)
> - Quels graphiques ? (Barres par commune, camembert par type)
> - Quels filtres ? (Commune, type, fourchette de prix)

> Quand l'Architecte genere PLAN.md et STATE.md :
> **Montre-les dans VS Code.**
> "Tu vois ? On a un plan structure AVANT d'ecrire le moindre code.
> Chaque partie a des criteres d'acceptation precis."

```bash
# Commiter dans un nouveau terminal ou avec ! dans Claude Code
!git add -A && git commit -m "feat: plan initial du dashboard SAFER"
```

### PHASE 2 — Developper la Partie 1 (15 min)

> "Maintenant on appelle le Developpeur. Il va lire le plan et coder la premiere partie."

```
/project:dev Partie 1
```

> Le Dev va :
> 1. Lire PLAN.md
> 2. Annoncer ce qu'il va faire (et demander confirmation)
> 3. Coder
> 4. Tester
> 5. Creer un rapport dans REPORTS/

> **Pendant qu'il travaille, commente** :
> "Regarde : il a lu le plan automatiquement. Il annonce ce qu'il va faire
> AVANT de coder — comme un vrai developpeur dans une equipe."

> Quand c'est fini, montre le rapport :
```
!cat REPORTS/dev-partie-1.md
```

> "Voila le rapport du developpeur. Il liste tout ce qu'il a fait,
> les fichiers crees, les tests ecrits."

```bash
!git add -A && git commit -m "feat(part-1): structure de base du dashboard"
```

### PHASE 3 — Audit QA (15 min)

> **Ce que tu dis** : "Maintenant, le moment cle. On envoie le QA.
> C'est un agent DIFFERENT — il n'a pas ecrit ce code, il va le juger
> avec un oeil neuf. Et il va ecrire SES PROPRES tests."

```
/project:qa Partie 1
```

> Le QA va :
> 1. Lire le code du dev
> 2. Ecrire ses propres tests
> 3. Faire l'audit securite (checklist)
> 4. Donner un verdict

> **C'est LE moment pedagogique cle.** Insiste :
> "Tu vois ? Le QA a trouve [X probleme] que le developpeur n'avait pas vu.
> C'est exactement pour ca qu'on separe les roles : celui qui code ne teste
> jamais son propre travail."

> Montre le rapport QA :
```
!cat REPORTS/qa-partie-1.md
```

> "Score de securite, problemes trouves et corriges, tests independants.
> Compare ca avec la Session 1 ou on n'a rien teste du tout."

```bash
!git add -A && git commit -m "test(part-1): audit QA et corrections"
```

### PHASE 4 — Validation (10 min)

```
/project:validate Partie 1
```

> Le Validateur verifie tout : tests, criteres, non-regression.

> "Dernier controle. Si tout est vert, la partie est officiellement terminee."

> Montre STATE.md mis a jour :
```
!cat STATE.md
```

> "Regarde la progression : Partie 1 validee. On voit l'historique de tout
> ce qui s'est passe."

```bash
!git add -A && git commit -m "chore(part-1): validation OK"
```

### PHASE 5 — Status check (5 min)

```
/project:status
```

> "A tout moment, tu peux voir ou en est ton projet avec cette commande.
> C'est comme un tableau de bord de TON projet."

---

## BLOC 3 — Comparaison + Debrief (30 min)

### Etape 1 — Ouvrir les deux resultats cote a cote (10 min)

> Ouvre deux fenetres de navigateur :
> - A gauche : le dashboard de la Session 1 (~/formation/mon-dashboard/index.html)
> - A droite : le dashboard de la Session 2 (~/formation/safer-dashboard/index.html)

> **Ce que tu dis** :
> "Visuellement, les deux se ressemblent. Mais regarde les differences :"

> Montre dans VS Code :

```bash
# Session 1 : un dossier avec juste index.html
ls ~/formation/mon-dashboard/
# → index.html   (c'est tout)

# Session 2 : un projet structure
ls ~/formation/safer-dashboard/
# → .claude/ PLAN.md STATE.md CORRECTIONS.md REPORTS/ docs/ tests/ src/ ...
```

> **Les 5 differences cles a montrer** :

> **1. Plan vs improvisation**
> "En Session 1, on a improvise. En Session 2, on a un PLAN.md avec des criteres
> d'acceptation. Si quelqu'un reprend le projet dans 6 mois, il sait exactement
> ce qui a ete prevu."
```
!head -50 PLAN.md
```

> **2. Tests vs pas de tests**
> "En Session 1, zero test. En Session 2, le developpeur ET le QA ont ecrit
> des tests. On SAIT que le code marche."
```
!ls tests/
```

> **3. Rapports vs rien**
> "En Session 1, pas de trace. En Session 2, chaque etape a genere un rapport.
> C'est de la tracabilite — essentiel dans un contexte professionnel."
```
!ls REPORTS/
```

> **4. Securite vs rien**
> "En Session 1, on n'a jamais verifie la securite. En Session 2, le QA a
> fait un audit complet avec une checklist."
```
!cat REPORTS/qa-partie-1.md
```

> **5. Historique Git vs rien**
> "En Session 2, chaque etape a un commit. On peut revenir en arriere a
> n'importe quel moment."
```
!git log --oneline
```

### Etape 2 — Le message cle (5 min)

> **Ce que tu dis** (c'est ta punchline) :
>
> "Le vibe coding simple, c'est magique pour prototyper. Tu as une idee,
> en 10 minutes tu as quelque chose qui marche.
>
> Mais pour un VRAI projet — un outil que la SAFER va utiliser au quotidien,
> avec des donnees sensibles, des utilisateurs multiples, des mises a jour
> regulieres — il faut de la methode.
>
> Le workflow multi-agent, c'est cette methode. Pas besoin de savoir coder,
> mais il faut savoir PILOTER les agents. Et c'est exactement ce que tu
> viens d'apprendre."

### Etape 3 — Fiche de survie + next steps (15 min)

> Remets-lui :
> 1. La fiche recap (le premier .docx)
> 2. Le guide multi-agent v3 (le deuxieme .docx)
> 3. Le template ZIP (sur cle USB ou partage ecran)

> **Exercices pour pratiquer seul** :
> Propose-lui 3 projets adaptes a SAFER :

> **Projet 1 — Facile** : "Un formulaire de saisie de transactions foncieres
> qui genere un PDF recapitulatif. Tu fais ca en vibe coding simple."

> **Projet 2 — Moyen** : "Un outil de comparaison de prix au m2 entre communes,
> avec import de donnees CSV. Tu utilises le workflow multi-agent."

> **Projet 3 — Ambitieux** : "Un mini-systeme de suivi des parcelles SAFER
> avec historique des transactions, alertes sur les prix anormaux, et export
> de rapports. Workflow multi-agent complet."

> **Ce que tu dis pour finir** :
> "Tu as maintenant les outils ET la methode. Le template est pret,
> les commandes sont la. Tu n'as plus qu'a pratiquer. Et si tu as
> des questions, je suis disponible."

---

# ══════════════════════════════════════════════════════════════
# NOTES POUR TOI (ne pas montrer au client)
# ══════════════════════════════════════════════════════════════

## Gestion du temps

| Bloc | Session 1 | Session 2 |
|------|-----------|-----------|
| Demo/rappel | 15 min | 5 min |
| Theorie | 20 min | 25 min (presenter le systeme) |
| Pratique guidee | 60 min | 60 min (workflow complet) |
| Debrief | 25 min | 30 min (comparaison) |
| **Total** | **2h** | **2h** |

## Si ca prend trop de temps
- Session 1 : Reduis les iterations a 2-3 max
- Session 2 : Fais seulement Partie 1 (dev → qa → validate), c'est suffisant
  pour montrer le concept. Pas besoin de faire 3 parties.

## Si Claude Code est lent ou bugge
- Aie un plan B : ouvre un fichier HTML que tu as DEJA prepare d'avance
  (la demo de la Session 1 que tu as testee chez toi)
- "Parfois l'IA a besoin de quelques secondes de reflexion, comme nous tous"

## Si le client pose des questions techniques
- "Le but n'est pas de comprendre le code, c'est de comprendre le processus.
  Le code, c'est le travail de l'IA. Ton travail, c'est de donner les bonnes
  instructions."

## Le piege a eviter
- Ne PAS montrer un resultat parfait du premier coup. Si Claude Code fait
  une petite erreur, c'est une OPPORTUNITE pedagogique :
  "Tu vois ? L'IA fait des erreurs. C'est pour ca que la Regle 3 dit
  TOUJOURS verifier. Et c'est pour ca que le workflow a un agent QA."

## Ton objectif cache
- Le client doit repartir en se disant :
  1. "Je peux faire des trucs incroyables avec ca" (Session 1)
  2. "Et avec la methode, je peux faire des trucs FIABLES" (Session 2)
  3. "ReunIA peut nous aider a mettre ca en place a la SAFER" (business)