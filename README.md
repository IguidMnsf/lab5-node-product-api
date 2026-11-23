# LAB5 — API REST de produits avec Node.js et Express

Ce projet réalise une API REST complète pour gérer des produits. Il suit une architecture en couches et permet de créer, lire, modifier partiellement, remplacer et supprimer des produits.

Les données sont conservées **uniquement en mémoire**. Elles reviennent à leurs trois valeurs initiales au redémarrage du serveur.

Le rapport est disponible dans [rapport-lab5.md](rapport-lab5.md) et [rapport-lab5.pdf](rapport-lab5.pdf).

## Ce qui est livré

- API Express avec modules ECMAScript.
- Architecture Routes, Controllers, Services, Repositories, Models et Middlewares.
- Validation des entrées avec `express-validator`.
- Gestion centralisée des réponses d'erreur.
- Documentation interactive Swagger/OpenAPI.
- Collection Postman prête à importer.
- Tests automatisés avec Node.js.
- Workflow GitHub Actions pour vérifier le projet.

## Prérequis

Installez les outils suivants :

- **Node.js 18 ou supérieur** ; Node.js 20 est recommandé.
- **npm** fourni avec Node.js.
- **Postman** ou `curl`.
- **Visual Studio Code** ou un autre éditeur.

Sous Windows, vérifiez l'installation depuis PowerShell :

```powershell
node --version
npm --version
```

Si une de ces commandes n'est pas reconnue, installez Node.js depuis https://nodejs.org/, puis redémarrez le terminal.

## Télécharger le projet

### Depuis GitHub

Dans PowerShell :

```powershell
git clone https://github.com/IguidMnsf/backend-labs.git
Set-Location .\backend-labs
```

Pour mettre à jour un clone existant :

```powershell
Set-Location "$HOME\backend-labs"
git pull --ff-only
```

Dans ce projet, les fichiers du LAB5 se trouvent dans :

```text
lab5-node-product-api
```

## Installation des dépendances

Depuis la racine du dépôt, accéder au dossier du laboratoire et installer les dépendances :

```powershell
Set-Location .\lab5-node-product-api
npm ci
```

La commande `npm ci` installe les versions enregistrées dans `package-lock.json`, notamment :

- `express` : serveur HTTP et routage.
- `dotenv` : chargement du fichier `.env`.
- `express-validator` : validation des entrées.
- `swagger-ui-express` : interface Swagger.
- `swagger-autogen` : génération du fichier OpenAPI.
- `supertest` : tests HTTP automatisés.

## Configuration du port

Le serveur utilise le port `3000` par défaut.

Pour créer un fichier `.env` à partir de l'exemple :

```powershell
Copy-Item .env.example .env
```

Le contenu est :

```dotenv
PORT=3000
```

Pour utiliser un autre port, modifier uniquement la valeur dans `.env`, par exemple `PORT=4000`. Toutes les adresses locales devront alors utiliser `4000`.

## Lancer le serveur

Depuis `lab5-node-product-api` :

```powershell
npm run dev
```

Le mode `dev` utilise `node --watch` : Express redémarre automatiquement après une modification.

Le serveur peut également être démarré sans le mode de surveillance :

```powershell
npm start
```

Lorsque le serveur démarre, des messages indiquent les adresses de l'API et de Swagger.

Vérifier le serveur dans un navigateur :

- `http://localhost:3000/`
- `http://localhost:3000/api/products`
- `http://localhost:3000/api-docs`
- `http://localhost:3000/api-docs.json`

Pour arrêter le serveur, utiliser `Ctrl+C` dans le terminal.

## Générer Swagger

Le document OpenAPI est fourni dans `src/docs/swagger-output.json` et chargé au démarrage. Pour le régénérer après une modification des routes ou des modèles :

```powershell
npm run swagger
```

Cette commande met à jour `src/docs/swagger-output.json`. L'interface Swagger lit ce fichier à l'adresse `http://localhost:3000/api-docs`.

## Endpoints disponibles

| Méthode | URL | Action | Résultat |
|---|---|---|---|
| GET | `/` | Vérifier que le serveur fonctionne | `200` |
| GET | `/api/products` | Lister les produits | `200` |
| GET | `/api/products/:id` | Lire un produit | `200` ou `404` |
| POST | `/api/products` | Créer un produit | `201` |
| PUT | `/api/products/:id` | Remplacer complètement un produit | `200` ou `404` |
| PATCH | `/api/products/:id` | Modifier seulement certains champs | `200` ou `404` |
| DELETE | `/api/products/:id` | Supprimer un produit | `204` ou `404` |

