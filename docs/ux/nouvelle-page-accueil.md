# Nouvelle page d'accueil : texte à gauche, photo à droite, fond blanc, sans cartes

Fiche de conception pour dev-interface. Aucun code n'est modifié par cette fiche.
Elle complète [bandeau-commun-accueil.md](bandeau-commun-accueil.md) (bandeau commun et h1 masqué, déjà en place) et remplace sa section 2 (« Ce qui reste inchangé ») : les cartes et le panneau de configuration disparaissent.

Demande du chef de projet :

1. retirer les deux vignettes (liste des actions, page publique de soutien) : on passe par le menu du bandeau commun ;
2. texte d'accueil plus grand : « Bienvenue sur l'outil Partenariat simplifié qui vous aidera à gérer et piloter les actions d'insertion par le sport. » (la phrase « Que souhaitez-vous faire ? » disparaît) ;
3. fond blanc pour toute la page ;
4. texte à gauche, photo à droite.

Sources : src/page-accueil/ (index.html, style.css, script.js), test/, build.js, feuille de style du CDN France Travail lue le 19/10/2026 (valeurs ci-dessous, hors mention contraire), docs/design-system-france-travail.md. Le site du design system n'a pas été consulté : les valeurs viennent de la feuille, qui fait foi.

Skill UI/UX : le dossier .claude/skills/ n'existe pas dans ce dépôt, aucun skill n'a donc été appliqué.

## 1. Parcours et écran

1. La personne ouvre le document Grist : le bandeau commun s'affiche en haut (titre « Accueil », menu).
2. Elle lit la phrase de bienvenue, à gauche, en grand ; la photo est à droite.
3. Pour agir, elle utilise le menu du bandeau commun. La page ne propose plus aucune action : c'est voulu.

Aucun état de succès, d'erreur ni de vide : la page est statique (plus aucun texte venant du document).

### Maquette, grand écran (à partir de 768 px)

```
┌──────────────── bandeau commun (hors de notre CSS) ────────────────┐
├─────────────────────────────────────────────────────────────────────┤
│  fond blanc                                                         │
│                                                                     │
│   Bienvenue sur l'outil Partenariat       ┌───────────────────────┐ │
│   simplifié qui vous aidera à gérer       │                       │ │
│   et piloter les actions d'insertion      │        PHOTO          │ │
│   par le sport.                           │  (terrain + groupe)   │ │
│                                           │                       │ │
│   (texte centré verticalement             └───────────────────────┘ │
│    face à la photo)                                                 │
└─────────────────────────────────────────────────────────────────────┘
```

### Maquette, petit écran (moins de 768 px) : proposition retenue

```
┌──────────────────────────┐
│ bandeau commun           │
├──────────────────────────┤
│ fond blanc               │
│ Bienvenue sur l'outil    │
│ Partenariat simplifié    │
│ qui vous aidera à gérer  │
│ et piloter les actions   │
│ d'insertion par le sport.│
│ ┌──────────────────────┐ │
│ │  PHOTO (recadrée au  │ │
│ │  centre, hauteur     │ │
│ │  limitée)            │ │
│ └──────────────────────┘ │
└──────────────────────────┘
```

## 2. Mise en page : deux colonnes, puis une colonne

### Points de rupture du design system

La feuille du CDN utilise ces largeurs dans ses media queries : **576, 768, 992, 1200 et 1440 px** (les plus fréquentes : 576, 1200, 992, 768). Le bloc `@media (min-width:768px)` est celui qui contient `.d-md-none` : 768 px est donc le point « md » du design system. Les noms de ses classes (`col-md-6`, `d-md-block`, `order-md-2`) sont dans test/design-system-classes.txt.

Rappel : un widget Grist est dans une iframe, les media queries mesurent la largeur **du widget**, pas celle de la fenêtre. Un widget étroit sur une grande page passera en une colonne.

| Largeur du widget | Mise en page                                                      |
| ----------------- | ----------------------------------------------------------------- |
| 768 px et plus    | deux colonnes de même largeur : texte à gauche, photo à droite |
| moins de 768 px   | une colonne : texte au-dessus, photo en dessous                   |

