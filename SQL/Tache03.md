# 🎯 Tâche 03 : Contrôle d'une Base de Données avec des triggers

Dans ce tutoriel, nous allons créer des triggers pour la base de données **Bibliotheque**. Ces triggers couvrent des cas d'utilisation courants, tels que la validation des données, la mise à jour automatique de champs, et la gestion des contraintes métier.

## 📌 Table des Matières
1. [Valider la date de retour d'un emprunt](#trigger-1)
2. [Mettre à jour la date de modification d'un livre](#trigger-2)
3. [Empêcher la suppression d'un auteur avec des livres associés](#trigger-3)
4. [Limiter le nombre d'emprunts par membre](#trigger-4)
5. [Historiser les modifications de la table Membres](#trigger-5)
6. [Vérifier l'âge minimum d'un membre](#trigger-6)
7. [Mettre à jour automatiquement le stock de livres](#trigger-7)
8. [Empêcher la modification de l'ID d'un livre](#trigger-8)
9. [Notifier les retards de retour de livres](#trigger-9)
10. [Calculer automatiquement l'âge d'un livre](#trigger-10)

## 🛠 Prérequis
Assurez-vous d'avoir la base de données **Bibliotheque** avec les tables suivantes :
- **Auteurs**
- **Livres**
- **Membres**
- **Emprunts**

## 🔥 Exemples de Triggers


### 🔖 [Trigger 1 : Valider la date de retour d'un emprunt](#trigger-1)
**Objectif** : S'assurer que la date de retour est postérieure à la date d'emprunt.

```sql
DELIMITER $$

CREATE TRIGGER before_insert_emprunt
BEFORE INSERT ON Emprunts
FOR EACH ROW
BEGIN
    IF NEW.DateRetour <= NEW.DateEmprunt THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'La date de retour doit être postérieure à la date d\'emprunt.';
    END IF;
END$$
DELIMITER ;
```


### 🔖 Trigger 2 : Mettre à jour la date de modification d'un livre
**Objectif** : Mettre à jour automatiquement la date de modification d'un livre lors d'une mise à jour.
```sql
DELIMITER $$

CREATE TRIGGER before_update_livre
BEFORE UPDATE ON Livres
FOR EACH ROW
BEGIN
    SET NEW.DateModification = NOW();
END$$

DELIMITER ;
```

**Explication :**
Ce trigger s'exécute avant une mise à jour dans la table Livres et met à jour automatiquement le champ DateModification avec l'heure actuelle.


### 🔖 Trigger 3 : Empêcher la suppression d'un auteur avec des livres associés
**Objectif** : Empêcher la suppression d'un auteur s'il a des livres associés.
```SQL
DELIMITER $$

CREATE TRIGGER before_delete_auteur
BEFORE DELETE ON Auteurs
FOR EACH ROW
BEGIN
    DECLARE livre_count INT;
    SELECT COUNT(*) INTO livre_count FROM Livres WHERE AuteurID = OLD.AuteurID;
    IF livre_count > 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Impossible de supprimer un auteur avec des livres associés.';
    END IF;
END$$

DELIMITER ;
```
**Explication :**
Ce trigger s'exécute avant la suppression d'un auteur. Il vérifie si l'auteur a des livres associés dans la table Livres. Si c'est le cas, il empêche la suppression et génère une erreur.


### 🔖 Trigger 4 : Limiter le nombre d'emprunts par membre
**Objectif** : Limiter un membre à 5 emprunts maximum.
```SQL
DELIMITER $$

CREATE TRIGGER before_insert_emprunt_limit
BEFORE INSERT ON Emprunts
FOR EACH ROW
BEGIN
    DECLARE emprunt_count INT;
    SELECT COUNT(*) INTO emprunt_count FROM Emprunts WHERE MembreID = NEW.MembreID;
    IF emprunt_count >= 5 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Un membre ne peut pas emprunter plus de 5 livres.';
    END IF;
END$$

DELIMITER ;
```
**Explication :**
Ce trigger vérifie avant chaque insertion dans Emprunts que le membre n'a pas atteint la limite de 5 emprunts. Si cette condition est violée, il génère une erreur.


### 🔖 Trigger 5 : Historiser les modifications de la table Membres
**Objectif** : Enregistrer les modifications de la table Membres dans une table d'historique.
```SQL
-- Créer la table d'historique
CREATE TABLE Membres_Historique (
    HistoriqueID INT AUTO_INCREMENT PRIMARY KEY,
    MembreID INT,
    Nom VARCHAR(100),
    Prenom VARCHAR(100),
    DateModification DATETIME
);

-- Créer le trigger
DELIMITER $$
```
```SQL
CREATE TRIGGER after_update_membre
AFTER UPDATE ON Membres
FOR EACH ROW
BEGIN
    INSERT INTO Membres_Historique (MembreID, Nom, Prenom, DateModification)
    VALUES (OLD.MembreID, OLD.Nom, OLD.Prenom, NOW());
END$$

DELIMITER ;
```
**Explication :**
Ce trigger enregistre après chaque mise à jour de la table Membres les anciennes valeurs dans une table Membres_Historique, permettant de garder une trace des modifications.


### 🔖 Trigger 6 : Vérifier l'âge minimum d'un membre
**Objectif** : S'assurer qu'un membre a au moins 12 ans.
```SQL
DELIMITER $$

CREATE TRIGGER before_insert_membre
BEFORE INSERT ON Membres
FOR EACH ROW
BEGIN
    IF NEW.DateNaissance > DATE_SUB(NOW(), INTERVAL 12 YEAR) THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Un membre doit avoir au moins 12 ans.';
    END IF;
END$$

DELIMITER ;
```
**Explication :**
Ce trigger empêche l'insertion de nouveaux membres qui n'ont pas encore 12 ans, en vérifiant la date de naissance avant l'ajout dans la base.


### 🔖 Trigger 7 : Mettre à jour automatiquement le stock de livres
**Objectif** : Décrémenter le stock d'un livre lors d'un emprunt.
```SQL
DELIMITER $$

CREATE TRIGGER after_insert_emprunt
AFTER INSERT ON Emprunts
FOR EACH ROW
BEGIN
    UPDATE Livres
    SET Stock = Stock - 1
    WHERE LivreID = NEW.LivreID;
END$$

DELIMITER ;
```
**Explication :**
Après l'insertion d'un emprunt, ce trigger met à jour le stock du livre emprunté en le décrémentant de 1.


### 🔖 Trigger 8 : Empêcher la modification de l'ID d'un livre
**Objectif**: Empêcher la modification de l'ID d'un livre.
```SQL
DELIMITER $$

CREATE TRIGGER before_update_livre_id
BEFORE UPDATE ON Livres
FOR EACH ROW
BEGIN
    IF NEW.LivreID <> OLD.LivreID THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'La modification de l\'ID d\'un livre est interdite.';
    END IF;
END$$

DELIMITER ;
```
**Explication :**
Ce trigger empêche de modifier l'ID d'un livre une fois qu'il est inséré dans la base de données.


### 🔖 Trigger 9 : Notifier les retards de retour de livres
**Objectif** : Envoyer une notification si un livre n'est pas retourné à temps.

```SQL
DELIMITER $$

CREATE TRIGGER after_update_emprunt_retard
AFTER UPDATE ON Emprunts
FOR EACH ROW
BEGIN
    IF NEW.DateRetour IS NULL AND OLD.DateRetour IS NULL AND NEW.DateEmprunt < DATE_SUB(NOW(), INTERVAL 30 DAY) THEN
        INSERT INTO Notifications (MembreID, Message, DateNotification)
        VALUES (NEW.MembreID, 'Votre livre est en retard. Veuillez le retourner.', NOW());
    END IF;
END$$

DELIMITER ;
```
**Explication :**
Ce trigger vérifie si un livre est en retard après 30 jours d'emprunt et envoie une notification au membre concerné.


### 🔖 Trigger 10 : Calculer automatiquement l'âge d'un livre
**Objectif** : Calculer l'âge d'un livre en fonction de son année de publication.
```SQL
DELIMITER $$

CREATE TRIGGER before_insert_livre_age
BEFORE INSERT ON Livres
FOR EACH ROW
BEGIN
    SET NEW.Age = YEAR(NOW()) - NEW
```
