# Lis-moi ça — Rapport des tests de faisabilité

**Projet final — Développement mobile Android (A26)**
**Période des tests :** 12 au 21 novembre 2026
**Statut :** tests de faisabilité terminés (lecture, API, voix). Reste : reconnaissance vocale, comparaison avec les concurrents.

---

## 1. Objectif

Avant de coder, vérifier que les trois piliers techniques de l'app fonctionnent avec de **vrais produits** :

1. **Lire** une étiquette de médicament avec la caméra (ML Kit)
2. **Identifier** le produit dans les bases officielles de Santé Canada (DPD et LNHPD)
3. **Lire à voix haute** le résultat de façon compréhensible pour un aîné (TextToSpeech)

## 2. Matériel et outils

| Élément | Détail |
|---|---|
| Téléphone | Samsung Galaxy S25 Ultra (Android, One UI 9) |
| Outil OCR | App d'exemple officielle ML Kit (`googlesamples/mlkit`, `android/vision-quickstart`), compilée avec Android Studio |
| Comparaison | Google Lens (mode IA) |
| API médicaments | Santé Canada — Base de données sur les produits pharmaceutiques (DPD) |
| Base produits naturels | Santé Canada — Base de données des produits de santé naturels homologués (LNHPD) |
| Voix | Synthèse vocale de Google Traduction (voix française) |

Produits testés : 2 pots d'ordonnance (Jean Coutu), 1 pot de fabricant avec étiquette d'une autre pharmacie, 1 pot sans ordonnance (marque maison), 1 produit naturel (vitamine D).
Les noms de patients ont été masqués sur toutes les captures.

---

## 3. Résultats — Lecture des étiquettes (OCR)

### 3.1 Google Lens (12 novembre)

| Produit | Nom | Dosage | Posologie | DIN |
|---|---|---|---|---|
| Apo-Indapamide (ordonnance) | ✅ | ✅ sur une 2e photo seulement | ✅ | ✅ près du code-barres |
| D-Tabs 10 000 UI (ordonnance) | ✅ | ✅ | ✅ | ✅ sur une autre face |
| D-Tabs avec flash | ✅ | ✅ | ✅ | ❌ lu `00321772` au lieu de `00821772` |

**Constat :** Lens résume et filtre le texte avec son IA. Ce n'est pas une mesure fiable de l'OCR brut → tests refaits avec ML Kit.

### 3.2 ML Kit — Analyse en direct (CameraX)

| Test | Résolution analysée | Images/s | DIN | Dosage | Ingrédient | Bruit |
|---|---|---|---|---|---|---|
| Dexilant, de loin | 640×480 | 25 | ✅ (lu « bIN ») | ⚠️ « 30- » | ❌ | clavier lu |
| Dexilant, de loin | 1088×1088 | 5 | ✅ | ✅ | ✅ | clavier lu |
| Dexilant, pot remplissant l'écran | 1088×1088 | 7 | ✅ | ✅ | ✅ | **aucun** |
| Étiquette de pharmacie (autre pharmacie) | 1088×1088 | 6 | — | ✅ | — | — |
| Code-barres UPC vertical (Barcode Scanning) | 1088×1088 | 13 | — | — | — | lu instantanément |
| Acétaminophène, faible lumière | 1088×1088 | 3 | ❌ | ❌ | — | mode d'emploi illisible |

- La résolution analysée en direct plafonne à **1088×1088** sur ce téléphone, peu importe le réglage choisi.
- Sur l'étiquette de pharmacie : posologie (en anglais), date, numéro d'ordonnance et téléphone de la pharmacie bien lus.

### 3.3 ML Kit — Photo fixe (StillImageActivity, 1440×1080)

