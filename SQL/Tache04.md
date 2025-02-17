# Tâche 04 : Gérer les Transactions et la Sécurité des Données
Dans cette partie, nous allons explorer comment utiliser les transactions pour garantir l'intégrité des données et comment mettre en place des mesures de sécurité pour protéger votre base de données.

## 📌 Table des Matières
- [Transaction 1 : Insérer un livre et un auteur en une seule transaction](#transaction-1)
- [Transaction 2 : Mettre à jour un emprunt et le stock de livres](#transaction-2)
- [Transaction 3 : Supprimer un membre et ses emprunts](#transaction-3)

- [Sécurité 4 : Créer un utilisateur avec des permissions limitées](#securite-4)
- [Sécurité 5 : Chiffrer les données sensibles](#securite-5)
- [Sécurité 6 : Auditer les accès à la base de données](#securite-6)
- [Sécurité 7 : Sauvegarder et restaurer la base de données](#securite-7)
- [Sécurité 8 : Utiliser des vues pour limiter l'accès aux données](#securite-8)
- [Sécurité 9 : Mettre en place des rôles et des permissions](#securite-9)
- [Sécurité 10 : Protéger contre les injections SQL](#securite-10)

🛠 **Prérequis**  
Assurez-vous d'avoir la base de données **Bibliotheque** avec les tables suivantes :
- Auteurs
- Livres
- Membres
- Emprunts


## 🔥 Exemples de Transactions et Sécurité
<a id="transaction-1"></a>
## :card_file_box: Transaction 1 : Insérer un livre et un auteur en une seule transaction
**Objectif** : Garantir que l'insertion d'un livre et de son auteur est atomique.

```sql
START TRANSACTION;
-- Insérer un nouvel auteur
INSERT INTO Auteurs (Nom, Prenom) VALUES ('Orwell', 'George');
-- Récupérer l'ID de l'auteur inséré
SET @auteur_id = LAST_INSERT_ID();
-- Insérer un nouveau livre associé à cet auteur
INSERT INTO Livres (Titre, AuteurID, AnneePublication) VALUES ('1984', @auteur_id, 1949);
-- Valider la transaction
COMMIT;
```

****Explication :****
Si une des deux insertions échoue, la transaction est annulée (ROLLBACK).
Utilisez COMMIT pour valider les changements.


<a id="transaction-2"></a>
## :card_file_box: Transaction 2 : Mettre à jour un emprunt et le stock de livres
**Objectif** : Mettre à jour un emprunt et décrémenter le stock de livres en une seule transaction.
```sql
START TRANSACTION;
-- Mettre à jour la date de retour d'un emprunt
UPDATE Emprunts SET DateRetour = NOW() WHERE EmpruntID = 1;
-- Décrémenter le stock du livre emprunté
UPDATE Livres SET Stock = Stock - 1 WHERE LivreID = (SELECT LivreID FROM Emprunts WHERE EmpruntID = 1);
-- Valider la transaction
COMMIT;
```
****Explication :****
Si l'une des deux mises à jour échoue, la transaction est annulée.

<a id="transaction-3"></a>
## :card_file_box: Transaction 3 : Supprimer un membre et ses emprunts
**Objectif** : Supprimer un membre et tous ses emprunts en une seule transaction.
```SQL
START TRANSACTION;
-- Supprimer les emprunts du membre
DELETE FROM Emprunts WHERE MembreID = 1;
-- Supprimer le membre
DELETE FROM Membres WHERE MembreID = 1;
-- Valider la transaction
COMMIT;
```
**Explication :**
Si la suppression des emprunts ou du membre échoue, la transaction est annulée.

<a id="securite-4"></a>
## :card_file_box: Sécurité 4 : Créer un utilisateur avec des permissions limitées
**Objectif** : Créer un utilisateur avec des permissions limitées pour lire les données.
```SQL
-- Créer un nouvel utilisateur
CREATE USER 'lecteur'@'localhost' IDENTIFIED BY 'motdepasse';
-- Accorder des permissions de lecture sur la table Livres
GRANT SELECT ON Bibliotheque.Livres TO 'lecteur'@'localhost';
-- Recharger les permissions
FLUSH PRIVILEGES;
```
**Explication :**
L'utilisateur lecteur ne peut que lire les données de la table Livres.

<a id="securite-5"></a>
## :card_file_box: Sécurité 5 : Chiffrer les données sensibles
**Objectif** : Chiffrer les données sensibles comme les mots de passe des membres.

```SQL
-- Ajouter une colonne pour stocker le mot de passe chiffré
ALTER TABLE Membres ADD COLUMN MotDePasse VARBINARY(255);
-- Chiffrer un mot de passe lors de l'insertion
INSERT INTO Membres (Nom, Prenom, MotDePasse) VALUES ('Dupont', 'Jean', AES_ENCRYPT('monmotdepasse', 'cle_secrete'));
-- Déchiffrer le mot de passe pour vérification
SELECT Nom, Prenom, AES_DECRYPT(MotDePasse, 'cle_secrete') AS MotDePasse FROM Membres;
```
**Explication :**
Utilisez AES_ENCRYPT pour chiffrer et AES_DECRYPT pour déchiffrer les données.

<a id="securite-6"></a>
## :card_file_box: Sécurité 6 : Auditer les accès à la base de données
**Objectif** : Enregistrer les connexions des utilisateurs dans une table d'audit.

```SQL
-- Créer une table d'audit
CREATE TABLE Audit_Connexions (
    AuditID INT AUTO_INCREMENT PRIMARY KEY,
    Utilisateur VARCHAR(100),
    DateConnexion DATETIME
);
-- Créer un trigger pour enregistrer les connexions**
DELIMITER $$

CREATE TRIGGER after_connect
AFTER CONNECT ON *.*
FOR EACH ROW
BEGIN
    INSERT INTO Audit_Connexions (Utilisateur, DateConnexion)
    VALUES (CURRENT_USER(), NOW());
END$$

DELIMITER ;
```
**Explication :**
Ce trigger enregistre chaque connexion dans la table Audit_Connexions.

<a id="securite-7"></a>
## :card_file_box: Sécurité 7 : Sauvegarder et restaurer la base de données
**Objectif**: Sauvegarder et restaurer la base de données pour prévenir la perte de données.

```SQL
-- Sauvegarder la base de données
mysqldump -u root -p Bibliotheque > backup_bibliotheque.sql
-- Restaurer la base de données
mysql -u root -p Bibliotheque < backup_bibliotheque.sql
```
**Explication :**
Utilisez mysqldump pour sauvegarder et mysql pour restaurer.

<a id="securite-8"></a>
## :card_file_box: Sécurité 8 : Utiliser des vues pour limiter l'accès aux données
**Objectif** : Créer une vue pour limiter l'accès aux données sensibles.

```SQL
-- Créer une vue pour afficher uniquement les noms et prénoms des membres
CREATE VIEW Vue_Membres AS
SELECT Nom, Prenom FROM Membres;
-- Accorder l'accès à la vue
GRANT SELECT ON Bibliotheque.Vue_Membres TO 'lecteur'@'localhost';
```

**Explication :**
La vue Vue_Membres masque les données sensibles comme les mots de passe.

<a id="securite-9"></a>
## :card_file_box: Sécurité 9 : Mettre en place des rôles et des permissions
**Objectif** : Créer des rôles pour gérer les permissions de manière centralisée.

```SQL
-- Créer un rôle
CREATE ROLE 'gestionnaire';
-- Accorder des permissions au rôle
GRANT SELECT, INSERT, UPDATE ON Bibliotheque.* TO 'gestionnaire';
-- Attribuer le rôle à un utilisateur**
GRANT 'gestionnaire' TO 'utilisateur'@'localhost';
```
**Explication :**
Le rôle gestionnaire permet de gérer les permissions de plusieurs utilisateurs.

<a id="securite-10"></a>
## :card_file_box: Sécurité 10 : Protéger contre les injections SQL
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
- ***Préparation de la requête*** : Le query utilise un placeholder %s pour la variable Nom. Cela sépare les données de la requête SQL, ce qui protège contre les injections.
- **Exécution de la requête** : La méthode cursor.execute() est utilisée pour exécuter la requête préparée, où nom est passé comme paramètre.
- **Protection** : Cela empêche toute tentative d'injection SQL, car l'entrée de l'utilisateur (comme nom) est traitée comme un paramètre et non comme une partie de la requête SQL elle-même.


Cela garantit que l'utilisateur ne pourra pas insérer de code malveillant dans la requête SQL.
