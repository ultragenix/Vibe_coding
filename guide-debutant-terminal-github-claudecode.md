# Guide Complet — Terminal, GitHub & Claude Code
### Pour les débutants qui veulent vibe coder

---

## 1. LE TERMINAL — Se repérer et naviguer

### Où suis-je ?

```bash
pwd
```
Affiche le chemin complet du dossier où tu te trouves.
Exemple de résultat : `/home/kalou/WorkSpace/MonProjet`

---

### Voir ce qu'il y a dans le dossier

```bash
ls
```
Liste les fichiers et dossiers.

```bash
ls -la
```
Version détaillée : affiche aussi les fichiers cachés (ceux qui commencent par `.`).

---

### Se déplacer dans les dossiers

```bash
cd NomDuDossier
```
Entre dans un dossier.

```bash
cd ..
```
Remonte d'un niveau (dossier parent).

```bash
cd ~
```
Va directement dans ton dossier personnel (home).

```bash
cd /chemin/complet/vers/dossier
```
Va directement n'importe où avec le chemin absolu.

**Exemple pratique :**
```bash
pwd                          # je suis dans /home/kalou
cd WorkSpace                 # j'entre dans WorkSpace
cd MonProjet                 # j'entre dans MonProjet
pwd                          # affiche /home/kalou/WorkSpace/MonProjet
cd ..                        # je remonte dans WorkSpace
cd ../..                     # je remonte de deux niveaux d'un coup
```

---

### Créer des dossiers et fichiers

```bash
mkdir NomDuDossier
```
Crée un nouveau dossier.

```bash
mkdir -p dossier/sous-dossier
```
Crée plusieurs niveaux de dossiers en une fois.

```bash
touch monfichier.txt
```
Crée un fichier vide.

---

### Lire un fichier

```bash
cat monfichier.txt
```
Affiche le contenu d'un fichier dans le terminal.

---

### Effacer des choses

```bash
rm monfichier.txt
```
Supprime un fichier.

```bash
rm -rf NomDuDossier
```
Supprime un dossier et tout ce qu'il contient.
> ⚠️ Pas de corbeille — c'est définitif. Vérifie deux fois avant d'exécuter.

---

## 2. GIT & GITHUB — Gérer ton code

### Concept de base

Git = l'outil qui enregistre l'historique de ton code sur ton ordi.
GitHub = le serveur en ligne où ton code est sauvegardé et partagé.

---

### Cloner un projet existant depuis GitHub

```bash
git clone https://github.com/utilisateur/nom-du-repo.git
```
Télécharge le projet complet sur ton ordi et crée un dossier avec le même nom.

```bash
git clone https://github.com/utilisateur/nom-du-repo.git mon-dossier
```
Idem mais le dossier s'appellera `mon-dossier`.

**Après le clone, entre dans le projet :**
```bash
cd nom-du-repo
```

---

### Récupérer les dernières mises à jour

```bash
git pull
```
Télécharge et applique les dernières modifications depuis GitHub.
À faire régulièrement si tu travailles en équipe ou si tu reprends un projet.

---

### Voir l'état de tes fichiers modifiés

```bash
git status
```
Montre quels fichiers ont été modifiés, ajoutés ou supprimés.

---

### Sauvegarder tes changements (commit)

```bash
git add .
```
Sélectionne tous les fichiers modifiés pour le prochain commit.

```bash
git add monfichier.txt
```
Sélectionne un fichier spécifique.

```bash
git commit -m "Description de ce que tu as fait"
```
Enregistre les changements avec un message explicatif.

**Exemple :**
```bash
git add .
git commit -m "ajout de la page d'accueil"
```

---

### Envoyer ton code sur GitHub

```bash
git push
```
Envoie tes commits locaux vers GitHub.

---

### Voir l'historique des commits

```bash
git log
```
Affiche l'historique avec auteur, date et message.

```bash
git log --oneline
```
Version condensée, une ligne par commit.

---

### Travailler avec des branches

```bash
git branch
```
Affiche les branches existantes. L'étoile indique la branche actuelle.

```bash
git checkout -b ma-nouvelle-feature
```
Crée une nouvelle branche et se positionne dessus.

```bash
git checkout main
```
Revient sur la branche principale.

```bash
git merge ma-nouvelle-feature
```
Fusionne une branche dans la branche actuelle.

---

### Résumé du flux de travail Git classique

```
1. git pull                        ← mettre à jour
2. (faire des modifications)
3. git status                      ← vérifier ce qui a changé
4. git add .                       ← préparer les changements
5. git commit -m "mon message"     ← enregistrer
6. git push                        ← envoyer sur GitHub
```

---

## 3. CLAUDE CODE — Vibe coder avec l'IA

### Lancer Claude Code dans un projet

```bash
claude
```
Lance Claude Code en mode interactif dans le dossier courant.

```bash
claude --dangerously-skip-permissions
```
Lance Claude Code en mode autonome complet : Claude peut modifier des fichiers, exécuter des commandes, créer des dossiers — sans te demander confirmation à chaque action.

> ⚠️ À utiliser quand tu fais confiance à ce que tu demandes à Claude. Idéal pour aller vite en vibe coding.

---