| Produit | Résultat |
|---|---|
| Acétaminophène 500 mg (avant) | ✅ **DIN minuscule et vertical trouvé** (`00605778`, lu avec des O au lieu de 0), nom, 500 mg |
| Acétaminophène 500 mg (mode d'emploi) | ⚠️ titres et début de posologie lus, fins de lignes coupées par la courbe ; UPC lu correctement |
| Vitamine D3 Jamieson (avant, anglais et français) | ✅ marque, D3, 1 000 UI (une fois lu « 1U »), allégation santé |
| Vitamine D3 Jamieson (côté) | ⚠️ NPN lu `800T6452` (T au lieu de 1) ; une 2e lecture à l'envers a donné `2991008 NdN` |

**Conclusion OCR :** analyse en direct pour la reconnaissance rapide ; **photo fixe + bonne lumière** pour enregistrer un nouveau médicament.

---

## 4. Résultats — API de Santé Canada

### 4.1 Médicaments (DPD)

| DIN | Résultat | Remarque |
|---|---|---|
| 00821772 | ✅ D-TABS, Laboratoire Riva | ingrédients : Vitamine D3, 10000 « Unité » |
| 02354950 | ✅ DEXILANT, Takeda Canada | |
| 00605778 | ✅ Comprimés d'acétaminophène extra-puissant 500 mg, Vita Health | marque **Kirkland** sur le pot, nom différent dans la base |
| 00321772 (DIN mal lu) | ✅ `[]` liste vide | **la validation détecte l'erreur** |

Requêtes utilisées :
- `GET https://health-products.canada.ca/api/drug/drugproduct/?din=<DIN>&lang=fr&type=json`
- `GET https://health-products.canada.ca/api/drug/activeingredient/?id=<drug_code>&lang=fr&type=json`

Observations :
- La réponse est une **liste** ; vide = DIN invalide.
- Le champ s'appelle `drug_identification_number` (pas `din`).
- La concentration est dans `strength` (texte) et `strength_unit` ; `dosage_value` et `dosage_unit` sont vides.
- L'API **ne donne pas la posologie** ni les effets secondaires.
- La base contient elle-même des fautes (« COMPIRMÉS ») → comparaison de noms tolérante.

### 4.2 Produits naturels (LNHPD)

| NPN | Résultat |
|---|---|
| 80016452 | ✅ Jamieson Laboratories, licence active, produit commercialisé |

La fiche contient : forme (comprimé), voie (orale), **ingrédient et quantité** (Vitamine D 25 mcg / 1000 UI), **dose recommandée** (adultes : 1 comprimé par jour), **usage recommandé approuvé**, ingrédients non médicinaux.
- Un même NPN regroupe **11 noms de produits** (saveurs, formats).
- Les informations sont **uniquement en anglais** (langue de soumission du fabricant).

---

## 5. Résultats — Synthèse vocale

| # | Texte | Résultat | Action dans l'app |
|---|---|---|---|
| 1 | Apo-Indapamide 1,25 mg | ✅ « milligramme » | aucune |
| 2 | D-TABS 10000 Unité | ⚠️ « D-T-A-B-S » (mais « dix mille unités » ✅) | noms de marque en minuscules |
| 3 | Vitamine D3 1 000 UI | ❌ « 1 zéro zéro zéro U-I » ; « 1000 » lu « dix zéro zéro » | nombres convertis en lettres + « unités internationales » |
| 4 | DIN 02354950 | ✅ « D-I-N zéro deux trois… » | séparer les chiffres par sécurité |
| 5 | 1 COMPRIMÉ SEMAINE TOUJOURS | ✅ | aucune |
| 6 | Prendre 1 comprimé 2 fois par jour | ✅ | aucune |
| 7 | TAKE 1 CAPSULE ONCE DAILY | ⚠️ « once » prononcé en français | voix anglaise ou dictionnaire de posologie |
| 8 | Dexlansoprazole | ✅ | aucune |

**Conclusion :** le texte lu ne doit jamais aller directement à la voix. Une fonction `preparerPourVoix()` est nécessaire.

---

## 6. Défis rencontrés et solutions retenues

| Défi | Cause | Solution retenue |
|---|---|---|
| Dosage invisible sur une seule photo | Courbure du pot rond | Lecture continue en tournant le pot, accumulation des fragments |
| DIN sans le mot « DIN », imprimé à la verticale | Mise en page des étiquettes | Regex `\b\d{8}\b` + validation par l'API |
| DIN mal lu (8 → 3) avec le flash | Reflet sur plastique brillant | Validation API, vérification croisée ingrédient + concentration, vote sur ≥ 3 images |
| Confusions lettres/chiffres (O/0, T/1, l/1) | Erreurs OCR | Table de correction avant validation |
| NPN lu à l'envers | Texte vertical | Analyser l'image tournée (90°, 180°, 270°) |
| Numéro d'ordonnance et numéro de lot | Autres nombres sur l'étiquette | Formats différents (Rx 6-7 chiffres, lot 10 chiffres) exclus par la regex |
| Chiffres d'UPC mal lus par l'OCR | Code-barres lu comme du texte | ML Kit Barcode Scanning |
| Pas de base publique UPC → médicament | — | Associer l'UPC au médicament dans la BD à l'enregistrement |
| Marque maison ≠ nom dans la base | Kirkland fabriqué par Vita Health | Comparer ingrédient actif + concentration, pas la marque |
| Petit texte dense illisible en direct | Résolution et lumière | Photo fixe + lampe à l'enregistrement, ou saisie par un proche aidant |
| Clavier et objets lus en arrière-plan | Champ de la caméra | Guider l'utilisateur pour que le pot remplisse l'écran |
| Unités variées (UI, IU, 1U, UNIT, Unité, mcg) | Étiquettes et base différentes | Normalisation |
| Posologie en anglais ou abrégée | Étiquettes de pharmacie | Dictionnaire bilingue (DÉJEUNER, HS, PRN, ONCE DAILY…) |
| Nombres mal lus par la voix | Espaces et contexte | Conversion des nombres en lettres (`RuleBasedNumberFormat`) |
| Fiches LNHPD en anglais seulement | Langue de soumission | Traduction ML Kit ou voix anglaise |
| L'IA de Lens donne des généralités médicales | Résumé IA | L'app lit seulement l'étiquette et les bases officielles |

### Défis d'environnement de développement

| Défi | Solution |
|---|---|
| Projet d'exemple incompatible avec Java 25 | Utilisation de la JVM 17 |
| Débogage USB grisé | Désactivation temporaire du Bloqueur automatique (Auto Blocker) Samsung |
| Débogage sans fil impossible sur le Wi-Fi du collège | Câble USB |
| Avast signale l'app de développement | Faux positif (app installée hors Play Store) |

---

## 7. Conclusion

Le cœur technique de **Lis-moi ça** est **validé avec de vrais produits** :

- ✅ ML Kit lit le DIN, le NPN, le nom, le dosage et la posologie, à condition de tourner le pot et d'avoir assez de lumière.
- ✅ Les deux bases de Santé Canada identifient les produits et **rejettent un DIN mal lu**.
- ✅ La synthèse vocale est compréhensible après une étape de préparation du texte.

**Limites assumées :** l'app ne sait qu'une prise a eu lieu que si l'utilisateur la confirme ; elle ne donne aucune information médicale qui ne vient pas de l'étiquette ou de Santé Canada ; elle demande toujours confirmation des doses lues.

## 8. Tests restants

- [ ] Reconnaissance vocale (« oui », « relis », « allume la lumière »…), avec bruit et en mode avion
- [ ] Comparaison avec Google Lookout et Bixby Vision
- [ ] Code-barres de l'étiquette de pharmacie Jean Coutu
- [ ] Test sur un téléphone moins récent
