# Manipulation de Bases de Données avec SQL 🗄️
![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)  
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)  
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)  
![GitHub repo size](https://img.shields.io/github/repo-size/username/repo-name)
![GitHub last commit](https://img.shields.io/github/last-commit/username/repo-name)
![GitHub issues](https://img.shields.io/github/issues/username/repo-name)
![GitHub stars](https://img.shields.io/github/stars/username/repo-name?style=social)

## Introduction 📌
Ce dépôt contient une série de tutoriels pratiques sur la manipulation de bases de données avec SQL.

Vous apprendrez à :

✅ Créer et gérer des bases de données  
✅ Manipuler des tables et des enregistrements  
✅ Écrire des requêtes SQL optimisées  
✅ Travailler avec des index, des jointures et des vues  
✅ Gérer les transactions et la sécurité des données  

## Tables des matières 📚
Voici la structure de ce repo :
- [Tache01: Création et et manips de base d'une base de données](https://github.com/DiopBabacarEdu/BD2.0/blob/main/SQL/Tache01.md)
- [Tache02: Contrôle et manipulation d'une base de données](https://github.com/DiopBabacarEdu/BD2.0/blob/main/SQL/Tache02.md)
- [Tache03: Utilité des triggers dans le contrôle d'une base de données](https://github.com/DiopBabacarEdu/BD2.0/blob/main/SQL/Tache03.md)
- [Tache04: Transaction et sécurité de base de données](https://github.com/DiopBabacarEdu/BD2.0/blob/main/SQL/Tache04.md)
- [Tache05: Optimisation de requêtes dans une DB](https://github.com/DiopBabacarEdu/BD2.0/blob/main/SQL/Tache05.md)

## Installation de MySQL

- Afin de démarrer les travaux sur ce git, commencez d'abord par installer MySQL sur votre ordinateur.
- Je vous propose d'installer MySQL via un conteneur tel que```Docker``` sur votre machine.

### 1. Installer MySQL via un conteneur Docker

#### Étapes à suivre :

1. **Télécharger l'image MySQL sur DockerHub**  
   Allez sur [MySQL sur Docker Hub](https://hub.docker.com/_/mysql) pour trouver l'image officielle de MySQL.
   Ou bien exécuter à partir de votre terminal la commande ```docker pull``` ci-dessous :
   ```bash
   docker pull mysql:latest
   ```
   Ce qui téléchargera l'image de MySQL avec le ```tag``` ```latest``` qui identifie la dernière version mise à jour de l'image.
3. **Lancer un conteneur MySQL**  
   Une fois le téléchargement de l'image terminé, ouvrez un terminal (ou PowerShell) et exécutez la commande suivante pour lancer un conteneur MySQL avec le mot de passe : ```root``` :
   ```bash
   docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
   ```
Cette commande ```docker run``` permet de démarrer le conteneur de l'image ```mysql:latest``` en configurant un mot de passe ```root```.
### 2. Se connecter à MySQL via Docker
Pour se connecter à MySQL en utilisant le conteneur, exécutez la commande suivante :
```bash
docker exec -it  some-mysql bash
```
qui permet d'ouvrir un client mySQL en mode interactif. 
Ensuite ouvrir à partir de votre ```bash``` l'application mySQL Client en tapant :
```bash
mySQL -u root -p
```
Ce qui va ouvrir un prompt qui vous demande votre mot de passe, qui a été configuré auparavant avec la valeur par défaut ```root```.


## Ressources 📚
Pour approfondir vos connaissances en SQL, voici quelques ressources utiles :

- [Documentation officielle MySQL](https://dev.mysql.com/doc/)
- [Documentation PostgreSQL](https://www.postgresql.org/docs/)
- [W3Schools - Tutoriel SQL](https://www.w3schools.com/sql/)
- [Le guide SQL de SQLZoo](https://sqlzoo.net/)

## 🚀 À propos de ce projet

Ce projet est développé dans le cadre du **Master Science des Données** à l'**Université Gaston Berger (UGB)**. Il vise à fournir une base solide en manipulation de bases de données pour les étudiants et les passionnés de science des données.

Si vous avez des suggestions ou souhaitez contribuer, n'hésitez pas à ouvrir une **issue** ou une **pull request** ! 🤝  

---
