# DataQuest — Context pour Claude Code

## C'est quoi ce projet
Application web progressive (PWA) d'apprentissage SQL et Python, style Duolingo.
Fonctionne offline, installable sur iPhone via Safari → "Sur l'écran d'accueil".
Pas de backend, pas de framework — HTML/CSS/JS pur, localStorage pour la persistance.

## Stack
- HTML + CSS + Vanilla JS (aucun framework, aucune dépendance NPM)
- Google Fonts : Plus Jakarta Sans + JetBrains Mono
- localStorage pour toutes les données utilisateur
- GitHub Pages pour le déploiement

## Fichiers du projet
```
dataquest/
├── index.html        ← app complète (tout en un seul fichier)
├── manifest.json     ← config PWA (nom, icônes, display standalone)
├── icon-512.png      ← icône app iPhone
├── icon-192.png      ← icône app Android/PWA
└── CLAUDE.md         ← ce fichier
```

## Architecture de l'app (index.html)

### Écrans
- **Splash** — animation 2.7s au lancement
- **Setup** — profil utilisateur (prénom, avatar, niveau)
- **App shell** — 3 onglets via bottom nav
  - **Home** — carte de progression, mode SQL/Python/Mix, bouton start
  - **Stats** — XP card, mini stats, graphe 7 jours (SVG), taux de réussite
  - **Profil** — infos, badges, reset
- **Map screen** — parcours visuel zigzag des 90 questions
- **Challenge** — session de 3 questions QCM
- **Summary** — résumé de session

### Données localStorage
```
dq8_p  → profil utilisateur {name, av, level}
dq8_g  → stats globales {xp, sessions, streak, today, history, totalCorrect, totalQ}
dq8_q  → mémoire questions {id: 'ok'|'fail'|'seen'}
```

### Base de questions
90 questions locales dans la constante `DB` :
- `DB.sql` — 30 questions SQL (10 novice + 10 apprenti + 10 expert)
- `DB.py` — 30 questions Python (10 novice + 10 apprenti + 10 expert)
- `DB.mix` — 30 questions mixtes SQL+Python

Structure d'une question :
```js
{
  id: 's01',           // identifiant unique
  diff: 'novice',      // 'novice' | 'apprenti' | 'expert'
  xp: 20,              // XP gagnés si correct
  title: 'Titre',
  desc: 'Énoncé avec <strong>HTML</strong>',
  ctx: null,           // contexte code (string) ou null
  options: ['A','B','C','D'],
  correct: 0,          // index de la bonne réponse
  expl: 'Explication pédagogique'
}
```

### Sélection des questions (progression linéaire)
Ordre de priorité pour une session de 3 questions :
1. Questions marquées 'fail' (ratées) — jusqu'à 2 max
2. Questions 'unseen' (jamais vues) — dans l'ordre du tableau
3. Questions 'ok' (déjà réussies) — si tout le reste est fait

### Système XP et niveaux
```js
Niveau 1 — Rookie      : 0 XP
Niveau 2 — Apprenti    : 100 XP
Niveau 3 — Analyste    : 250 XP
Niveau 4 — Expert      : 500 XP
Niveau 5 — Data Master : 900 XP
Niveau 6 — SQL Wizard  : 1400 XP
Niveau 7 — Pandas Pro  : 2000 XP
```

## Palette de couleurs (thème clair iOS)
```css
--bg: #F2F2F7        /* fond principal gris perle */
--s1: #FFFFFF        /* surfaces cards */
--sql: #0A84FF       /* bleu iOS — mode SQL */
--py: #FF9F0A        /* orange iOS — mode Python */
--mix: #BF5AF2       /* violet — mode Mix */
--acc: #34C759       /* vert iOS — succès, XP */
--red: #FF3B30       /* rouge iOS — erreur */
--green: #34C759     /* vert — correct */
--yellow: #FF9F0A    /* orange — streak */
```

## Polices
- **Plus Jakarta Sans** — tout le texte UI (poids 400-900)
- **JetBrains Mono** — code, contextes SQL/Python, blocs de code

## Conventions de code
- Tout en français dans l'UI
- Fonctions courtes et nommées clairement
- Pas de classes ES6, tout en fonctions simples
- Les IDs HTML correspondent aux fonctions JS qui les manipulent
- `save()` après chaque modification de state

## Ce qui est prévu / idées futures
- [ ] Ajouter 30 questions supplémentaires par matière (actuellement 30+30+30)
- [ ] Mode révision : revoir toutes les questions ratées en une session
- [ ] Statistiques par concept (ex: taux de réussite sur JOIN vs GROUP BY)
- [ ] Export des stats en JSON
- [ ] Animations de transition entre questions plus fluides
- [ ] Son optionnel sur bonne/mauvaise réponse

## Déploiement
```bash
# Modifier le code
# Tester localement avec un serveur simple :
python3 -m http.server 8080
# Ouvrir http://localhost:8080

# Pousser sur GitHub Pages
git add .
git commit -m "description du changement"
git push origin main

# GitHub Pages se met à jour automatiquement en ~1-2 min
# URL prod : https://lucasp9952.github.io/dataquest/
```

## Contexte utilisateur
- Data analyst en formation (2 mois de formation, encore 1 mois)
- Account Manager Apple SMB depuis 5 ans
- Premier jour de Python au moment de la création du projet
- Utilise Google Colab pour apprendre
- iPhone + Mac, full écosystème Apple
- Profil : apprend vite, aime les outils bien faits, applique le 80/20

## Notes importantes
- Le fichier index.html fait ~1000 lignes — tout est dedans intentionnellement
- Ne pas splitter en plusieurs fichiers sauf si vraiment nécessaire
- Tester sur Safari iOS avant de pousser (comportement différent de Chrome)
- localStorage est limité à ~5MB — pas de problème avec les données actuelles
- Les questions dans DB ne doivent jamais être modifiées sans mettre à jour les IDs