### Lancer Claude sur un projet rapidement

```bash
cd MonProjet
claude --dangerously-skip-permissions
```
Deux commandes et tu es prêt à vibe coder.

---

### Commandes utiles dans Claude Code

Une fois dans Claude Code, tu peux taper des commandes qui commencent par `/` :

| Commande | Description |
|---|---|
| `/help` | Affiche toutes les commandes disponibles |
| `/clear` | Efface l'historique de la conversation |
| `/model` | Choisir le modèle IA (sonnet, opus, haiku) |
| `/exit` | Quitter Claude Code |
| `!commande` | Exécute une commande shell sans quitter Claude |

**Exemple pour exécuter une commande shell depuis Claude :**
```
! git status
! ls -la
! npm install
```

---

### Comment bien demander à Claude

**Sois précis sur ce que tu veux :**
```
Crée une page HTML avec un formulaire de connexion qui a 
un champ email, un champ mot de passe et un bouton "Se connecter".
Utilise du CSS moderne, design épuré fond blanc.
```

**Donne le contexte du projet :**
```
Je fais une application web en Python Flask pour gérer 
une liste de tâches. Ajoute une fonctionnalité pour 
marquer une tâche comme complétée.
```

**Demande des corrections directement :**
```
Le bouton ne s'affiche pas correctement sur mobile, 
corrige le CSS pour que ça soit responsive.
```

---

## 4. FLUX DE TRAVAIL COMPLET — Du début à la fin

### Démarrer un nouveau projet depuis GitHub

```bash
# 1. Cloner le projet
git clone https://github.com/mon-compte/mon-projet.git

# 2. Entrer dans le dossier
cd mon-projet

# 3. Lancer Claude Code
claude --dangerously-skip-permissions
```

---

### Reprendre un projet existant

```bash
# 1. Aller dans le bon dossier
cd ~/WorkSpace/mon-projet

# 2. Mettre à jour depuis GitHub
git pull

# 3. Lancer Claude Code
claude --dangerously-skip-permissions
```

---

### Sauvegarder son travail après une session

```bash
# (Quitter Claude Code d'abord avec /exit ou Ctrl+C)

# 1. Voir ce qui a changé
git status

# 2. Ajouter tous les changements
git add .

# 3. Créer un commit
git commit -m "description de ce qui a été fait"

# 4. Envoyer sur GitHub
git push
```

---

## 5. RACCOURCIS ET ASTUCES

### Dans le terminal

| Raccourci | Action |
|---|---|
| `Tab` | Auto-complétion du nom de fichier/dossier |
| `Flèche haut/bas` | Naviguer dans l'historique des commandes |
| `Ctrl + C` | Annuler/interrompre la commande en cours |
| `Ctrl + L` | Effacer l'écran du terminal |
| `Ctrl + A` | Aller au début de la ligne |
| `Ctrl + E` | Aller à la fin de la ligne |

### Astuce Tab — indispensable

Commence à taper un nom et appuie sur `Tab` pour que le terminal complète automatiquement :
```bash
cd Work   # appuie sur Tab → cd WorkSpace/
```

---

## 6. ERREURS COURANTES ET SOLUTIONS

### "Permission denied"
```bash
# Problème : tu n'as pas les droits
# Solution : ajouter sudo devant (demande le mot de passe)
sudo la-commande
```

### "command not found"
```bash
# Le programme n'est pas installé
# Solution : installer ce qu'il faut (ex: pour Node.js)
sudo apt install nodejs   # Linux/WSL
brew install node         # Mac
```

### "fatal: not a git repository"
```bash
# Tu n'es pas dans un dossier git
# Solution : se déplacer dans le bon dossier ou initialiser git
git init   # pour démarrer un nouveau repo git dans le dossier actuel
```

### Git push refusé
```bash
# Récupérer d'abord les changements distants
git pull
# Puis re-essayer
git push
```

---

## 7. RÉFÉRENCE RAPIDE — Antisèche

```bash
# NAVIGATION
pwd                          # où suis-je ?
ls                           # qu'est-ce qu'il y a ici ?
ls -la                       # version détaillée + fichiers cachés
cd NomDossier                # entrer dans un dossier
cd ..                        # remonter d'un niveau
cd ~                         # aller au home

# FICHIERS
mkdir NomDossier             # créer un dossier
touch fichier.txt            # créer un fichier vide
cat fichier.txt              # lire un fichier
rm fichier.txt               # supprimer un fichier
rm -rf NomDossier            # supprimer un dossier (⚠️ définitif)

# GIT
git clone <url>              # télécharger un projet
git pull                     # mettre à jour
git status                   # voir les changements
git add .                    # préparer tous les changements
git add fichier.txt          # préparer un fichier spécifique
git commit -m "message"      # enregistrer les changements
git push                     # envoyer sur GitHub
git log --oneline            # voir l'historique

# CLAUDE CODE
claude                       # lancer Claude Code
claude --dangerously-skip-permissions   # lancer en mode autonome
/help                        # aide dans Claude Code
/clear                       # effacer la conversation
/exit                        # quitter Claude Code
! git status                 # exécuter une commande shell dans Claude
```

---

*Guide de référence — Vibe Coding avec Claude Code*
