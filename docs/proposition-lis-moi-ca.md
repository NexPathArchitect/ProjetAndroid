# Proposition de projet — Lis-moi ça

> Format exigé : 1 page — nom, client, sac de technologies. Maximum 2 paragraphes.

---

## Nom de l'application
**Lis-moi ça** — Assistant de lecture vocal et mémoire pour aînés malvoyants.

## Client
**Aînés malvoyants** (personnes âgées à faible vision). Client : personnes âgées et leur famille aidante ; à adapter en organisme partenaire si besoin (ex. association de personnes âgées).

---

## Description (2 paragraphes)

Les personnes âgées malvoyantes rencontrent des difficultés quotidiennes pour lire les étiquettes, les lettres, les menus et surtout leurs médicaments. Des applications comme Google Lookout ou Seeing AI lisent très bien le texte, mais elles l'oublient aussitôt : elles ne mémorisent pas l'historique des lectures ni le suivi des médicaments. Notre application Android, **Lis-moi ça**, pointe la caméra vers n'importe quel document et lit le texte à voix haute, même hors ligne, grâce à l'OCR et à la synthèse vocale intégrés. Elle conserve un historique complet des lectures, classé par catégorie et en favoris, afin que l'utilisateur puisse réécouter une lettre, une étiquette ou un reçu sans avoir à rescanner.

Le volet médicaments constitue la valeur ajoutée du projet : on scanne la boîte, l'application retrouve le produit grâce à son numéro DIN dans l'API de Santé Canada, annonce le nom et la dose, enregistre la prise et envoie un rappel à l'heure prévue avec un simple bouton « Pris », tout en avertissant en cas de double dose. L'application est pensée dès le départ pour l'accessibilité : commandes vocales, gros boutons et compatibilité TalkBack. Réalisée par une équipe de 2 développeurs sur 6 semaines, une version de base complète (lecture, médicaments, rappels, historique) est livrée à la semaine 4 ; l'extension éventuelle vers les vitamines et suppléments naturels est documentée comme amélioration future.

---

## Full Dev Stack

| Élément | Technologie |
|---|---|
| Langage / UI | Kotlin, Jetpack Compose |
| Caméra | CameraX |
| OCR (hors ligne) | ML Kit Text Recognition |
| Synthèse vocale | Android Text-to-Speech |
| Base de données locale | Room (SQLite) |
| Tables | Profil, Lecture, Catégorie, Tag, Médicament, Rappel, PriseMédicament, ProduitRéférence + table de jonction LectureTag |
| API externe | API DPD (Drug Product Database) de Santé Canada — via Retrofit |
| Rappels / notifications | WorkManager |
| Accessibilité | TalkBack, commandes vocales, gros boutons |
| Cible | Appareils Android (smartphones) |

---

## Pourquoi l'extension « vitamines et suppléments » n'est pas dans la version de base
*(à garder dans le rapport, pas dans la proposition)*

- **Temps** : en 6 semaines, mieux vaut une app de base solide qu'une app à fonctions à moitié finies.
- **Histoire claire** : la présentation reste centrée sur un seul public (aînés malvoyants) et un seul problème.
- **API de plus à maîtriser** : la base des produits naturels (LNHPD) a sa propre structure à explorer.
- **Prudence** : annoncer des « bienfaits » exige de vérifier les sources pour ne jamais diffuser d'informations santé trompeuses.
- **Plan** : si la base est terminée à la semaine 4, l'extension devient le bonus de la semaine 5 ; sinon, elle figure dans le rapport comme amélioration future.

---

## Checklist des autres exigences du professeur

- [ ] Proposition écrite soumise **avant** de commencer le développement.
- [ ] Page unique : nom, client, sac de technologies (ci-dessus).
- [ ] **PowerPoint** de présentation du projet.
- [ ] **Vidéo de démonstration** de l'application (en complément du PPT).
- [ ] Chacun installe toutes les applications/outils nécessaires ; **au moins un PC fonctionnel** pour l'équipe.
- [ ] **Journal des défis techniques** : 1 résumé par semaine (défi + solution) — à garder aussi pour le portfolio de stage.

### Journal des défis et solutions (à remplir chaque semaine)

| Semaine | Défi rencontré | Solution retenue | Leçon |
|---|---|---|---|
| S1 | Structure du projet / navigation Compose | À remplir | |
| S2 | OCR ML Kit hors ligne + calibration caméra | À remplir | |
| S3 | Intégration API DPD Santé Canada (recherche par DIN) | À remplir | |
| S4 | Rappels WorkManager + notification avec bouton « Pris » | À remplir | |
| S5 | Avertissement double dose + compatibilité TalkBack | À remplir | |
| S6 | Démo vidéo + nettoyage du rapport | À remplir | |
