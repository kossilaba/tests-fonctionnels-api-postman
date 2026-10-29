# Tests fonctionnels d’API REST avec Postman

![Postman](https://img.shields.io/badge/Postman-API%20Testing-orange) ![Statut](https://img.shields.io/badge/Campagnes%20validées-2-success) ![Langue](https://img.shields.io/badge/Documentation-Français-blue)

## Présentation

Mini-projet de **tests fonctionnels d’API REST** réalisé avec **Postman** et l’API publique [JSONPlaceholder](https://jsonplaceholder.typicode.com/). L’objectif est de transformer des besoins fonctionnels en critères d’acceptation, cas d’essais et vérifications automatisées, puis d’exécuter des campagnes de régression et des tests pilotés par des données CSV.

**Compétences mises en pratique :** analyse fonctionnelle, critères d’acceptation, conception de tests, HTTP/REST, JSON, assertions JavaScript dans Postman, tests de régression, tests pilotés par les données et traçabilité des résultats.

## Périmètre fonctionnel

| Histoire | Besoin | Méthode et point d’accès | Vérifications principales |
|---|---|---|---|
| US-01 | Consulter la liste des articles | `GET /posts` | HTTP 200, tableau JSON, 100 articles, champs obligatoires |
| US-02 | Consulter un article | `GET /posts/1` | HTTP 200, ID attendu, structure JSON |
| US-02 (négatif) | Consulter un article inexistant | `GET /posts/9999` | HTTP 404, objet JSON vide |
| US-03 | Filtrer les articles d’un auteur | `GET /posts?userId=1` | HTTP 200, 10 articles, auteur conforme |
| US-04 | Créer un article | `POST /posts` | HTTP 201, valeurs retournées, identifiant attribué |
| US-05 | Modifier un article | `PUT /posts/1` | HTTP 200, valeurs modifiées retournées |
| US-05 | Modifier partiellement un article | `PATCH /posts/1` | HTTP 200, titre modifié, identifiant inchangé |
| US-05 | Supprimer un article | `DELETE /posts/1` | HTTP 200, réponse JSON vide |
| US-04 (CSV) | Répéter la création avec plusieurs jeux de données | `POST /posts` | 5 itérations, correspondance des champs avec le CSV |

## Résultats des campagnes

| Campagne | Date | Périmètre | Assertions réussies | Échecs |
|---|---|---|---:|---:|
| Régression fonctionnelle | 29 octobre 2026 | 8 requêtes, 1 itération | **33** | **0** |
| Tests pilotés par les données | 29 octobre 2026 | 1 requête POST, 5 itérations | **25** | **0** |

**Total des deux campagnes ciblées : 58 assertions réussies, 0 échec.** Les chiffres correspondent à des **assertions**, et non à 58 scénarios distincts. Une campagne exploratoire supplémentaire de 9 requêtes × 5 itérations a aussi produit 190 assertions réussies ; elle est archivée séparément.

Les exports du Runner disponibles dans ce dépôt donnent les totaux agrégés, mais ne contiennent pas le détail des réponses ni les valeurs réellement injectées à chaque itération. Les résultats ne doivent donc pas être interprétés comme une preuve d’enregistrement persistant des articles.

## Structure du dépôt

```text
portfolio-tests-api-postman/
├── README.md
├── .gitignore
├── data/
│   └── donnees_tests_postman.csv
├── documentation/
│   └── Suivi_cas_essais_JSONPlaceholder.xlsx
├── postman/
│   ├── README.md
│   └── (exports Postman à ajouter)
├── rapports/
│   ├── regression_33_tests.json
│   ├── data_driven_25_tests.json
│   ├── campagne_complete_190_tests.json
│   └── execution_initiale_29_pass_4_fail.json
└── captures/
    └── README.md
```

## Reproduire les tests

1. Installer [Postman](https://www.postman.com/downloads/).
2. Dans Postman, exporter la collection **« JSONPlaceholder – Tests fonctionnels »** au format Collection v2.1 et l’environnement **« JSONPlaceholder »**, puis ajouter ces fichiers au dossier `postman/`.
3. Importer les deux fichiers dans Postman et sélectionner l’environnement **JSONPlaceholder**.
4. Vérifier que la variable d’environnement `baseUrl` vaut `https://jsonplaceholder.typicode.com`.
5. Pour la régression, exécuter les **8 requêtes fonctionnelles initiales** une seule fois. Résultat de référence : 33 assertions réussies.
6. Pour les tests pilotés par les données, sélectionner **uniquement la requête « US-04 – Création avec données CSV »**, charger `data/donnees_tests_postman.csv` dans Collection Runner et lancer **5 itérations**. Résultat de référence : 25 assertions réussies.

> **Attention :** le dépôt fourni initialement contient la documentation et les rapports, **pas encore les exports de la collection et de l’environnement**. Les étapes 2 à 6 nécessitent leur ajout préalable.

### Exemple de corps POST piloté par le CSV

```json
{
  "title": "{{title}}",
  "body": "{{body}}",
  "userId": {{userId}}
}
```

### Exemple d’assertion Postman

```javascript
pm.test("Le statut HTTP est 201", function () {
    pm.response.to.have.status(201);
});

const article = pm.response.json();
pm.test("Les données correspondent au fichier CSV", function () {
    pm.expect(article.title).to.eql(pm.iterationData.get("title"));
    pm.expect(article.body).to.eql(pm.iterationData.get("body"));
    pm.expect(article.userId).to.eql(Number(pm.iterationData.get("userId")));
});
```

## Limites et améliorations possibles

- **JSONPlaceholder simule les écritures** (`POST`, `PUT`, `PATCH`, `DELETE`) : les modifications ne sont pas conservées dans une vraie base de données.
- Les tests vérifient les réponses de cette API de démonstration, pas les règles métier ou la sécurité d’une API de production.
- Les rapports exportés contiennent des **totaux agrégés** ; ajouter des captures et des exports détaillés pour une meilleure traçabilité.
- Évolutions possibles : tests de données invalides sur une API qui les valide réellement, scénarios de bout en bout avec état persistant, exécution en ligne de commande avec Newman et intégration continue GitHub Actions.

## Livrables

- [Tableau de suivi des cas d’essais](documentation/Suivi_cas_essais_JSONPlaceholder.xlsx)
- [Jeux de données CSV](data/donnees_tests_postman.csv)
- [Rapport de régression : 33/33](rapports/regression_33_tests.json)
- [Rapport CSV : 25/25](rapports/data_driven_25_tests.json)
- [Consignes d’export Postman](postman/README.md)

## Contexte

Projet personnel de démonstration des pratiques d’**analyse fonctionnelle et de validation d’API**, réalisé le 29 octobre 2026.