Corps JSON attendu pour POST et PUT :

```json
{
  "name": "Écran 24 pouces",
  "price": 199.99,
  "stock": 10
}
```

Corps JSON pour PATCH :

```json
{
  "stock": 20
}
```

Les règles principales sont :

- `name` : chaîne non vide.
- `price` : nombre positif ou nul.
- `stock` : entier positif ou nul.
- `id` : entier supérieur ou égal à 1.

## Tester avec Postman

1. Lance l'API avec `npm run dev`.
2. Ouvre Postman.
3. Choisis **Import**.
4. Importe `postman/LAB5-Product-API.postman_collection.json`.
5. Vérifie que la variable `baseUrl` vaut `http://localhost:3000`.
6. Lance d'abord **3. Créer un produit**.
7. La requête sauvegarde automatiquement l'identifiant créé dans `productId`.
8. Lance ensuite les autres requêtes dans l'ordre.
9. Lance **10. Supprimer le produit créé** en dernier.

Swagger UI est également disponible à l'adresse :

`http://localhost:3000/api-docs`

## Tester avec curl sous Windows

Le dépôt indique `curl.exe` pour éviter les alias PowerShell.

### Vérifier le serveur

```powershell
curl.exe http://localhost:3000/
```

### Lister les produits

```powershell
curl.exe http://localhost:3000/api/products
```

### Lire le produit 1

```powershell
curl.exe http://localhost:3000/api/products/1
```

### Créer un produit

```powershell
curl.exe -X POST http://localhost:3000/api/products -H "Content-Type: application/json" -d '{"name":"Tapis","price":10,"stock":5}'
```

### Remplacer complètement le produit 1

```powershell
curl.exe -X PUT http://localhost:3000/api/products/1 -H "Content-Type: application/json" -d '{"name":"Clavier Gamer","price":80,"stock":10}'
```

### Modifier seulement le stock du produit 1

```powershell
curl.exe -X PATCH http://localhost:3000/api/products/1 -H "Content-Type: application/json" -d '{"stock":20}'
```

### Supprimer le produit 3

```powershell
curl.exe -X DELETE http://localhost:3000/api/products/3
```

## Tester les erreurs

### Identifiant invalide

```powershell
curl.exe -i http://localhost:3000/api/products/1abc
```

Réponse attendue : `400 Bad Request`.

### Produit inexistant

```powershell
curl.exe -i http://localhost:3000/api/products/999
```

Réponse attendue : `404 Not Found`.

### Corps invalide

```powershell
curl.exe -i -X POST http://localhost:3000/api/products -H "Content-Type: application/json" -d '{"name":"","price":-1,"stock":0}'
```

Réponse attendue : `400 Bad Request` avec les détails des champs invalides.

### Route inconnue

```powershell
curl.exe -i http://localhost:3000/api/unknown
```

Réponse attendue : `404 Not Found` avec le message « Route non trouvée ».

## Exécuter les tests automatisés

Depuis `lab5-node-product-api` :

```powershell
npm test
```

Les tests vérifient notamment :

- la route de bienvenue ;
- le cycle CRUD complet ;
- l'identifiant invalide ;
- le produit inexistant ;
- les données invalides ;
- les routes inconnues ;
- la disponibilité de Swagger JSON.

## Architecture du projet

```text
lab5-node-product-api/
├── src/
│   ├── controllers/
│   │   └── product.controller.js
│   ├── docs/
│   │   └── swagger-output.json
│   ├── middlewares/
│   │   ├── error.middleware.js
│   │   └── validator.middleware.js
│   ├── models/
│   │   └── product.model.js
│   ├── repositories/
│   │   └── product.repository.js
│   ├── routes/
│   │   └── product.routes.js
│   ├── services/
│   │   └── product.service.js
│   └── server.js
├── postman/
│   └── LAB5-Product-API.postman_collection.json
├── test/
│   └── product-api.test.js
├── swagger.js
├── package.json
└── README.md
```

Le fonctionnement suit ce chemin :

**requête HTTP → route → middleware de validation → controller → service → repository → tableau en mémoire → réponse JSON**

Les erreurs remontent vers le middleware centralisé `error.middleware.js`, ce qui évite de dupliquer la gestion des exceptions dans chaque endpoint.

## Dépannage

