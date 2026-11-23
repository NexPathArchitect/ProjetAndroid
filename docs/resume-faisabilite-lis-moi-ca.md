# Résumé de faisabilité — Lis-moi ça

**Projet final — Développement mobile Android (A26)**
**Date :** 23 novembre 2026 · **Équipe :** 2 développeurs

## Verdict préliminaire

Le cœur technique de Lis-moi ça est validé avec de vrais produits : l'OCR, les deux bases de Santé Canada et la synthèse vocale fonctionnent sous réserve des mesures décrites ci-dessous. Le projet se poursuit ; ce verdict reste conditionné aux tests restants (reconnaissance vocale hors ligne, comparaison concurrentielle).

## Ce qui est validé

- **OCR (ML Kit, CameraX)** : lecture du DIN, NPN, nom, dosage et posologie sur étiquettes réelles, en analyse directe comme en photo fixe. L'analyse directe sert à la reconnaissance rapide ; la photo fixe en bonne lumière sert à enregistrer un nouveau médicament.
- **DPD (médicaments)** : quatre DIN validés, avec ingrédients et dose ; un DIN mal lu est correctement rejeté par une réponse vide.
- **LNHPD (produits naturels)** : un NPN retrouvé, avec ingrédient, quantité et dose recommandée ; les fiches sont en anglais seulement.
- **Synthèse vocale** : compréhensible après une étape de préparation du texte (nombres écrits en lettres, normalisation des unités, traitement des noms de marque).

## Ce qui reste à tester

- Reconnaissance vocale hors ligne (« oui », « relis », « allume la lumière »), avec bruit de fond et en mode avion.
- Comparaison avec Google Lookout et Bixby Vision sur les mêmes produits.
- Test sur un téléphone moins récent ; code-barres de l'étiquette de pharmacie.

## Périmètre de la version de base (v1)

- Lecture vocale des étiquettes et historique réécoutable, classé par catégorie et en favoris.
- Identification par DIN via la DPD : nom, fabricant, ingrédient actif et dose.
- Rappels de prise avec bouton « Pris » et avertissement en cas de double dose ; confirmation de la dose par l'utilisateur.
- Accessibilité : gros boutons, compatibilité TalkBack.

## Reporté à une itération ultérieure

- Intégration complète des suppléments par NPN (LNHPD) : possible en semaine 5 seulement si la version de base est terminée.
- Commandes vocales hors ligne : option ultérieure ; l'interaction de base repose sur les boutons et TalkBack.

## Principaux risques et mesures prévues

| Risque | Mesure |
|---|---|
| Étiquette sur pot rond, texte vertical, faible lumière | Lecture continue en tournant le pot, accumulation des fragments ; photo fixe et bonne lumière pour l'enregistrement |
| Erreurs de lecture du numéro (8↔3, O↔0, T↔1) | Regex 8 chiffres, validation par l'API, concordance nom + ingrédient + dose, vote sur au moins 3 images |
| Nombres et unités mal lus par la voix | Préparation du texte avant la synthèse : nombres en lettres, dictionnaire d'unités |
| Posologie absente de l'API | Lue sur l'étiquette de pharmacie, confirmée par l'utilisateur avant l'enregistrement du rappel |
| Reconnaissance vocale hors ligne instable | Boutons et TalkBack en priorité ; commandes vocales ajoutées plus tard |

Détails des tests et tableau complet des défis : [`rapport-faisabilite-lis-moi-ca.md`](rapport-faisabilite-lis-moi-ca.md).

## Plan de développement (6 semaines)

| Semaine | Objectif |
|---|---|
| S1 | Squelette Compose, schéma Room, client Retrofit DPD/LNHPD |
| S2 | Caméra + OCR, extraction DIN/NPN, validation API et croisement des noms |
| S3 | Enregistrement des produits, historique, préparation du texte et synthèse vocale |
| S4 | Rappels WorkManager, bouton « Pris », avertissement double dose ; version de base complète |
| S5 | Accessibilité (boutons, TalkBack), association des codes-barres ; suppléments LNHPD si le temps le permet |
| S6 | Tests complémentaires, correctifs, vidéo de démonstration, présentation |
