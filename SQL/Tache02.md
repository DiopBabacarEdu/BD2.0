# Tâche 02 : Contrôle d'une base de données SQL

Cette deuxième tâche vous guide dans la manipulation d'une base de données SQL en prenant comme exemple la base de données **Bibliotheque**, contenant les tables **Auteurs, Livres, Membres** et **Emprunts**. Vous apprendrez à effectuer des opérations essentielles comme les requêtes **SELECT**, les mises à jour **UPDATE**, les suppressions **DELETE** et l'utilisation des **jointures**.

## Étape 1 : Se connecter à la base de données
Avant de commencer, assurez-vous d'être connecté à votre système de gestion de base de données (MySQL, PostgreSQL, etc.) et d'utiliser la base de données **Bibliotheque**.

```sql
USE Bibliotheque;
```

## Étape 2 : Lire les données (SELECT)

### 1. Afficher tous les auteurs
```sql
SELECT * FROM Auteurs;
```

### 2. Afficher tous les livres avec leurs auteurs
```sql
SELECT Livres.Titre, Auteurs.Nom, Auteurs.Prenom
FROM Livres
JOIN Auteurs ON Livres.AuteurID = Auteurs.AuteurID;
```

### 3. Afficher les membres et leurs emprunts
```sql
SELECT Membres.Nom, Membres.Prenom, Livres.Titre, Emprunts.DateEmprunt, Emprunts.DateRetour
FROM Emprunts
JOIN Membres ON Emprunts.MembreID = Membres.MembreID
JOIN Livres ON Emprunts.LivreID = Livres.LivreID;
```

## Étape 3 : Filtrer les données (WHERE)
### 1. Afficher les livres publiés après l'an 2000
```sql
SELECT * FROM Livres
WHERE AnneePublication > 2000;
```
### 2. Afficher les emprunts d'un membre spécifique (par exemple, MembreID = 1)
```sql
SELECT * FROM Emprunts
WHERE MembreID = 1;
```

### 3. Afficher les livres d'un auteur spécifique (par exemple, AuteurID = 2)
```sql
SELECT * FROM Livres
WHERE AuteurID = 2;
```

## Étape 4 : Mettre à jour les données (UPDATE)
### 1. Modifier la date de retour d'un emprunt
```sql
UPDATE Emprunts
SET DateRetour = '2023-05-01'
WHERE EmpruntID = 1;
```
### 2. Changer le nom d'un membre
```sql
UPDATE Membres
SET Nom = 'Dupond'
WHERE MembreID = 1;
```
### 3. Mettre à jour l'année de publication d'un livre
```sql
UPDATE Livres
SET AnneePublication = 1998
WHERE LivreID = 1;
```

## Étape 5 : Supprimer des données (DELETE)
### 1. Supprimer un emprunt
```sql
DELETE FROM Emprunts
WHERE EmpruntID = 3;
```
### 2. Supprimer un membre (attention : cela peut affecter les emprunts liés)
```sql
DELETE FROM Membres
WHERE MembreID = 2;
```
### 3. Supprimer un livre
```sql
DELETE FROM Livres
WHERE LivreID = 4;
```

## Étape 6 : Utiliser des jointures pour des requêtes complexes
### 1. Afficher les livres empruntés avec les noms des membres
```sql
SELECT Livres.Titre, Membres.Nom, Membres.Prenom, Emprunts.DateEmprunt, Emprunts.DateRetour
FROM Emprunts
JOIN Livres ON Emprunts.LivreID = Livres.LivreID
JOIN Membres ON Emprunts.MembreID = Membres.MembreID;
```
### 2. Afficher les livres non rendus (date de retour dépassée)
```sql
SELECT Livres.Titre, Membres.Nom, Membres.Prenom, Emprunts.DateEmprunt, Emprunts.DateRetour
FROM Emprunts
JOIN Livres ON Emprunts.LivreID = Livres.LivreID
JOIN Membres ON Emprunts.MembreID = Membres.MembreID
WHERE Emprunts.DateRetour < CURDATE();
```

## Étape 7 : Gérer les erreurs et les contraintes
### 1. Vérifier les contraintes de clé étrangère
Si vous essayez de supprimer un auteur qui a des livres associés, vous obtiendrez une erreur. Pour gérer cela, vous pouvez :
1. Supprimer d'abord les livres associés.
2. Utiliser ON DELETE CASCADE lors de la création des tables pour supprimer automatiquement les enregistrements liés.

#### Exemple de création de table avec ON DELETE CASCADE :
```sql
CREATE TABLE Livres (
    LivreID INT AUTO_INCREMENT PRIMARY KEY,
    Titre VARCHAR(255) NOT NULL,
    AuteurID INT,
    AnneePublication INT,
    FOREIGN KEY (AuteurID) REFERENCES Auteurs(AuteurID) ON DELETE CASCADE
);
```
## Étape 8 : Sauvegarder et restaurer la base de données
### 1. Sauvegarder la base de données
```sql
mysqldump -u utilisateur -p Bibliotheque > backup_bibliotheque.sql
```
### 2. Restaurer la base de données
```sql
mysql -u utilisateur -p Bibliotheque < backup_bibliotheque.sql
```
