
# Installation d'Elasticsearch

## Prérequis : Java
Elasticsearch nécessite au moins **Java 8**. Il est recommandé d'utiliser **Oracle JDK 1.8.0_131**. L'installation de Java peut varier selon la plateforme. Voici comment vérifier votre version actuelle de Java avant de procéder :

```bash
java -version
echo $JAVA_HOME
```

Si nécessaire, installez ou mettez à jour Java. La documentation officielle pour l'installation de Java est disponible sur [le site web d'Oracle](https://www.oracle.com/java/technologies/javase-downloads.html).

---

## Télécharger Elasticsearch
Les binaires d'Elasticsearch sont disponibles sur [www.elastic.co/downloads](https://www.elastic.co/downloads). Vous pouvez y trouver des archives pour différentes versions et formats : 

- **Archive ZIP ou TAR**
- **Paquet DEB ou RPM**
- **Installateur MSI pour Windows**

---

## Exemple d'installation avec un fichier TAR
Pour cet exemple, nous utiliserons une **archive TAR**.

### Étape 1 : Télécharger Elasticsearch
Téléchargez Elasticsearch version **5.5.3** avec la commande suivante :

```bash
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.3.tar.gz
```

---

### Étape 2 : Extraire l'archive
Extrayez les fichiers de l'archive avec la commande suivante :

```bash
tar -xvf elasticsearch-5.5.3.tar.gz
```

Une fois extrait, un ensemble de fichiers et de dossiers sera créé dans le répertoire courant.

---

### Étape 3 : Accéder au répertoire bin
Déplacez-vous dans le répertoire `bin` d'Elasticsearch avec la commande :

```bash
cd elasticsearch-5.5.3/bin
```

---

### Étape 4 : Démarrer le nœud et le cluster
Lancez Elasticsearch avec la commande suivante :

```bash
./elasticsearch
```

Elasticsearch est maintenant en cours d'exécution et prêt à être utilisé.

