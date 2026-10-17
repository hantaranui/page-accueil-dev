# Journal de revue — Page d'accueil Partenariat simplifié (widget Grist)

## Tâche 0 — Sprint 0 : installation de la squad d'agents

Date : 15/10/2026
Agent : aucun (installation faite à la main, guidée pas à pas)
Prompt : aucun

Note /5 :
Ce qui allait : copie depuis _squad-base sans rien écraser (cp -n) ; _squad-base non modifiée ; aucun fichier de code touché (git status).
Ce que j'ai fait corriger : CLAUDE.md rempli avec le contexte du widget ; fiche docs/design-system-france-travail.md créée ; section « Dans ce projet » ajoutée à designer-ux (avec WebFetch pour le site du design system), dev-interface (sans WebFetch : il passe par les maquettes de docs/ux/), testeur et auditeur-securite.
Tests : aucun lancé (aucun code modifié)
Commit : chore: installe la squad d'agents et le CLAUDE.md du widget (Sprint 0)

## Tâche 2 — Fiche de conception : bandeau commun sur la page d'accueil

Date : 17/10/2026
Agent : @designer-ux
Prompt : décrire le retrait de l'en-tête propre de la page d'accueil et l'ajout du bandeau commun (data-titre « Accueil »), le niveau d'accès, l'espacement et les tests à adapter

Note /5 : 5
Ce qui allait : fichiers et lignes exacts ; niveau d'accès déjà « full » vérifié dans le code ; espacement relié aux variables du design system ; test à adapter repéré ; a trouvé que la page perdrait son titre principal (h1) et a proposé un h1 sr-only du design system au lieu de décider seul
Ce que j'ai fait corriger : rien. Décision : h1 sr-only « Partenariat simplifié – Accueil » accepté ; README et guide du testeur à mettre à jour après le code
Tests : 0 lancés, 0 réussis (fiche de conception)
Fichiers modifiés : docs/ux/bandeau-commun-accueil.md (créé)
Build refait : pas nécessaire
Commit : à faire avec le code


## Tâche 3 — Bandeau commun sur la page d'accueil (code)

Date : 17/10/2026
Agent : @dev-interface
Prompt : retirer l'en-tête propre (index.html et style.css), ajouter la balise du bandeau commun (data-titre « Accueil ») et un h1 sr-only, sans toucher au texte, aux cartes, au script ni à l'espacement

Note /5 : 4
Ce qui allait : uniquement les fichiers prévus ; lignes avant / après listées ; build refait ; seul le test attendu en échec ; a signalé l'écart avec la fiche sur la place du h1
Ce que j'ai fait corriger : rien (h1 laissé avant

## Tâche 4 — Adapter le test des classes après le retrait de l'en-tête

Date : 17/10/2026
Agent : @testeur
Prompt : remplacer home-banner et home-title dans le test « la feuille declare bien des classes en tete de selecteur » par des classes qui existent encore, puis lancer tous les tests

Note /5 : [ta note]
Ce qui allait : une seule ligne modifiée (test/styles.test.js:51, home-main et home-intro) ; but du test inchangé ; a vérifié qu'aucun autre test ne dépend des anciennes classes ; résultat exact donné
Ce que j'ai fait corriger : rien. Restent : commentaire obsolète ligne 25 ; test du h1 et de la balise bandeau.js proposé par la fiche, à écrire plus tard
Tests : 18 lancés, 18 réussis
Fichiers modifiés : test/styles.test.js
Build refait : pas nécessaire (déjà à jour)
Commit : feat: remplacer l'en-tête propre par le bandeau commun



























<main></main>




























, choix accepté)
Tests : 18 lancés, 17 réussis (1 échec attendu : test à adapter par le testeur)
Fichiers modifiés : src/page-accueil/index.html, src/page-accueil/style.css, page-accueil.html (régénéré)
Build refait : oui
Commit : à faire après le testeur