Le code actuel utilise 600 px et 1000 px (style.css lignes 84 et 88), qui ne sont **pas** des valeurs du design system. Ces règles disparaissent avec les cartes ; les nouvelles utilisent 768 px.

### Petit écran : texte au-dessus + photo en dessous (retenu), ou photo masquée ?

| Option                                                    | Avantage                                                                                                                                | Inconvénient                                                                                                                   |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **A. Photo en dessous, hauteur limitée (retenue)** | Même contenu partout ; la page ne paraît pas vide sous la phrase ; le texte reste lu en premier (il est avant la photo dans le HTML). | La photo ajoute du défilement : on limite sa hauteur (voir §5).                                                               |
| B. Photo masquée (`display: none`)                     | Page très courte.                                                                                                                      | Une image masquée en CSS est quand même téléchargée : aucun gain de poids ; et sous le texte il ne resterait presque rien. |

Choix : A. La photo est décorative : si le chef de projet préfère B, c'est une seule règle à changer, sans conséquence sur l'accessibilité. **À valider.**

### Grille : CSS propre, pas la grille `.row` / `.col-*` du design system

La grille du design system existe (`row`, `col-md-6`, `container` : inventaire lignes 2540, 316, 384), mais `.row` a des marges négatives de −15 px et demande des colonnes avec gouttière : plus de réglages que de gain. On garde l'approche déjà utilisée pour les cartes : une grille CSS avec nos classes `home-`. Elle ne reprend aucun nom du design system (`home-content` et `home-photo` ne figurent pas dans l'inventaire ; `content` y figure, mais le préfixe `home-` suffit, règle de CLAUDE.md). **À valider.**

### Espacements (variables du design system, valeurs lues dans la feuille)

| Usage                                      | Variable                                                                                | Valeur |
| ------------------------------------------ | --------------------------------------------------------------------------------------- | ------ |
| Écart entre les deux colonnes (≥ 768 px) | `--ft-spacer-l1`                                                                      | 2rem   |
| Écart texte / photo (< 768 px)            | `--ft-spacer-m2`                                                                      | 1.5rem |
| Marges de la page                          | inchangées : `.home-main` `padding: 2rem 1.5rem`, puis `1.5rem 1rem` sous 600 px | —     |

Les marges de `.home-main` existent déjà (style.css lignes 12 à 14 et 89). Elles valent `--ft-spacer-l1` / `--ft-spacer-m2` / `--ft-spacer-s2` mais restent en valeurs en dur : les convertir n'est pas demandé. Largeur maximale du contenu : 80rem, comme `.home-intro` aujourd'hui (style.css ligne 19).

## 3. Structure HTML

Remplace tout le contenu de `<main>` (index.html lignes 17 à 60). Le `h1` `sr-only` (ligne 15) **reste tel quel**.

```html
<h1 class="sr-only">Partenariat simplifié – Accueil</h1>

<main class="home-main">
  <div class="home-content">
    <p class="t1 home-intro">Bienvenue sur l'outil Partenariat simplifié qui vous aidera à gérer et piloter les actions d'insertion par le sport.</p>
    <img class="home-photo" src="photo-accueil.jpg" alt="" width="1200" height="802">
  </div>
</main>
```

Points d'attention :

- **Ordre dans le HTML : texte puis photo.** Il donne l'ordre de lecture (clavier, lecteur d'écran) et l'ordre visuel sans CSS (`order`) ; aucune classe `order-*` n'est nécessaire.
- **`<p>` et non plus `<h2>`** : la phrase est un texte d'accueil, pas un titre de section. La page garde un seul titre, le `h1` masqué (CLAUDE.md : titre principal obligatoire). `.t1` donne l'aspect d'un titre sur n'importe quel élément. **À valider** : si le chef de projet veut garder un `h2`, remplacer `<p class="t1 …">` par `<h2 class="t1 …">`, rien d'autre ne change.
- `width="1200" height="802"` : la taille réelle du fichier allégé (§5). Le navigateur réserve la place avant le chargement : la page ne saute pas.
- `alt=""` : photo décorative (§5).
- Le lien du design system (feuille et autoloader) et `body class="outil-agent"` ne changent pas.

