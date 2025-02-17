# 🎯 Tâche 01 : Création d'une base de données avec 4 tables
Cette première tâche vous guide étape par étape pour créer une base de données SQL avec 4 tables et insérer des données. Nous allons créer une base de données pour gérer une bibliothèque, avec les tables suivantes : `Livres`, `Auteurs`, `Emprunts`, et `Membres`.

---

## Étape 1 : Créer la base de données

```sql
CREATE DATABASE Bibliotheque;
USE Bibliotheque;
```

## Étape 2 : Créer les tables
### Table Auteurs
Cette table contient les informations sur les auteurs des livres.
```sql
Table Auteurs
Cette table contient les informations sur les auteurs des livres.

CREATE TABLE Auteurs (
    AuteurID INT AUTO_INCREMENT PRIMARY KEY,
    Nom VARCHAR(100) NOT NULL,
    Prenom VARCHAR(100) NOT NULL
);
```
### Table Livres
Cette table contient les informations sur les livres.
```sql
CREATE TABLE Livres (
    LivreID INT AUTO_INCREMENT PRIMARY KEY,
    Titre VARCHAR(255) NOT NULL,
    AuteurID INT,
    AnneePublication INT,
    FOREIGN KEY (AuteurID) REFERENCES Auteurs(AuteurID)
);
```
### Table Membres
Cette table contient les informations sur les membres de la bibliothèque.
```sql
CREATE TABLE Membres (
    MembreID INT AUTO_INCREMENT PRIMARY KEY,
    Nom VARCHAR(100) NOT NULL,
    Prenom VARCHAR(100) NOT NULL,
    DateInscription DATE
);
```
### Table Emprunts
Cette table contient les informations sur les emprunts de livres par les membres.
```sql
CREATE TABLE Emprunts (
    EmpruntID INT AUTO_INCREMENT PRIMARY KEY,
    LivreID INT,
    MembreID INT,
    DateEmprunt DATE,
    DateRetour DATE,
    FOREIGN KEY (LivreID) REFERENCES Livres(LivreID),
    FOREIGN KEY (MembreID) REFERENCES Membres(MembreID)
);
```

## Étape 3 : Insérer des données dans les tables
### Insérer des données dans la table Auteurs
```sql
INSERT INTO Auteurs (Nom, Prenom) VALUES
('Rowling', 'J.K.'),
('Tolkien', 'J.R.R.'),
('Martin', 'George R.R.'),
('Hugo', 'Victor');
```

### Insérer des données dans la table Livres

```sql
INSERT INTO Livres (Titre, AuteurID, AnneePublication) VALUES
('Harry Potter à l\'école des sorciers', 1, 1997),
('Le Seigneur des Anneaux', 2, 1954),
('Le Trône de Fer', 3, 1996),
('Les Misérables', 4, 1862);
```

### Insérer des données dans la table Membres

```sql
INSERT INTO Membres (Nom, Prenom, DateInscription) VALUES
('Dupont', 'Jean', '2023-01-15'),
('Martin', 'Sophie', '2023-02-20'),
('Durand', 'Pierre', '2023-03-10');
```

### Insérer des données dans la table Emprunts

```sql
INSERT INTO Emprunts (LivreID, MembreID, DateEmprunt, DateRetour) VALUES
(1, 1, '2023-04-01', '2023-04-15'),
(2, 2, '2023-04-05', '2023-04-20'),
(3, 3, '2023-04-10', '2023-04-25');
```

## Étape 4 : Vérifier les données insérées
Vous pouvez vérifier les données insérées en exécutant des requêtes SELECT sur chaque table.

```sql
SELECT * FROM Auteurs;
SELECT * FROM Livres;
SELECT * FROM Membres;
SELECT * FROM Emprunts;
```

## Étape 5 : Conclusion
Vous avez maintenant créé une base de données avec 4 tables et inséré des données dans chaque table. Vous pouvez continuer à explorer SQL pour ajouter plus de fonctionnalités, comme des requêtes complexes, des mises à jour, des suppressions, etc.
