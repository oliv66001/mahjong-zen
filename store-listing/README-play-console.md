# Checklist de mise en ligne Google Play — Mahjong Zen

Guide pas à pas pour préparer la demande de publication. Les éléments marqués
« TOI » nécessitent une action de ta part (comptes, clés, hébergement).

## 1. Héberger les pages légales (TOI)

Les deux pages du dossier `store-listing/` doivent être accessibles publiquement :

1. Crée un dépôt GitHub public `mahjong-zen` (ou utilise le dépôt du projet).
2. Active GitHub Pages (Settings → Pages) et publie `privacy-policy.html` et
   `account-deletion.html`.
3. URLs attendues par l'application (modifiables dans le fichier `.env`) :
   - https://oliv66001.github.io/mahjong-zen/privacy-policy.html
   - https://oliv66001.github.io/mahjong-zen/account-deletion.html
   - https://oliv66001.github.io/mahjong-zen/contact.html (page de contact,
     adresse : wordsleuth.mywordgame@gmail.com)
4. Renseigne ces URLs dans la Play Console :
   - *Règles de l'application → Politique de confidentialité* ;
   - *Contenu de l'application → Suppression de compte* (page account-deletion).

## 2. Configurer les services de jeux Play (TOI + déjà câblé côté app)

1. Play Console → *Services de jeux Play → Configuration* : crée le projet de jeu,
   lie-le à ton projet Google Cloud (celui où tu crées les clés OAuth).
2. Crée l'identifiant OAuth Android dans Google Cloud avec le
   **SHA-1 de ta clé de signature** (upload key + clé de signature Play).
3. Récupère l'**identifiant numérique du projet de jeu** (12 chiffres) et
   renseigne-le dans `.env` → `EXPO_PUBLIC_PLAY_GAMES_APP_ID` (copie
   `.env.example` vers `.env` si besoin). Le plugin `withPlayGames` l'injecte
   au prebuild ; si tu gardes un dossier `android/` déjà généré, mets aussi à
   jour `android/app/src/main/res/values/games-ids.xml`.
4. Crée les classements et succès dans la Play Console puis reporte leurs
   identifiants dans `.env` (variables `EXPO_PUBLIC_LEADERBOARD_*` et
   `EXPO_PUBLIC_ACHIEVEMENT_*`). Tant qu'une valeur reste `REMPLACER_...`,
   l'app l'ignore sans erreur.
5. Ajoute des **testeurs** aux services de jeux (obligatoire avant publication).

Suggestion de ressources à créer :
- Classements : « Meilleur score », « Meilleur temps » ;
- Succès : « Première victoire », « Combo x8 », « Victoire sans aide », « 10 victoires ».

## 3. Formulaire « Sécurité des données » (Play Console)

Réponses correspondant au comportement réel de l'app :
- Collecte de données : **Non** (aucune donnée envoyée à l'éditeur ; le jeu est hors ligne).
- Partage de données : **Non**.
- Données chiffrées en transit : sans objet (pas de transmission).
- Suppression des données : oui, in-app (*Paramètres → Supprimer toutes mes données*).
- Google Play Jeux est un service de Google : la connexion facultative est couverte
  par la déclaration de Google, mais mentionne-la dans la section
  « Fonctionnalités tierces » si le formulaire le demande.

## 4. Classification du contenu

Questionnaire IARC : jeu de réflexion, aucune violence, aucun contenu sensible,
pas d'interaction entre utilisateurs, pas d'achat. Résultat attendu : PEGI 3 / Tous publics.

## 5. Build de production (TOI)

```bash
npm install                       # sur ta machine (réinstalle les binaires natifs)
npx expo prebuild --platform android   # optionnel : régénère android/ avec les plugins
npx expo run:android --variant release # test local en release
# Build signé pour le Play Store :
cd android && ./gradlew bundleRelease   # produit app/build/outputs/bundle/release/app-release.aab
```

Ou avec EAS : `npx eas build --platform android --profile production`.

Rappels :
- `versionCode` doit être incrémenté à chaque envoi (app.json / build.gradle).
- Signe avec ta clé d'upload enregistrée dans la Play Console (Play App Signing).

## 6. Fiche Play Store (TOI)

À préparer : titre (30 c.), description courte (80 c.), description longue,
icône 512×512, bannière 1024×500, au moins 4 captures d'écran téléphone
(+ tablettes recommandé). Les captures peuvent être prises depuis un émulateur
une fois le rendu Skia en place.

## 7. Vérifications avant envoi

- [ ] `npx tsc --noEmit` sans erreur
- [ ] `npm test` vert
- [ ] `npx expo-doctor@latest`
- [ ] `.env` rempli (APP_ID, classements, succès, URLs) — jamais committé
- [ ] Page contact.html en ligne (wordsleuth.mywordgame@gmail.com)
- [ ] Pages légales en ligne et URLs correctes dans .env
- [ ] Connexion Play Jeux testée sur un appareil réel avec un compte testeur
- [ ] Politique de confidentialité + suppression de compte renseignées dans la Play Console