### `npm` n'est pas reconnu

1. Installer Node.js depuis https://nodejs.org/.
2. Fermer les terminaux ouverts.
3. Ouvrir une nouvelle fenêtre PowerShell.
4. Vérifier `node --version` et `npm --version`.

### Le port 3000 est déjà utilisé

Modifier `PORT` dans `.env` :

```dotenv
PORT=4000
```

Puis relancer `npm run dev` et utiliser `http://localhost:4000`.

### Swagger ne s'affiche pas

Exécuter :

```powershell
npm run swagger
npm run dev
```

Puis ouvrir `http://localhost:3000/api-docs` ou vérifier `src/docs/swagger-output.json`.

### Les modifications disparaissent après redémarrage

C'est normal pour le LAB5 : le repository utilise un tableau JavaScript en mémoire. L'ajout d'une vraie base de données fera partie d'un laboratoire suivant.

## Résultat attendu

**Résultat attendu :** une API Node.js exécutable, une structure modulaire, une validation des entrées, une gestion centralisée des erreurs et une documentation Swagger interactive. Le code, les tests, la collection Postman et la documentation sont consultables sur GitHub.

## Accéder au projet local et le lancer avec PowerShell

Les commandes supposent un clone sous `$HOME\backend-labs`. Si le dépôt se trouve ailleurs, remplacer ce chemin par son emplacement réel. Pour un clone existant, ouvrir PowerShell, mettre le dépôt à jour, entrer dans le LAB5 et installer les dépendances :

```powershell
Set-Location "$HOME\backend-labs"
git pull --ff-only
Set-Location .\lab5-node-product-api
Get-Location
npm ci
```

Si le dépôt n'est pas encore cloné :

```powershell
Set-Location $HOME
git clone https://github.com/IguidMnsf/backend-labs.git
Set-Location .\backend-labs\lab5-node-product-api
npm ci
```

Vérifier Node.js et npm, puis démarrer le serveur :

```powershell
node --version
npm --version
npm run dev
```

Garder le terminal ouvert. Quand le serveur démarre, ouvrir `http://localhost:3000/api/products` dans un navigateur. Pour arrêter le serveur, utiliser `Ctrl+C`.

## Postman expliqué pas à pas

Postman envoie une requête au serveur qui tourne dans le terminal. Il ne lance pas `npm run dev`.

1. Ouvrir Postman Desktop et cliquer sur **Import** → **Files**.
2. Dans le clone local, ouvrir `lab5-node-product-api\postman` et importer `LAB5-Product-API.postman_collection.json`.
3. Dans **Collections**, ouvrir **LAB5 - API Produits Node.js Express** et vérifier que `baseUrl` vaut `http://localhost:3000`.
4. Dans un terminal PowerShell séparé, démarrer `npm run dev` et le laisser ouvert.
5. Dans Postman, ouvrir **1. Vérifier le serveur**, puis cliquer sur **Send**. Le résultat attendu est `200 OK`.
6. Ouvrir **3. Créer un produit** et cliquer sur **Send**. Vérifier `201 Created` et le JSON du produit dans l'onglet **Body**. L'identifiant est enregistré automatiquement.
7. Envoyer ensuite les requêtes de liste, lecture, PUT et PATCH. Les onglets **Body** et **Test Results** montrent respectivement la réponse et les assertions.
8. Les requêtes d'erreur peuvent être exécutées ensuite. Envoyer **10. Supprimer le produit créé** en dernier.
9. Pour exécuter toutes les requêtes dans l'ordre, cliquer sur **Run** dans la collection.

`localhost` désigne l'ordinateur local : cette URL fonctionne uniquement lorsque le serveur est démarré. Si Postman Web n'accède pas à cette adresse, sélectionner l'agent Desktop/local ou utiliser Postman Desktop.

## Captures d'écran pour le professeur

Utiliser `Win + Shift + S`, sélectionner la zone, puis choisir **Enregistrer sous** dans l'outil Capture d'écran. Enregistrer les images dans un dossier dédié, par exemple `Documents\Captures-Labs\LAB5`.

**Captures en local :**

1. PowerShell dans `lab5-node-product-api` avec le serveur en cours d'exécution.
2. Postman sur **1. Vérifier le serveur**, avec `localhost:3000` et `200 OK`.
3. Postman sur **3. Créer un produit**, avec `201 Created` et le JSON visible.
4. Swagger dans le navigateur à `http://localhost:3000/api-docs`.
5. Facultatif : PowerShell après `npm test` avec tous les tests réussis.