Composants du design system utilisés : **aucun composant**, seulement la classe typographique `.t1` (page Typographie : https://designsystem.francetravail.io/docs/fondamentaux/typographie.html, non consultée ; valeurs lues dans la feuille).

## 4. Texte : taille, graisse, couleur

### Classes de titre sous `body.outil-agent` (valeurs de la feuille)

| Classe  | Taille grand écran | Taille ≤ 576 px  | Graisse | Interligne grand écran |
| ------- | ------------------- | ----------------- | ------- | ----------------------- |
| `.t1` | 1.5rem (24 px)      | 1.375rem (22 px)  | 500     | 1.75rem                 |
| `.t2` | 1.125rem (18 px)    | 1.25rem (20 px)   | 500     | 1.3125rem               |
| `.t3` | 1rem (16 px)        | non surchargée   | 500     | 1.1875rem               |
| `.t4` | 0.875rem (14 px)    | 1.125rem (18 px)* | 500     | 1rem                    |
| `.t5` | 0.8125rem (13 px)   | 1rem (16 px)*     | 500     | 0.9375rem               |

\* Pour `.t4` et `.t5`, la valeur ≤ 576 px est la valeur « hors outil agent » (la feuille la fixe en dur). Seules `.t1` et `.t2` ont été vérifiées ligne à ligne ; `.t3` à `.t5` : **à valider** si on les utilise (cette fiche ne les utilise pas).

Le texte d'accueil est aujourd'hui en `.t2` (18 px). **« Plus grand » = `.t1`**, la plus grande des classes `.t1` à `.t5`. Graisse 500 : celle de la feuille (la documentation du site annonce 400, voir docs/design-system-france-travail.md : on suit la feuille). Aucune taille ni graisse n'est écrite à la main.

Deux particularités à connaître :

- Sous 576 px, `.t2` (20 px) est plus grande que sur grand écran (18 px) : c'est la feuille qui le fait. Sans effet pour nous, qui passons à `.t1`.
- `.t1` ajoute `margin-bottom: 2rem`. Dans la grille, cette marge n'a pas lieu d'être : `.home-intro` la remet à 0 (`margin: 0`), l'écart vient du `gap` de la grille.

Si 24 px paraît encore trop petit au chef de projet, le design system a aussi des variables `display-*` (`--ft--font-size-display-1` : 2.625rem en outil agent), mais **aucune classe `display` n'a été vérifiée** et la demande cite `.t1` à `.t5` : à ne proposer qu'avec son accord. **À valider.**

Alignement : à gauche (l'actuel `text-align: center` de `.home-intro` est retiré).

### Couleurs : fond et texte, définis ensemble

Piège : sous `body.outil-agent`, la variable `--ft-color-background-layout-body` vaut **#dbe3ff** (bleu pâle), pas blanc (elle vaut #fff seulement hors outil agent). Le `body` actuel (style.css ligne 8) utilise cette variable : la page est donc bleue aujourd'hui, et la remettre telle quelle ne donnera pas un fond blanc.

| Élément                            | Variable du design system               | Valeur  | Statut                                                                                                      |
| ------------------------------------ | --------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------- |
| Fond de la page (`body`)           | `--ft-color-background-neutral-light` | #fff    | non surchargée par `outil-agent` ; déjà utilisée par le panneau de configuration (style.css ligne 58) |
| Texte d'accueil (`.home-intro`)    | `--ft-color-text-content-strong`      | #121212 | déjà utilisée par `.home-intro` (ligne 22)                                                             |
| Texte courant par défaut (`body`) | `--ft-color-text-content-default`     | #2e2e31 | déjà sur `body` (ligne 9)                                                                               |

`.home-main` n'a pas de fond propre : un seul fond, sur `body`, qui couvre toute la page. Fond et couleur du texte sont posés dans la **même règle** `body`, pour qu'on ne change jamais l'un sans l'autre.

### Mode clair et mode sombre de Grist

- La feuille du design system ne contient **aucune** règle `prefers-color-scheme` ni mode sombre : ses variables ne changent pas.
- Le widget est une iframe. Parce que `body` pose explicitement un fond (#fff) **et** une couleur de texte (#121212 / #2e2e31), la page doit s'afficher à l'identique en mode clair et en mode sombre de Grist : texte sombre sur fond blanc dans les deux cas.
- Si seul le fond avait été défini, un texte clair hérité d'un thème sombre serait devenu illisible sur le blanc : c'est la raison de la règle commune ci-dessus.
- Dans le mode sombre, la page sera un grand rectangle blanc dans un document sombre : c'est ce que demande le chef de projet. Le bandeau commun (Shadow DOM) suit ses propres règles, hors de cette fiche.
- **À valider** : le rendu dans Grist DEV, mode clair et mode sombre, bureau et mobile. Cette fiche n'a rien vérifié dans un navigateur.

### Contraste (RGAA, critère 3.2 : au moins 4,5:1 pour du texte normal, 3:1 pour un grand texte)

Calcul fait à partir des valeurs de la feuille, texte sur #fff :

| Texte             | Couleur | Rapport           | Seuil           |
| ----------------- | ------- | ----------------- | --------------- |
| Texte d'accueil   | #121212 | **18,73:1** | 4,5:1 : atteint |
| Texte par défaut | #2e2e31 | 13,54:1           | 4,5:1 : atteint |

Le texte est posé sur un fond blanc, sans autre couleur : cela respecte la règle « ne pas colorer un texte posé sur un fond de couleur ». **À valider** : relever les couleurs calculées dans le navigateur (docs/design-system-france-travail.md).

## 5. La photo

Fichier : src/page-accueil/photo-accueil.jpg. Paysage, environ 2350 × 1570 px (rapport 3:2), **3 728 466 octets, soit environ 3,7 Mo** (relevé avec `ls -l`). Sujet : un groupe en échauffement sur un terrain de basket. Le fichier n'est pas encore suivi par git.

### Poids et dimensions à viser

|               | Valeur                                                                     |
| ------------- | -------------------------------------------------------------------------- |
| Largeur       | **1200 px** (réduction proportionnelle : 1200 × 802 px)            |
| Poids         | **moins de 200 Ko** (soit environ 18 fois moins qu'aujourd'hui)      |
| Format        | JPEG, qualité à régler (voir ci-dessous)                                |
| Métadonnées | à supprimer (position GPS, appareil) : le dépôt et la page sont publics |

Pourquoi 1200 px : à l'affichage, une colonne mesure au plus environ 600 px (contenu limité à 80rem, deux colonnes, écart de 2rem). 1200 px suffit donc pour les écrans à double densité de pixels.

Comment l'alléger (à faire par le chef de projet, hors dépôt, avant tout `git add`) :

- **Sur Mac, en ligne de commande** : `sips` est livré avec macOS. Exemple, à adapter : `sips -Z 1200 -s format jpeg -s formatOptions 70 photo-accueil.jpg --out photo-accueil-leger.jpg` (`-Z 1200` fixe le plus grand côté à 1200 px ; `formatOptions` est la qualité). Si le résultat dépasse 200 Ko, baisser la qualité (60, 55…) ; si la photo est abîmée, la remonter. À contrôler à l'œil. **À valider** : cette commande n'a pas été exécutée pour cette fiche.
- **Application Aperçu** : Outils > Ajuster la taille (1200 px de large), puis Fichier > Exporter en JPEG avec le curseur de qualité.
- Éviter un site d'optimisation en ligne : la photo montre des personnes et serait envoyée à un tiers.
- Contrôle final : `ls -l` doit afficher moins de 200 000 octets ; ouvrir l'image et vérifier que le terrain et le groupe sont nets.

Important : l'original de 3,7 Mo ne doit **jamais** être commité. Le dépôt est public et git garde tout l'historique : un fichier lourd commité une fois y reste, même supprimé ensuite. Remplacer le fichier par la version allégée (même nom) avant `git add`.

### Comment la publier avec le widget

Contexte : build.js ne sait aujourd'hui qu'assembler HTML + CSS + JavaScript dans page-accueil.html (il ne copie aucune image). Grist ne charge que ce fichier, depuis GitHub Pages. Un `<img src="photo-accueil.jpg">` ne fonctionnera donc que si le fichier image est **publié à côté** de page-accueil.html, ou **à l'intérieur** de celui-ci.

|                                  | Option 1 : à côté (recommandée)                                                                                                                                                 | Option 2 : dans le HTML assemblé                                                                                                                   |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Principe                         | Le fichier allégé est publié à la racine du dépôt, à côté de page-accueil.html ; `src="photo-accueil.jpg"` ; build.js le copie depuis src/page-accueil/ à chaque build. | build.js remplace `src="photo-accueil.jpg"` par une adresse `data:image/jpeg;base64,…` dans page-accueil.html.                                 |
| Poids du HTML                    | inchangé ; la photo est mise en cache à part                                                                                                                                      | +33 % du poids de la photo (200 Ko → environ 270 Ko dans le HTML), rechargé avec la page                                                          |
| Lisibilité du fichier généré | normale                                                                                                                                                                             | page-accueil.html devient illisible (une ligne de plusieurs centaines de Ko)                                                                        |
| Écart avec CLAUDE.md            | CLAUDE.md dit que page-accueil.html est « le seul fichier chargé par Grist » : un second fichier (l'image) est chargé par la page                                               | aucun : un seul fichier                                                                                                                             |
| Changements                      | build.js (une copie de fichier) ; test/build.test.js (vérifier que la copie est à jour)                                                                                           | build.js (lecture + encodage) ; test/build.test.js (sa fonction `assemble` refait l'assemblage : la même transformation doit y être reproduite) |

Recommandation : option 1, plus simple à relire et à maintenir. Elle demande **l'accord du chef de projet** : elle modifie build.js, et CLAUDE.md demande de valider tout changement d'outillage. L'effort est faible (quelques lignes dans build.js, un test), le risque est d'oublier de publier l'image : le test de build doit alors échouer. **À valider** avant que dev-interface ne touche à build.js.

Adresse prévue en ligne (option 1) : à côté de https://hantaranui.github.io/page-accueil-partenariat-simplifi-/page-accueil.html, donc `…/photo-accueil.jpg`. Dans le serveur local (`python3 -m http.server 8765`), l'image doit être à la racine pour s'afficher ; `src="photo-accueil.jpg"` y est relatif à page-accueil.html.

### Taille d'affichage et recadrage

|           | Grand écran (≥ 768 px)                             | Petit écran (< 768 px)                                                                                          |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Largeur   | 100 % de sa colonne (environ 600 px au maximum)      | 100 % du widget, moins les marges (1rem de chaque côté)                                                        |
| Hauteur   | automatique : rapport 3:2 conservé, aucun recadrage | limitée pour ne pas trop allonger la page : à valider (ordre de grandeur : un rapport 16:9 environ)            |
| Recadrage | aucun                                                | au **centre** : `object-fit: cover` et `object-position: 50% 50%`, pour garder le terrain et le groupe |

Règles CSS de principe (propriétés CSS standard, pas des composants) : `max-width: 100%`, `height: auto`, `display: block`. Si la colonne de texte est beaucoup plus courte que la photo, le texte est centré verticalement (`align-items: center` sur la grille). Pas d'arrondi ni d'ombre : aucun n'est demandé et aucun n'est défini pour une image dans le design system. **À valider.**

### Texte alternatif

La photo est **décorative** : le texte d'accueil porte tout le contenu. Donc `alt=""` (attribut présent mais vide) : les lecteurs d'écran l'ignorent (RGAA, critère 1.2 sur les images de décoration : **à valider**). Ne pas écrire de description « pour bien faire » : elle alourdirait la lecture sans rien apporter. Aucune légende, aucun `aria-label`.

Si la photo venait un jour à porter une information (par exemple une légende liée à un partenaire), son `alt` devrait être renseigné : changement à rediscuter.

## 6. Code à retirer ou à modifier

Numéros de ligne relevés dans les fichiers actuels.

### src/page-accueil/index.html

| Lignes   | Contenu                                                                                                                                                                                                                       | Action                                                                                                                      |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 8        | balise `bandeau.js`                                                                                                                                                                                                         | **garder**                                                                                                            |
| 15       | `h1.sr-only`                                                                                                                                                                                                                | **garder**                                                                                                            |
| 18       | `h2.t2.home-intro#intro` (avec « Que souhaitez-vous faire ? »)                                                                                                                                                            | remplacer par le `<p>` du §3 (sans la 2ᵉ phrase)                                                                        |
| 19 à 40 | `nav.home-cards#cards` : les deux cartes `#card-dashboard` et `#card-soutien`, avec leurs `target="_top"`, images `dashboard.svg` et `handshake.svg`, lignes d'état `#status-dashboard` et `#status-soutien` | **supprimer**                                                                                                         |
| 42 à 59 | `section.home-config#config` : le panneau « Cible des cartes », ses deux `<select>` `#config-dashboard` et `#config-soutien`, `#config-form`, `#config-cancel`, `#config-help`                                | **supprimer**                                                                                                         |
| 62       | `<script src="script.js">`                                                                                                                                                                                                  | voir script.js : si le fichier est vidé en totalité, build.js lève une erreur (il exige la balise) : **la garder** |

### src/page-accueil/script.js

Tout ce qui sert aux cartes disparaît : `TARGETS`, `state`, `docUrlFromBaseUrl`, `listPages`, `resolveTarget`, `buildLinks`, `statusMessage`, `render`, `openConfig`, `closeConfig`, `makeOption`, `saveConfig`, `load`, les deux écouteurs du formulaire (lignes 209 et 210), `grist.onOptions` (lignes 213 à 216) et l'appel `load()` (ligne 217). Plus aucune lecture de `_grist_Pages` ni `_grist_Views`, plus aucune écriture d'options, plus de lien vers une page du document.

**À garder** : `grist.ready({requiredAccess: "full"});` (sans `onEditOptions`, qui n'a plus de fonction à appeler). Raison : la fiche du bandeau commun indique que le niveau « full » est celui demandé aujourd'hui et qu'il n'y a rien à y changer. Baisser ce niveau (« none ») est **hors demande** et pourrait priver le bandeau commun de ce dont il a besoin : question pour le collègue du bandeau. **À valider.**

Conséquence : le bouton « Ouvrir la configuration » de Grist n'a plus d'effet (il n'y a plus d'`onEditOptions`) ; vérifier ce qu'il affiche dans Grist DEV.

### src/page-accueil/style.css

| Lignes     | Règle                                                                                                            | Action                                                                                                                                                                                                       |
| ---------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 5 à 10    | `body`                                                                                                          | fond : `--ft-color-background-layout-body` → `--ft-color-background-neutral-light` ; couleur du texte inchangée (§4)                                                                                  |
| 12 à 14   | `.home-main`                                                                                                    | garder                                                                                                                                                                                                       |
| 16 à 23   | `.home-intro`                                                                                                   | retirer `text-align: center` et la marge basse de 2.5rem ; ajouter `margin: 0` ; garder la couleur `--ft-color-text-content-strong`. Mettre à jour le commentaire (il parle de `.t2` et des cartes) |
| 25 à 35   | `.home-cards` (et son commentaire)                                                                              | **supprimer**                                                                                                                                                                                          |
| 37 à 42   | `.home-cards .ds-card-link-body`                                                                                | **supprimer**                                                                                                                                                                                          |
| 44 à 50   | `.home-card-status` et `.home-card-status:empty`                                                              | **supprimer**                                                                                                                                                                                          |
| 52 à 80   | `.home-config`, `.home-config-title`, `.home-config-help`, `.home-config-field`, `.home-config-actions` | **supprimer**                                                                                                                                                                                          |
| 84 à 86   | `@media (max-width: 1000px)` (`.home-cards`)                                                                  | **supprimer**                                                                                                                                                                                          |
| 88 à 92   | `@media (max-width: 600px)`                                                                                     | garder `.home-main { padding: 1.5rem 1rem; }` ; supprimer `.home-cards` et `.home-config`                                                                                                              |
| à ajouter | `.home-content` (grille, §2) et `.home-photo` (§5), avec la règle à 768 px                                | nouvelles règles, toutes en `home-`                                                                                                                                                                       |

Après retrait, les composants Card link (`ds-card-link`, `ds-card-link-lg`, `ds-card-link-disabled`), les illustrations « spot », `form-label`, `form-control`, `btn btn-primary` et `btn btn-secondary` ne servent plus dans cette page. La ligne « Composants utilisés » de CLAUDE.md est à mettre à jour (hors de cette fiche).

### Fichier généré

`page-accueil.html` : **ne pas modifier à la main**, le régénérer avec `npm run build` après tout changement dans src/.

## 7. Tests à supprimer ou adapter

### test/links.test.js : à supprimer en entier

Tous ses tests portent sur les liens des cartes (adresse du document, pages, cibles) ; le code qu'ils exercent disparaît. Ses 13 tests :

1. « l'adresse du document se deduit de celle de son API »
2. « les pages se lisent dans l'ordre du document, adressees par leur vue »
3. « Grist retrouve le document et la page dans chaque lien »
4. « sans configuration, les cartes visent les pages de meme nom »
5. « le numero de page choisi l'emporte, meme apres un renommage »
6. « une configuration incomplete retombe sur ce qu'elle precise »
7. « une cible introuvable laisse la carte sans adresse et dit laquelle manque »
8. « chez un editeur a acces partiel, le numero enregistre suffit »
9. « liste des pages illisible : on se fie au numero enregistre »
10. « sans adresse de document, aucune carte ne recoit de lien »
11. « les cartes recoivent leur lien une fois le document lu »
12. « un editeur a ses liens des qu'un proprietaire a enregistre la configuration »
13. « un acces refuse aux pages est signale sur les cartes »

Garder ce fichier tel quel ferait échouer la suite : ces tests appellent des fonctions qui n'existeront plus.

### test/helpers/widget.js : à adapter ou à supprimer

La ligne 15 (`const EXPORTED = ["state", "TARGETS"];`) ajoute à la fin du script une ligne qui lit `state` et `TARGETS`. Ces deux constantes disparaissent : `loadWidget` lèverait une erreur dès le chargement. Deux possibilités :

- si on ajoute le test « accès complet » ci-dessous : mettre `EXPORTED` à `[]` (liste vide) et retirer des doublures ce dont plus rien ne sert (`docApi`, `getElementById`…) : changement minimal d'abord, nettoyage ensuite si le testeur le juge utile ;
- sinon : supprimer le fichier, qui n'aurait plus aucun utilisateur.

### test/styles.test.js : à adapter

- **« la feuille declare bien des classes en tete de selecteur »** (ligne 49) : la liste de la ligne 51 contient `home-cards`, qui n'existera plus. La remplacer par des classes qui restent ou arrivent : `home-main`, `home-intro`, `home-content`, `home-photo`.
- « l'inventaire du design system est bien charge » (ligne 42) : inchangé.
- « aucune classe du widget ne reprend un nom du design system » (ligne 56) : inchangé ; il vérifiera automatiquement les nouvelles classes.

### test/build.test.js : à conserver, avec une adaptation possible

- **« des widgets sont bien presents dans src/ »** (ligne 30) : inchangé.
- **« page-accueil.html est a jour par rapport a ses sources »** (ligne 35, nom construit avec le dossier) : échouera tant que `npm run build` n'est pas relancé. Si l'option 1 du §5 est retenue (photo copiée à côté du HTML), ajouter un test qui compare la photo copiée à la source ; si l'option 2, reproduire dans la fonction `assemble` (ligne 18) la même transformation que build.js.

### Tests à ajouter (à confirmer par le chef de projet et par le testeur)

Aucun test ne les couvre aujourd'hui :

- index.html contient exactement un `h1`, de classe `sr-only` ;
- index.html ne contient plus `ds-card-link` ni `home-config` ;
- le `<img class="home-photo">` a un attribut `alt` vide, des attributs `width` et `height`, et son fichier existe ;
- le fichier photo publié pèse moins de 200 000 octets (garde-fou contre le commit de l'original de 3,7 Mo) ;
- script.js appelle `grist.ready` avec `requiredAccess: "full"` (test « accès complet »).

Les tests ne vérifient ni le rendu ni les couleurs : un contrôle visuel (clair/sombre, bureau/mobile) reste nécessaire.

## 8. Autres fichiers touchés (hors périmètre de cette fiche)

- **CLAUDE.md** : « Objectif » (cartes qui ouvrent Dashboard et Page publique), « Fonctionnement du widget » (liens `target="_top"`, tables `_grist_Pages` et `_grist_Views`, options, accès complet), « Composants utilisés » (Card link, formulaire), « Pièges connus » (noms de pages effacés pour l'accès partiel : ne concerne plus la page), « Chantier en cours ». À mettre à jour par le chef de projet.
- **README.md**, **docs/tests/guide-testeur.md**, **docs/backlog.md**, **REVIEW_LOG.md** : à relire pour retirer ce qui décrit les cartes et la configuration (rôle de documentaliste). Non relus pour cette fiche.
- **Publication** : le travail reste sur la branche du chantier, pas de fusion dans main avant le bandeau commun prêt (CLAUDE.md).
- **Menu du bandeau commun** : les utilisateurs ne peuvent plus atteindre la liste des actions et la page publique **que** par ce menu. Vérifier qu'il contient bien ces deux liens avant la mise en ligne, sinon plus rien ne mène à ces pages depuis l'accueil.

## Règles pour dev-interface (résumé)

1. Fond blanc : `body { background: var(--ft-color-background-neutral-light); color: var(--ft-color-text-content-default); }` (les deux ensemble).
2. Texte d'accueil : `<p class="t1 home-intro">`, couleur `--ft-color-text-content-strong`, aligné à gauche, `margin: 0`. Aucune taille ni graisse écrite à la main.
3. Grille `.home-content` : une colonne, `gap: var(--ft-spacer-m2)` ; deux colonnes de même largeur à partir de 768 px, `gap: var(--ft-spacer-l1)`, contenu centré verticalement, largeur max 80rem.
4. Photo : `alt=""`, `width="1200" height="802"`, `max-width: 100%`, `height: auto`, recadrage au centre sous 768 px.
5. Ne pas toucher au `h1` `sr-only` ni à la balise `bandeau.js`.
6. Ne pas toucher à build.js sans l'accord de le chef de projet ; relancer `npm run build` puis `npm test`.
7. N'écrire aucune adresse de document Grist dans le code ; ne commiter que la photo allégée.

## Questions ouvertes

1. Petit écran : photo en dessous (proposé) ou masquée ?
2. Taille du texte : `.t1` (24 px) suffit-elle, ou faut-il une taille `display` (non vérifiée) ?
3. `<p>` ou `<h2>` pour la phrase d'accueil ?
4. Photo : option 1 (fichier à côté, build.js modifié) ou option 2 (intégrée au HTML) ?
5. Garder `requiredAccess: "full"` dans `grist.ready` : confirmé par le collègue du bandeau commun ?
6. Droit à l'image : les personnes visibles sur la photo ont-elles donné leur accord pour une publication sur un dépôt et une page publics ?
7. Rendu dans Grist DEV, modes clair et sombre, bureau et mobile : à regarder (aucun rendu vérifié par cette fiche).
8. Le menu du bandeau commun contient-il bien la liste des actions et la page publique ?

## Origine de la photo

- Photo fournie par le chef de projet (17/10/2026), qui en assure les droits d'usage pour l'outil.
- Crédit à afficher : à confirmer avec le chef de projet.
- L'original (3,7 Mo) est conservé hors du dépôt ; seule la version allégée est commitée.
