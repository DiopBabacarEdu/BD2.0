# Tâche 05: Optimisations de Requêtes SQL pour une Base de Données

Dans cette tâche, nous allons explorer quelques techniques d'optimisation pour améliorer les performances des requêtes SQL. Ces optimisations sont applicables à la base de données Bibliotheque vue précédemment et peuvent être adaptées à d'autres contextes.

## 📌 Table des Matières
- [Optimisation 1 : Utiliser des index](#optimisation-1-utiliser-des-index)
- [Optimisation 2 : Éviter SELECT *](#optimisation-2-eviter-select-asterisque)
- [Optimisation 3 : Utiliser des jointures efficaces](#optimisation-3-utiliser-des-jointures-efficaces)
- [Optimisation 4 : Limiter les résultats avec LIMIT](#optimisation-4-limiter-les-resultats-avec-limit)
- [Optimisation 5 : Éviter les sous-requêtes inutiles](#optimisation-5-eviter-les-sous-requetes-inutiles)
- [Optimisation 6 : Utiliser EXPLAIN pour analyser les requêtes](#optimisation-6-utiliser-explain-pour-analyser-les-requetes)
- [Optimisation 7 : Partitionner les tables](#optimisation-7-partitionner-les-tables)
- [Optimisation 8 : Utiliser des vues matérialisées](#optimisation-8-utiliser-des-vues-matérialisées)
- [Optimisation 9 : Optimiser les requêtes avec des agrégations](#optimisation-9-optimiser-les-requêtes-avec-des-agrégations)
- [Optimisation 10 : Utiliser des requêtes préparées](#optimisation-10-utiliser-des-requêtes-préparées)

## 🔥 Exemples d'Optimisations
## 🔧 Optimisation 1 : Utiliser des index
  **Objectif** : Accélérer les recherches en créant des index sur les colonnes fréquemment utilisées dans les clauses ``` WHERE```, ``` JOIN```, et ``` ORDER BY```.
  
- Créer un index sur la colonne Titre de la table Livres
```sql
CREATE INDEX idx_titre ON Livres (Titre);
```
- Créer un index composite sur les colonnes Nom et Prenom de la table Auteurs
```sql
CREATE INDEX idx_nom_prenom ON Auteurs (Nom, Prenom);
```
**Explication :**
- Les index accélèrent les recherches mais ralentissent les insertions et les mises à jour.
- Utilisez-les sur des colonnes souvent utilisées dans les conditions de recherche.

## 🔧 Optimisation 2 : Éviter SELECT *
**Objectif** : Réduire la quantité de données renvoyées en sélectionnant uniquement les colonnes nécessaires.

- Au lieu de :
```sql
SELECT * FROM Membres;
```
- Utilisez :
```sql
SELECT MembreID, Nom, Prenom FROM Membres;
```
**Explication :**
- ``` SELECT *``` renvoie toutes les colonnes, ce qui peut être inefficace.
- Sélectionnez uniquement les colonnes dont vous avez besoin.

## 🔧 Optimisation 3 : Utiliser des jointures efficaces
**Objectif**: Préférer les jointures explicites (```INNER JOIN```, ```LEFT JOIN```) aux jointures implicites pour une meilleure lisibilité et performance.

- Au lieu de :

```sql
SELECT Livres.Titre, Auteurs.Nom
FROM Livres, Auteurs
WHERE Livres.AuteurID = Auteurs.AuteurID;
```
- Utilisez :
```sql
SELECT Livres.Titre, Auteurs.Nom
FROM Livres
INNER JOIN Auteurs ON Livres.AuteurID = Auteurs.AuteurID;
```
**Explication :** Les jointures explicites sont plus claires et souvent mieux optimisées par le SGBD.

## 🔧 Optimisation 4 : Limiter les résultats avec LIMIT
**Objectif** : Réduire le nombre de résultats renvoyés pour les requêtes retournant un grand nombre de lignes.
- Limiter à 10 résultats
```sql
SELECT * FROM Livres
ORDER BY AnneePublication DESC
LIMIT 10;
```
**Explication :** ```LIMIT``` est utile pour les requêtes paginées ou pour éviter de surcharger la mémoire.

## 🔧 Optimisation 5 : Éviter les sous-requêtes inutiles
**Objectif** : Remplacer les sous-requêtes par des jointures ou des agrégations lorsque c'est possible.

- Au lieu de :
```sql
SELECT Titre FROM Livres
WHERE AuteurID IN (SELECT AuteurID FROM Auteurs WHERE Nom = 'Rowling');
```
- Utilisez :
```sql
SELECT Livres.Titre
FROM Livres
INNER JOIN Auteurs ON Livres.AuteurID = Auteurs.AuteurID
WHERE Auteurs.Nom = 'Rowling';
```
**Explication :** Les jointures sont souvent plus efficaces que les sous-requêtes.

## 🔧 Optimisation 6 : Utiliser EXPLAIN pour analyser les requêtes
**Objectif** : Comprendre comment le SGBD exécute une requête pour identifier les goulots d'étranglement.
```sql
EXPLAIN SELECT * FROM Livres WHERE Titre = '1984';
```

**Explication :** 
- ```EXPLAIN``` montre le plan d'exécution de la requête.
- Utilisez-le pour identifier les index manquants ou les opérations coûteuses.


## 🔧 Optimisation 7 : Partitionner les tables
**Objectif** : Diviser une grande table en partitions pour améliorer les performances des requêtes.

- Partitionner la table Emprunts par année
- ```sql
CREATE TABLE Emprunts (
    EmpruntID INT AUTO_INCREMENT PRIMARY KEY,
    LivreID INT,
    MembreID INT,
    DateEmprunt DATE,
    DateRetour DATE
)
```
```sql
PARTITION BY RANGE (YEAR(DateEmprunt)) (
    PARTITION p2020 VALUES LESS THAN (2021),
    PARTITION p2021 VALUES LESS THAN (2022),
    PARTITION p2022 VALUES LESS THAN (2023)
);
```
**Explication :**
- Le partitionnement réduit la quantité de données à parcourir pour les requêtes.

## 🔧 Optimisation 8 : Utiliser des vues matérialisées
**Objectif** : Stocker les résultats d'une requête complexe pour des accès rapides.

- Créer une vue matérialisée (si supporté par votre SGBD)
```sql
CREATE MATERIALIZED VIEW Vue_Livres_Populaires AS
SELECT LivreID, COUNT(*) AS NombreEmprunts
FROM Emprunts
GROUP BY LivreID
ORDER BY NombreEmprunts DESC;
```
**Explication :** Les vues matérialisées stockent les résultats physiquement, ce qui accélère les requêtes répétitives.


## 🔧 Optimisation 9 : Optimiser les requêtes avec des agrégations
**Objectif** : Utiliser des agrégations (```GROUP BY```, ```HAVING```) de manière efficace.

- Trouver les auteurs avec plus de 5 livres
```sql
SELECT Auteurs.Nom, COUNT(Livres.LivreID) AS NombreLivres
FROM Auteurs
INNER JOIN Livres ON Auteurs.AuteurID = Livres.AuteurID
GROUP BY Auteurs.AuteurID
HAVING NombreLivres > 5;
```
**Explication :**
- Utilisez ```HAVING``` pour filtrer les résultats après agrégation.

## 🔧 Optimisation 10 : Utiliser des requêtes préparées
**Objectif** : Utiliser des requêtes préparées pour éviter les injections SQL.

### Exemple en Python avec MySQL
```SQL
import mysql.connector

# Connexion à la base de données
db_connection = mysql.connector.connect(
    host="localhost",
    user="votre_utilisateur",
    password="votre_mot_de_passe",
    database="Bibliotheque"
)

cursor = db_connection.cursor()

# Définir la requête SQL avec un paramètre placeholder
query = "SELECT * FROM Membres WHERE Nom = %s"

# L'utilisateur entre le nom à rechercher
nom = "Dupont"

# Exécuter la requête avec le paramètre
cursor.execute(query, (nom,))

# Récupérer les résultats
result = cursor.fetchall()

# Afficher les résultats
for row in result:
    print(row)

# Fermer la connexion
cursor.close()
db_connection.close()
```
**Explication :**
- ***Préparation de la requête*** : Le query utilise un placeholder ```%s``` pour la variable ```Nom```. Cela sépare les données de la requête SQL, ce qui protège contre les injections.
- **Exécution de la requête** : La méthode ```cursor.execute()``` est utilisée pour exécuter la requête préparée, où nom est passé comme paramètre.
- **Protection** : Cela empêche toute tentative d'injection SQL, car l'entrée de l'utilisateur (comme nom) est traitée comme un paramètre et non comme une partie de la requête SQL elle-même.

Cela garantit que l'utilisateur ne pourra pas insérer de code malveillant dans la requête SQL.

