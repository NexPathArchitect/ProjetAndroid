# ProjetAndroid — Lis-moi ça

**Assistant de lecture vocal et mémoire pour aînés malvoyants.**

Application Android qui lit à voix haute les étiquettes de médicaments et de compléments alimentaires à partir de la caméra, conserve l'historique des lectures, suit les médicaments et envoie des rappels de prise.

## Public cible

Personnes âgées malvoyantes et leur famille aidante.

## Fonctionnalités principales

- Lecture vocale des étiquettes (OCR hors ligne) et historique réécoutable, classé par catégorie et en favoris.
- Reconnaissance du médicament par DIN via l'API DPD de Santé Canada : nom, fabricant, ingrédient actif et dose.
- Rappels de prise avec bouton « Pris » ; avertissement en cas de double dose.
- Accessibilité : commandes vocales, gros boutons, compatibilité TalkBack.

## Technologies

Kotlin, Jetpack Compose, CameraX, ML Kit (OCR), Text-to-Speech, Room, WorkManager, Retrofit, API DPD de Santé Canada.

## État du projet

Tests de faisabilité préliminaires effectués ; le mini-prototype d'écran unique et le code de l'application sont à venir.

## Documentation

| Fichier | Description |
|---|---|
| [`docs/proposition-lis-moi-ca.md`](docs/proposition-lis-moi-ca.md) | Proposition de projet : objectif, fonctionnalités, public cible et technologies. |
| [`docs/resume-faisabilite-lis-moi-ca.md`](docs/resume-faisabilite-lis-moi-ca.md) | Résumé de faisabilité : verdict, périmètre v1, risques et mesures, plan de 6 semaines. |
| [`docs/rapport-test-preliminaire-lis-moi-ca.md`](docs/rapport-test-preliminaire-lis-moi-ca.md) | Rapport de test préliminaire : lectures d'étiquettes, requêtes API, contraintes de conception, verdict préliminaire et mesures prévues. |
| [docs/rapport-faisabilite-lis-moi-ca.md](docs/rapport-faisabilite-lis-moi-ca.md) | Rapport de faisabilité : tests ML Kit (direct et photo), API DPD et LNHPD, synthèse vocale, défis et solutions. |

## Structure du dépôt (à venir)

- `app/` — code source de l'application Android (à développer)
- `docs/` — proposition et rapports de test
