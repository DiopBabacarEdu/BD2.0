# Installer MongoDB Community Edition sur Ubuntu

---

### Vue d'ensemble
Ce tutoriel, extrait du [site officiel de MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/), explique comment installer MongoDB 8.0 Community Edition sur des versions LTS (support à long terme) d'Ubuntu Linux en utilisant le gestionnaire de paquets `apt`.

---

### Version de MongoDB
Ce guide installe la version MongoDB 8.0 Community Edition. Pour installer une autre version, se reférer à la page officielle pour sélectionner la documentation de la version souhaitée.

---

### Installation de MongoDB Community Edition

#### Étape 1 : Importer la clé publique
1. Assurez-vous que `gnupg` et `curl` sont installés :
   ```bash
   sudo apt-get install gnupg curl
   ```

2. Importez la clé publique GPG de MongoDB :
   ```bash
   curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor
   ```

---

#### Étape 2 : Créer le fichier de liste des sources
Créez le fichier `/etc/apt/sources.list.d/mongodb-org-8.0.list` adapté à votre version d'Ubuntu. Par exemple, pour Ubuntu 24.04 (Noble) :
```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```

---

#### Étape 3 : Mettre à jour la base de données locale
Rechargez la base de données des paquets :
```bash
sudo apt-get update
```

---

#### Étape 4 : Installer MongoDB
Installez la dernière version stable de MongoDB :
```bash
sudo apt-get install -y mongodb-org
```

Si vous rencontrez des problèmes lors de l'installation, consultez le [guide de dépannage de MongoDB](https://www.mongodb.com/docs/manual/reference/installation-ubuntu-community-troubleshooting/#std-label-install-ubuntu-troubleshooting).

---

### Lancer MongoDB Community Edition

#### Considérations sur les limites système (`ulimit`)
Les systèmes Unix limitent les ressources qu’un processus peut utiliser, ce qui peut affecter MongoDB. Ajustez ces limites selon les recommandations de MongoDB.

---

#### Répertoires
- **Données** : `/var/lib/mongodb`
- **Logs** : `/var/log/mongodb`

Si vous changez l'utilisateur qui exécute MongoDB, assurez-vous de modifier les permissions pour ces répertoires.

---

#### Fichier de configuration
Le fichier de configuration par défaut se trouve ici :
```plaintext
/etc/mongod.conf
```
Toute modification nécessite un redémarrage du service MongoDB.

---

#### Gérer MongoDB avec `systemd`
1. **Démarrer MongoDB** :
   ```bash
   sudo ] start mongod
   ```

2. **Vérifier le statut** :
   ```bash
   sudo systemctl status mongod
   ```

3. **Activer le démarrage automatique** :
   ```bash
   sudo systemctl enable mongod
   ```

4. **Arrêter MongoDB** :
   ```bash
   sudo systemctl stop mongod
   ```

5. **Redémarrer MongoDB** :
   ```bash
   sudo systemctl restart mongod
   ```

Pour surveiller les logs :
```bash
tail -f /var/log/mongodb/mongod.log
```

---

#### Utiliser MongoDB
Lancez une session `mongosh` :
```bash
mongosh
```
Consultez la documentation officielle pour plus d’informations sur les connexions distantes ou via des ports spécifiques.

---

### Désinstaller MongoDB Community Edition

1. **Arrêter MongoDB** :
   ```bash
   sudo service mongod stop
   ```

2. **Supprimer les paquets MongoDB** :
   ```bash
   sudo apt-get purge mongodb-org*
   ```

3. **Supprimer les données et logs** :
   ```bash
   sudo rm -r /var/log/mongodb
   sudo rm -r /var/lib/mongodb
   ```

⚠️ **Attention** : Cette opération est irréversible. Sauvegardez vos données avant de procéder.

---

### Informations complémentaires

#### Par défaut, MongoDB se lie à `127.0.0.1`
Cela signifie que MongoDB n'acceptera que les connexions locales. Pour permettre les connexions distantes, modifiez la valeur `bindIp` dans le fichier de configuration ou via l'option `--bind_ip`.

Avant de rendre MongoDB accessible à distance, suivez les recommandations de sécurité de MongoDB, comme l’activation de l’authentification.

Pour plus d’informations, consultez [la documentation officielle](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/).