**Captures du projet publié sur GitHub :**

1. Ouvrir [les fichiers LAB5 sur GitHub](https://github.com/IguidMnsf/backend-labs/tree/main/lab5-node-product-api).
2. Capturer le README et la liste des fichiers du dossier.
3. Dans **Actions**, ouvrir un run récent et capturer le job `lab5-node` terminé avec succès.

La publication GitHub et la réussite des tests ne mettent pas l'API en ligne. L'API s'exécute localement et utilise `localhost:3000`.

## Dépannage pas à pas

**`node` ou `npm` n'est pas reconnu**

1. Installer Node.js 20 LTS (Node.js 18 ou supérieur est requis).
2. Fermer puis rouvrir PowerShell.
3. Vérifier `node --version` et `npm --version`.
4. Si les commandes restent introuvables, vérifier que le dossier d'installation de Node.js figure dans le `PATH` Windows.

**`npm ci` affiche `ENOENT`, `package.json not found` ou `lock file`**

La commande doit être exécutée dans le dossier du LAB5, celui qui contient `package.json` et `package-lock.json` :

```powershell
Set-Location "$HOME\backend-labs\lab5-node-product-api"
Get-Location
Test-Path .\package.json
Test-Path .\package-lock.json
npm ci
```

Les deux vérifications `Test-Path` doivent afficher `True`. Si le dépôt se trouve ailleurs, utiliser son chemin réel. Si une erreur de téléchargement apparaît, vérifier la connexion Internet puis relancer `npm ci`.

**Le serveur affiche `EADDRINUSE` ou le port 3000 est déjà utilisé**

Arrêter l'ancien serveur avec `Ctrl+C` dans son terminal. Sinon, créer ou modifier `.env` dans le dossier du LAB5 et choisir `PORT=4000`. Relancer `npm run dev`, puis utiliser `http://localhost:4000` dans Postman et le navigateur.

**Le serveur démarre mais Postman ne reçoit pas de réponse**

1. Garder ouvert le terminal exécutant `npm run dev`.
2. Dans une deuxième fenêtre PowerShell, exécuter `curl.exe -i http://localhost:3000/`.
3. Si le port a été modifié, remplacer `3000` dans cette commande et vérifier `baseUrl` dans la collection.
4. Si le navigateur ou `curl.exe` fonctionne mais pas Postman Web, utiliser Postman Desktop ou sélectionner l'agent Desktop/local.

**Postman reçoit `400 Bad Request`**

Vérifier que **Body → raw → JSON** est sélectionné et que le JSON est syntaxiquement valide. Pour `POST` et `PUT`, les champs `name`, `price` et `stock` doivent respecter les règles indiquées dans ce guide ; pour `PATCH`, fournir au moins un champ modifiable.

**Postman reçoit `404 Not Found` ou ne retrouve pas un identifiant créé**

1. Exécuter d'abord **3. Créer un produit** et vérifier `201 Created`.
2. Vérifier que le script a enregistré l'identifiant dans `productId`.
3. Envoyer les requêtes de lecture et modification avant **10. Supprimer le produit créé**.
4. Après un redémarrage du serveur, les données sont réinitialisées : recréer le produit. Ce laboratoire utilise un tableau en mémoire.

**Swagger affiche une erreur ou une documentation vide**

Depuis `lab5-node-product-api`, exécuter `npm run swagger`, puis redémarrer `npm run dev`. Vérifier ensuite `http://localhost:3000/api-docs` et `http://localhost:3000/api-docs.json`. En cas de port modifié, employer le nouveau port. Le fichier `src/docs/swagger-output.json` est le document OpenAPI généré.

**`npm test` échoue**

1. Arrêter le serveur local si nécessaire, vérifier que PowerShell est dans `lab5-node-product-api`, puis exécuter `npm ci`.
2. Relancer `npm test` et lire le premier échec de test.
3. Les tests utilisent Supertest et démarrent l'application de test eux-mêmes ; il n'est pas nécessaire de laisser `npm run dev` ouvert.
4. Si un test lit les données modifiées, le redémarrer après une nouvelle exécution complète des tests ; les tests initialisent leur jeu de données.

**Les modifications ne restent pas après fermeture du terminal**

C'est normal : les produits sont conservés en mémoire. Toute redémarrage restaure les données initiales ; la persistance dans une base de données ne fait pas partie de ce laboratoire.
