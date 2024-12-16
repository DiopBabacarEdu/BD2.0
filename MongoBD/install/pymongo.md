
# Tutoriel
Ce tutoriel est une introduction au travail avec MongoDB et PyMongo.

---

## Prérequis
Avant de commencer, assurez-vous que la distribution PyMongo est installée. Dans le shell Python, la commande suivante devrait s’exécuter sans générer d’exception :

```python
import pymongo
```

Ce tutoriel suppose également qu'une instance MongoDB est en cours d'exécution sur l'hôte et le port par défaut. Si MongoDB est installé et téléchargé, vous pouvez le démarrer avec la commande suivante :

```bash
$ mongod
```

---

## Établir une connexion avec `MongoClient`
La première étape pour travailler avec PyMongo est de créer un client MongoClient pour l'instance `mongod` en cours d'exécution. C’est très simple :

```python
from pymongo import MongoClient
client = MongoClient()
```

Le code ci-dessus se connectera à l'hôte et au port par défaut. Vous pouvez également spécifier explicitement l'hôte et le port :

```python
client = MongoClient("localhost", 27017)
```

Ou utiliser le format URI MongoDB :

```python
client = MongoClient("mongodb://localhost:27017/")
```

---

## Accéder à une base de données
Une instance unique de MongoDB peut prendre en charge plusieurs bases de données indépendantes. Pour accéder à une base de données avec PyMongo, utilisez la syntaxe d'attribut :

```python
db = client.test_database
```

Si le nom de votre base de données empêche l'accès par attribut (par exemple, `test-database`), utilisez l'accès par dictionnaire :

```python
db = client["test-database"]
```

---

## Accéder à une collection
Une collection est un groupe de documents stockés dans MongoDB, comparable à une table dans une base de données relationnelle. Pour obtenir une collection dans PyMongo, procédez comme suit :

```python
collection = db.test_collection
```

Ou utilisez l'accès par dictionnaire :

```python
collection = db["test-collection"]
```

À noter que les collections (et les bases de données) dans MongoDB sont créées de manière paresseuse. Aucune des commandes ci-dessus n'exécute réellement d'opérations sur le serveur MongoDB. Les collections et bases de données sont créées lorsqu'un premier document y est inséré.

---

## Documents
Les données dans MongoDB sont représentées et stockées sous forme de documents de style JSON. En PyMongo, nous utilisons des dictionnaires pour représenter ces documents. Par exemple, le dictionnaire suivant pourrait représenter un article de blog :

```python
import datetime
post = {
    "author": "Mike",
    "text": "Mon premier article de blog !",
    "tags": ["mongodb", "python", "pymongo"],
    "date": datetime.datetime.now(tz=datetime.timezone.utc),
}
```

Les documents peuvent contenir des types natifs Python (comme les instances `datetime.datetime`) qui seront automatiquement convertis vers et depuis les types BSON appropriés.

---

## Insérer un document
Pour insérer un document dans une collection, utilisez la méthode `insert_one()` :

```python
posts = db.posts
post_id = posts.insert_one(post).inserted_id
post_id
```

Lorsqu'un document est inséré, une clé spéciale `_id` est automatiquement ajoutée si le document n’en contient pas déjà une. Cette clé doit être unique dans la collection. `insert_one()` retourne une instance de `InsertOneResult`.

Après l’insertion du premier document, la collection `posts` est effectivement créée sur le serveur. Vérifiez cela en listant toutes les collections dans votre base de données :

```python
db.list_collection_names()
```

---

## Récupérer un document avec `find_one()`
La méthode de requête la plus basique dans MongoDB est `find_one()`. Elle retourne un document correspondant à la requête (ou `None` si aucun document ne correspond).

```python
import pprint
pprint.pprint(posts.find_one())
```

Le résultat est un dictionnaire correspondant à celui inséré précédemment.

---

## Requêtes par `ObjectId`
Il est possible de retrouver un document par son `_id` :

```python
post_id
pprint.pprint(posts.find_one({"_id": post_id}))
```

---

## Insertion en masse
Pour insérer plusieurs documents, utilisez la méthode `insert_many()` :

```python
new_posts = [
    {
        "author": "Mike",
        "text": "Un autre article !",
        "tags": ["bulk", "insert"],
        "date": datetime.datetime(2009, 11, 12, 11, 14),
    },
    {
        "author": "Eliot",
        "title": "MongoDB est amusant",
        "text": "Et assez simple aussi !",
        "date": datetime.datetime(2009, 11, 10, 10, 45),
    },
]
result = posts.insert_many(new_posts)
result.inserted_ids
```

---

## Requêtes avancées
Pour récupérer plusieurs documents, utilisez `find()` :

```python
for post in posts.find():
    pprint.pprint(post)
```

Pour filtrer les résultats :

```python
for post in posts.find({"author": "Mike"}):
    pprint.pprint(post)
```

---

## Comptage des documents
Pour compter les documents dans une collection :

```python
posts.count_documents({})
```

Pour compter uniquement ceux qui correspondent à une requête :

```python
posts.count_documents({"author": "Mike"})
```

---

## Indexation
Les index permettent d’accélérer les requêtes et d’ajouter des fonctionnalités supplémentaires. Par exemple, créez un index unique sur un champ :

```python
result = db.profiles.create_index([("user_id", pymongo.ASCENDING)], unique=True)
```

---

Pour plus d’informations, consultez la documentation officielle MongoDB.


### Informations sur les Index

Pour lister les informations sur les index dans une collection :

```python
sorted(list(db.profiles.index_information()))
['_id_', 'user_id_1']
```

Nous avons maintenant deux index :
- Un index sur `_id`, créé automatiquement par MongoDB.
- Un index unique sur `user_id`, que nous avons créé.

### Ajouter des Profils d'Utilisateur

Ajoutons quelques profils d'utilisateur pour illustrer l'utilisation des index :

```python
user_profiles = [{"user_id": 211, "name": "Luke"}, {"user_id": 212, "name": "Ziltoid"}]
result = db.profiles.insert_many(user_profiles)
```

### Empêcher les Duplications avec les Index Uniques

L'index unique sur `user_id` empêche l'insertion de documents avec des valeurs déjà existantes dans cet index :

```python
new_profile = {"user_id": 213, "name": "Drew"}
duplicate_profile = {"user_id": 212, "name": "Tommy"}
result = db.profiles.insert_one(new_profile)  # Cette insertion fonctionne.
result = db.profiles.insert_one(duplicate_profile)  # Provoque une erreur.
```

Erreur générée lors de la tentative d'insertion d'une duplication :

```
Traceback (most recent call last):
DuplicateKeyError: E11000 duplicate key error index: test_database.profiles.$user_id_1 dup key: { : 212 }
```
