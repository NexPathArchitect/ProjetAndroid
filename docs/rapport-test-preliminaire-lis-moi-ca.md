# Rapport de test préliminaire — Lis-moi ça

*Tests préliminaires de reconnaissance d'étiquettes et de validation de l'API de Santé Canada*

Projet final — Application Android · Équipe de 2 développeurs · Test effectué le 12 novembre 2026 · Échantillon réduit

## 1. Objectif du test

Avant de commencer le développement, nous avons voulu vérifier le point le plus risqué du projet : la lecture fiable des étiquettes de médicaments et de compléments alimentaires par caméra, puis la reconnaissance du produit auprès de la base de données de Santé Canada. Les autres pièces de l'application (base de données locale, rappels, interface) reposent sur des compétences déjà maîtrisées et n'ont pas nécessité de vérification préalable.

## 2. Matériel et méthode

- Deux types de produits réels : un médicament remis par la pharmacie à la suite d'une consultation à l'hôpital (soupçon d'appendicite), indiqué contre les ballonnements et la douleur ; et un complément alimentaire du commerce.
- Photos prises dans une pièce peu éclairée, d'abord sans flash, puis avec flash, sur pot rond et boîte.
- Lecture visuelle avec Google Lens dans l'application Google, comme indicateur rapide avant le développement : cette technologie est proche de la reconnaissance ML Kit que nous prévoyons d'utiliser dans l'application.
- Requêtes manuelles à l'API de la Base de données sur les produits pharmaceutiques (DPD) de Santé Canada, dans le navigateur, en format JSON et en langue française, à partir du numéro d'identification lu sur l'étiquette.
- Pour chaque essai, nous avons noté le type de produit, l'éclairage, la présence ou non d'un numéro exploitable et le nombre de photos nécessaires.

## 3. Résultats

### 3.1 Lecture des étiquettes

| Produit | Éclairage | Nom lu | Posologie lue | Numéro lu / remarques |
|---|---|---|---|---|
| Médicament de pharmacie (pot rond) | Faible | Nom du médicament lu | Posologie lue sur l'étiquette | Numéro à 8 chiffres (DIN) imprimé à la verticale sous le code-barres, sans le mot « DIN » ; le dosage n'est apparu que sur une deuxième photo en tournant le pot. |
| Complément alimentaire du commerce | Faible | Nom du produit lu | Dosage lu | Numéro de licence (NPN ou DIN) situé sur une autre face de l'emballage. |
| Même médicament, avec flash | Avec flash | — | — | Un chiffre du numéro a été lu par erreur (un 8 interprété comme un 3) ; le numéro erroné n'est pas reconnu par l'API. |

### 3.2 Requêtes à l'API DPD

| Produit testé | Données renvoyées par l'API | Comparaison avec l'étiquette | Résultat |
|---|---|---|---|
| Médicament de pharmacie (numéro valide) | Nom de marque, fabricant, ingrédient actif, dose et unité | Les informations correspondent à l'étiquette | Produit identifié |
| Numéro erroné issu de la lecture au flash | Liste vide, aucun produit | — | Numéro invalide, correctement rejeté |

Pour un numéro valide, l'API renvoie le nom de marque, le fabricant, un code produit, ainsi que le nom de l'ingrédient actif, la force et l'unité. Aucune posologie (fréquence ou moment de prise) n'est fournie par l'API : elle provient de l'étiquette de pharmacie.

### 3.3 Cas d'erreur rencontrés

- Erreur de lecture d'un chiffre du numéro au flash : le numéro erroné n'est pas reconnu par l'API, qui retourne une liste vide, ce qui permet de le rejeter automatiquement.
- Le numéro d'ordonnance figure aussi sur l'étiquette, au format 7 chiffres-tiret-4 chiffres ; il ne doit pas être confondu avec le DIN, qui compte 8 chiffres.

## 4. Constats et contraintes de conception

- Pot rond : aucune photo ne contient tout l'étiquetage. Nous prévoyons une analyse continue des images (CameraX ImageAnalysis + reconnaissance ML Kit) et l'accumulation des fragments de texte pendant que l'utilisateur tourne le pot.
- Extraction du numéro : chercher tout nombre de 8 chiffres dans le texte reconnu, puis le valider auprès de l'API ; exclure les numéros d'ordonnance.
- Validation croisée : comparer le nom lu sur l'étiquette au nom renvoyé par l'API ; n'accepter un numéro que s'il est lu identiquement sur au moins trois images.
- Normalisation des unités : regrouper UI / IU / UNIT / Unité et nettoyer les espaces dans les nombres.
- Dictionnaire de posologie : traduire les abréviations courantes (DÉJEUNER, SOUPER, HS, PRN, SEMAINE, TOUJOURS) en règles de rappel.
- Code-barres de pharmacie : il est probablement différent du DIN ; nous pourrons l'associer au produit dans la base locale pour une reconnaissance rapide.
- L'API ne fournit pas la posologie : celle-ci sera lue sur l'étiquette de pharmacie et confirmée par l'utilisateur.
- Google Lens résume et filtre le texte ; l'évaluation finale se fera avec l'exemple officiel ML Kit (vision-quickstart), qui affiche le texte brut en direct.

## 5. Tests restants

**Prioritaires**

- Autres produits : boîte sans ordonnance (antalgique ou produit courant), complément alimentaire du commerce avec NPN, pot d'une autre pharmacie (Pharmaprix, Uniprix, Familiprix).
- Lecture du code-barres de pharmacie pour vérifier son contenu.
- Reconfirmation qu'un numéro erroné retourne une liste vide (déjà constaté lors du présent test).

**Très utiles**

- Comparaison avec Google Lookout sur les mêmes produits, dans le cadre de l'analyse concurrentielle.
- Test de la synthèse vocale en français sur nombres, doses et unités, ainsi que sur le nom du produit.
- Reconnaissance vocale hors ligne : commandes « oui », « relis », « allume la lumière », avec bruit de fond et sans connexion Internet.

**Bonus**

- Observer un aîné utiliser Lookout sur ses propres produits.
- Refaire les tests sur un second téléphone pour comparer les caméras.

## 6. Critère de décision

Critère retenu : numéro trouvé dans 70 % ou plus des cas en bonne lumière → le projet se poursuit ; entre 40 % et 70 % → il se poursuit, avec la recherche par nom du produit comme plan B ; sous 40 % → le projet doit être repensé.

Sur ces premiers produits, la lecture du nom et de la posologie est possible en lumière faible, et la chaîne numéro → API → produit fonctionne pour le cas valide ; le numéro erroné est bien rejeté par l'API. Ces résultats sont encourageants, mais l'échantillon est encore réduit.

## 7. Verdict préliminaire et mesures prévues

> Le projet est viable : les contraintes rencontrées sont toutes traitées par des mesures concrètes, et il ne s'agit pas d'un projet à abandonner. Ce verdict reste préliminaire, car l'échantillon est petit ; la décision finale de poursuivre ou non sera prise après le test d'une vingtaine de produits.

| Contrainte | Niveau | Solution prévue |
|---|---|---|
| Étiquette sur pot rond, DIN vertical, faible lumière | Moyen | Analyse continue des images (CameraX ImageAnalysis), accumulation du texte pendant la rotation ; guidage visuel à l'écran ; accord sur au moins trois images identiques. |
| Erreur de lecture d'un chiffre du DIN (8 lu comme 3) | Moyen | Extraction par expression régulière à 8 chiffres, validation par l'API et concordance du nom entre l'étiquette et l'API ; en cas de doute, nouvelle capture. |
| Unités et nombres difficiles à lire (UI/IU, 10 000, mg) | Faible | Normalisation avant la lecture : dictionnaire d'unités, suppression des espaces, conversion du nombre en lecture française. |
| L'API ne fournit pas la posologie | Faible | La posologie est lue sur l'étiquette de pharmacie et confirmée par l'utilisateur avant l'enregistrement du rappel ; dictionnaire d'abréviations. |
| Reconnaissance vocale hors ligne instable en français | Moyen | Pas de voix hors ligne engagée en version de base : interaction principale par gros boutons et TalkBack ; les commandes vocales sont une option ultérieure. |
| Suppléments avec NPN | Faible | Base de produits naturels distincte ; reportés à une itération ultérieure. |

## 8. Prochaines étapes

- Installer l'exemple officiel ML Kit sur un vrai téléphone et refaire la série de tests sur environ 20 produits.
- Réaliser un mini-prototype d'un seul écran : caméra → OCR → extraction du numéro → appel API → lecture à voix haute du nom.
- Répartir les tests restants entre les deux membres de l'équipe et conserver toutes les captures, en masquant les noms de patients.
