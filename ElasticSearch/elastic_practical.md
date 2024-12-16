# Prise en main pratique de ElasticSearch
## Configuration
Pour commencer, vous devez avoir Elasticsearch. Vérifier votre installation avant de procéder aux étapes suivantes.

Ensuite, vous devez communiquer avec **Elasticsearch**. 
Pour cela, il est nécessaire d'émettre des requêtes  **HTTP** vers l'API REST. 

Elastic démarre par défaut sur le port  **9200**. Pour y accéder, vous pouvez utiliser l'outil qui convient le mieux à vos compétences : outils de ligne de commande (comme curl pour Linux), plug-ins REST pour navigateurs Web comme Chrome ou Firefox, ou tout simplement Kibana avec le plug-in console que vous pouvez installer. 

Chaque requête se compose d'un verbe HTTP (GET, POST, PUT, etc.),
d'un point de terminaison URL
et d'un corps facultatif (dans la plupart des cas, le corps est un objet JSON).


Par exemple, pour confirmer qu'**Elasticsearch** a bien démarré, exécutons **GET** vers l'URL de base pour accéder au point de terminaison de base (aucun corps n'est nécessaire) :

```python
GET localhost:9200
```
Vous devriez obtenir une réponse similaire à celle ci-dessous. Étant donné que nous n'avions rien configuré, le nom de notre instance se présentera sous la forme d'une chaîne de sept lettres aléatoires :

```json
{
    "name": "t9mGYu5",
    "cluster_name": "elasticsearch",
    "cluster_uuid": "xq-6d4QpSDa-kiNE4Ph-Cg",
    "version": {
        "number": "5.5.0",
        "build_hash": "260387d",
        "build_date": "2017-06-30T23:16:05.735Z",
        "build_snapshot": false,
        "lucene_version": "6.6.0"
    },
    "tagline": "You Know, for Search"
}
```
## Quelques exemples simples
Nous avons déjà à notre disposition une instance Elasticsearch initialisée qui fonctionne bien. La première chose que nous allons faire, c'est ajouter des documents et les récupérer. Les documents dans Elasticsearch sont au format JSON. Ils sont également ajoutés aux index et disposent d'un type. Nous allons maintenant ajouter à l'index nommé "accounts" un document de type "person" avec l'ID "1". Étant donné que l'index n'existe pas encore, Elasticsearch va le créer automatiquement.
```python
POST localhost:9200/accounts/person/1 
{
    "name" : "John",
    "lastname" : "Doe",
    "job_description" : "Systems administrator and Linux specialist"
}
```
La réponse va renvoyer des informations concernant la création du document :
```python
{
    "_index": "accounts",
    "_type": "person",
    "_id": "1",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "created": true
}
```
Maintenant que le document existe, nous pouvons le récupérer :
```python
GET localhost:9200/accounts/person/1
```

Le résultat contiendra des métadonnées ainsi que le document complet (affiché dans le champ _source) :
```python
{
    "_index": "accounts",
    "_type": "person",
    "_id": "1",
    "_version": 1,
    "found": true,
    "_source": {
        "name": "John",
        "lastname": "Doe",
        "job_description": "Systems administrator and Linux specialit"
    }
}
```
Tout lecteur assidu se sera déjà rendu compte que nous avons fait une erreur dans la description de la tâche (specialit). Nous allons la corriger en mettant à jour le document (_update) :
```python
POST localhost:9200/accounts/person/1/_update
{
      "doc":{
          "job_description" : "Systems administrator and Linux specialist"
       }
}
```
Suite au succès de l'opération, le document est modifié. Récupérons-le à nouveau et vérifions la réponse :
```python
{
  "_index": "accounts",
    "_type": "person",
    "_id": "1",
    "_version": 2,
    "found": true,
    "_source": {
        "name": "John",
        "lastname": "Doe",
        "job_description": "Systems administrator and Linux specialist"
    }
}
```
Pour nous préparer aux prochaines opérations, ajoutons un document supplémentaire avec l'ID "2" :
```python
POST localhost:9200/accounts/person/2
{
    "name" : "John",
    "lastname" : "Smith",
    "job_description" : "Systems administrator"
}
```
Nous avons réussi à récupérer les documents en fonction de leur ID, mais nous n'avons pas fait de recherches. Lorsque nous utilisons l'API REST pour faire une recherche, nous pouvons faire passer cette recherche dans le corps de la requête ou directement dans l'URL avec une syntaxe spécifique. Dans cette section, nous allons faire des recherches directement dans l'URL au format /_search?q=something :
```python
GET localhost:9200/_search?q=john
```
Les deux documents apparaîtront dans les résultats de cette recherche, étant donné qu'ils incluent tous les deux john :
```python
{
    "took": 58,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 0.2876821,
        "hits": [
            {
                "_index": "accounts",
                "_type": "person",
                "_id": "2",
                "_score": 0.2876821,
                "_source": {
                    "name": "John",
                    "lastname": "Smith",
                    "job_description": "Systems administrator"
                }
            },
            {
                "_index": "accounts",
                "_type": "person",
                "_id": "1",
                "_score": 0.28582606,
                "_source": {
                    "name": "John",
                    "lastname": "Doe",
                    "job_description": "Systems administrator and Linux specialist"
                }
            }
        ]
    }
}
```
Dans ce résultat, nous pouvons voir les documents correspondants ainsi que certaines métadonnées, comme le nombre total de résultats pour la recherche. Voyons d'autres exemples de recherches. Avant d'exécuter ces recherches et de voir leurs résultats, essayez de déterminer par vous-même quels documents seront récupérés (je donnerai la réponse après la commande) :
```python
GET localhost:9200/_search?q=smith
```
Cette recherche ne renverra que le dernier document que nous avons ajouté, car c'est le seul qui contient smith.
```python
GET localhost:9200/_search?q=job_description:john
```
Cette recherche ne renverra aucun document. Dans le cas présent, nous avons restreint la recherche uniquement au champ job_description, qui ne contient pas ce terme. Pour continuer à vous exercer, essayez de trouver comment formuler les recherches suivantes : - une recherche dans le champ qui ne renverra que le document avec l'ID "1" - une recherche dans le champ qui renverra les deux documents - une recherche dans le champ qui ne renverra que le document avec l'ID "2" (petite astuce : le paramètre "q" utilise la même syntaxe que la chaîne de requête).

Ce dernier exemple soulève une question : comme nous l'avons vu, nous pouvons faire des recherches dans des champs spécifiques. Mais pouvons-nous également faire des recherches au sein d'un index spécifique ? La réponse est oui : nous pouvons préciser l'index et le type dans l'URL. Essayez ceci :
```python
GET localhost:9200/accounts/person/_search?q=job_description:linux
```
Non seulement nous pouvons faire des recherches dans un index, mais nous pouvons aussi faire des recherches dans plusieurs index simultanément en fournissant une liste de noms d'index séparés par une virgule. Il en va de même pour les types. Plusieurs options sont possibles : vous trouverez plus d'informations dans les sections Multi-Index, Multi-type. À vous de jouer : ajoutez des documents dans un deuxième index (distinct) et effectuez des recherches dans les deux index en même temps.

Pour conclure cette section, nous allons supprimer un document, puis l'intégralité de l'index. Après avoir supprimé le document, essayez de le récupérer ou de le trouver dans les recherches.
```python
DELETE localhost:9200/accounts/person/1
```
La réponse nous confirme la suppression :
```python
{
    "found": true,
    "_index": "accounts",
    "_type": "person",
    "_id": "1",
    "_version": 3,
    "result": "deleted",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    }
}
```
Pour finir, nous supprimons l'intégralité de l'index.
```python
DELETE localhost:9200/accounts
```
Voilà ! Nous sommes arrivés au terme de cette première section. Résumons ce que nous avons fait :

- Nous avons ajouté un document. De manière implicite, un index a été créé (l'index n'existait pas précédemment).
- Nous avons récupéré le document.
- Nous avons mis à jour le document pour corriger une erreur et nous avons vérifié que la correction avait bien été effectuée.
- Nous avons ajouté un deuxième document.
- Nous avons fait des recherches, notamment des recherches utilisant implicitement tous les champs et une recherche ciblée sur un seul champ.
- Nous avons proposé plusieurs exercices de recherche.
- Nous avons expliqué les bases de la recherche simultanée sur plusieurs index et plusieurs types.
- Nous avons proposé une recherche dans plusieurs index en même temps.
- Nous avons supprimé un document.
- Nous avons supprimé l'intégralité d'un index.

Si vous souhaitez en savoir plus sur les thèmes abordés dans cette section, consultez les liens suivants :

- [API index](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-index_.html)
- [API Get](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-get.html)
- [API Delete](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-delete.html)
- [API Update](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-update.html)
- [URI Search](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/search-uri-request.html)

# Manipulation de données 
Jusque-là, nous avons manipulé des données fictives. Dans cette section, nous allons nous entraîner en utilisant des pièces de Shakespeare. Commencez par télécharger le fichier shakespeare.json, disponible sur Kibana : Loading sample data. Dans Elasticsearch, l'API Bulk vous permet d'effectuer des opérations d'ajout, de suppression, de mise à jour et de création en vrac, c'est-à-dire par lot. Le fichier que nous avons téléchargé contient des données prêtes à être ingérées à l'aide de cette API et prêtes à être indexées dans un index nommé Shakespeare contenant des documents de type "act", "scene" et "line". Le corps des requêtes vers l'API Bulk comprend un objet JSON par ligne ; pour les opérations d'addition, comme celles qui se trouvent dans le fichier, il y a un objet JSON indiquant les métadonnées concernant l'opération d'ajout et un deuxième objet JSON dans la ligne suivante contenant le document à ajouter :
```python
{"index":{"_index":"shakespeare","_type":"act","_id":0}}
{"line_id":1,"play_name":"Henry IV","speech_number":"","line_number":"","speaker":"","text_entry":"ACT I"}
```
Nous ne détaillerons pas davantage l'API Bulk. Si toutefois vous souhaitez plus d'informations à ce sujet, consultez la documentation sur Bulk.

À présent, chargeons toutes ces données dans Elasticsearch. Étant donné que le corps de cette requête est assez volumineux (plus de 200 000 lignes), il est recommandé de passer par un outil qui permette de charger le corps de la requête à partir d'un fichier, par exemple cURL :

```python
curl -XPOST "localhost:9200/shakespeare/_bulk?pretty" --data-binary @shakespeare.json
```
Une fois les données chargées, nous pouvons commencer à faire des recherches. Dans la section précédente, nous avons fait des recherches en passant par l'URL. Dans cette section, nous allons découvrir le langage Query DSL qui précise le format JSON à utiliser dans le corps des requêtes de recherche pour définir les requêtes. Selon le type d'opération, les requêtes peuvent être formulées à l'aide des verbes GET et POST. Commençons par la plus simple : obtenir tous les documents avec GET. Pour cela, nous indiquons dans le corps une clé query, et comme valeur, la requête match_all.
```python
GET localhost:9200/shakespeare/_search
{
    "query": {
            "match_all": {}
    }
}
```
Dix documents s'affichent dans les résultats, dont voici une sortie partielle :
```python
{
    "took": 7,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 111393,
        "max_score": 1,
        "hits": [
              ...          
            {
                "_index": "shakespeare",
                "_type": "line",
                "_id": "19",
                "_score": 1,
                "_source": {
                    "line_id": 20,
                    "play_name": "Henry IV",
                    "speech_number": 1,
                    "line_number": "1.1.17",
                    "speaker": "KING HENRY IV",
                    "text_entry": "The edge of war, like an ill-sheathed knife,"
                }
            },
            ...
```       
Le format des recherches est plutôt simple. De nombreux types de recherches sont disponibles : Elastic propose aussi bien des recherches directes (par exemple, recherche d'un terme précis, recherche d'éléments dans une gamme, etc.) que des requêtes composées (par exemple, avec les opérateurs booléens AND, OR, etc.). Pour avoir une référence complète sur le sujet, consultez la documentation sur Query DSL. Ici, nous verrons simplement quelques exemples pour nous familiariser avec l'utilisation de ces différents types de recherche.
```python
POST localhost:9200/shakespeare/scene/_search/
{
    "query":{
        "match" : {
            "play_name" : "Antony"
        }
    }
}
```
Avec la requête précédente, nous recherchons l'ensemble des scènes (voir l'URL) pour lesquelles le nom de la pièce contient le prénom Antony. Nous pouvons affiner cette recherche et sélectionner également les scènes dans lesquelles *Demetrius" est le locuteur :
```python
POST localhost:9200/shakespeare/scene/_search/
{
    "query":{
        "bool": {
            "must" : [
                {
                    "match" : {
                        "play_name" : "Antony"
                    }
                },
                {
                    "match" : {
                        "speaker" : "Demetrius"
                    }
                }
            ]
        }
    }
}
```
Attaquons à présent un premier exercice : modifiez la requête précédente de sorte que la recherche renvoie non seulement les scènes dans lesquelles le locuteur est Demetrius, mais aussi celles où le locuteur est Antony (petite astuce : regardez la clause booléenne should). Deuxième exercice : n'hésitez pas à tester les différentes options qui peuvent être utilisées dans le corps de la requête lors de la recherche. Par exemple, sélectionnez la position dans les résultats à partir de laquelle nous voulons commencer et le nombre de résultats que nous voulons récupérer pour effectuer la pagination.

Jusqu'à présent, nous avons exécuté quelques requêtes à l'aide de Query DSL. Et si, au lieu de nous contenter de récupérer les contenus que nous recherchons, nous pouvions les analyser ? C'est là que les agrégations entrent en jeu. Les agrégations nous permettent d'avoir une visibilité approfondie sur les données : par exemple, combien y a-t-il de pièces différentes dans notre ensemble de données actuel ? Combien de scènes y a-t-il en moyenne par ouvrage ? Quels sont les ouvrages qui contiennent le plus de scènes ?

Avant de passer à des exemples pratiques, revenons un peu en arrière, au moment où nous avons créé l'index Shakespeare, car il serait malvenu de poursuivre sans avoir étudié un peu la théorie au préalable. Dans Elastic, nous pouvons créer des index qui définissent les types de données pour les différents champs qu'ils contiennent : champs numériques, champs de mots-clés, champs textuels... Il y a de nombreux types de données. Les types de données dont peut disposer un index sont définis via les mappings. Dans le cas présent, nous n'avons pas créé d'index avant d'indexer les documents. C'est donc Elastic qui a attribué un type à chaque champ (et a créé un mapping de l'index). Le type text a été sélectionné pour les champs textuels : l'analyse de ce type est ce qui nous permet de trouver le nom de la pièce Antony and Cleopatra dans le champ play_name en effectuant une simple recherche sur Antony. Par défaut, nous ne pouvons pas faire d'agrégation dans les champs analysés. Comment faire pour afficher ces agrégations si les champs ne nous le permettent pas ? Au moment d'attribuer un type à chaque champ, Elastic a également ajouté une version non analysée des champs textuels (appelée keyword) au cas où nous souhaiterions faire des agrégations, des tris ou des scripts : il nous suffit donc d'utiliser play_name.keyword dans les agrégations. Pour vous entraîner, inspectez les mappings actuels.

Après cette petite leçon relativement courte et relativement théorique, revenons à nos moutons, c'est-à-dire aux agrégations ! Commençons par étudier nos données et déterminer le nombre de pièces différentes à notre disposition :
```python
GET localhost:9200/shakespeare/_search
{
    "size":0,
    "aggs" : {
        "Total plays" : {
            "cardinality" : {
                "field" : "play_name.keyword"
            }
        }
    }
}
```
Remarque : étant donné que les documents ne nous intéressent pas, nous avons décidé de n'afficher aucun résultat en la matière. Par ailleurs, comme nous souhaitons explorer l'intégralité de l'index, nous n'avons pas de section de requête : les agrégations seront calculées avec l'ensemble des documents correspondant à la requête, laquelle aura comme valeur par défaut match_all. Pour finir, nous décidons d'utiliser une agrégation cardinality qui nous permettra de savoir combien de valeurs uniques nous avons pour le champ play_name.
```python
{
    "took": 67,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 111393,
        "max_score": 0,
        "hits": []
    },
    "aggregations": {
        "Total plays": {
            "value": 36
        }
    }
}
```
À présent, répertorions les pièces qui reviennent le plus souvent dans notre ensemble de données :
```python
GET localhost:9200/shakespeare/_search
{
    "size":0,
    "aggs" : {
        "Popular plays" : {
            "terms" : {
                "field" : "play_name.keyword"
            }
        }
    }
}
```
Voici le résultat :
```python
{
    "took": 35,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 111393,
        "max_score": 0,
        "hits": []
    },
    "aggregations": {
        "Popular plays": {
            "doc_count_error_upper_bound": 2763,
            "sum_other_doc_count": 73249,
            "buckets": [
                {
                    "key": "Hamlet",
                    "doc_count": 4244
                },
                {
                    "key": "Coriolanus",
                    "doc_count": 3992
                },
                {
                    "key": "Cymbeline",
                    "doc_count": 3958
                },
                {
                    "key": "Richard III",
                    "doc_count": 3941
                },
                {
                    "key": "Antony and Cleopatra",
                    "doc_count": 3862
                },
                {
                    "key": "King Lear",
                    "doc_count": 3766
                },
                {
                    "key": "Othello",
                    "doc_count": 3762
                },
                {
                    "key": "Troilus and Cressida",
                    "doc_count": 3711
                },
                {
                    "key": "A Winters Tale",
                    "doc_count": 3489
                },
                {
                    "key": "Henry VIII",
                    "doc_count": 3419
                }
            ]
        }
    }
}
```
Nous pouvons voir les 10 valeurs les plus populaires de play_name. Si vous souhaitez afficher plus de valeurs (ou moins) dans l'agrégation, n'hésitez pas à consulter la documentation.

Cap à présent vers la prochaine étape. Je suis sûr que vous pouvez deviner de quoi il retourne... Il s'agit de combiner les agrégations. Supposons que nous souhaitions connaître le nombre de scènes, d'actes ou de lignes que nous avons dans l'index, voire dans une pièce. Pour le savoir, nous devons imbriquer des agrégations dans des agrégations :
```python
GET localhost:9200/shakespeare/_search
{
    "size":0,
    "aggs" : {
        "Total plays" : {
            "terms" : {
                "field" : "play_name.keyword"
            },
            "aggs" : {
                "Per type" : {
                    "terms" : {
                        "field" : "_type"
                     }
                }
            }
        }
    }
}
```
Voici une partie de la réponse :
```python
    "aggregations": {
        "Total plays": {
            "doc_count_error_upper_bound": 2763,
            "sum_other_doc_count": 73249,
            "buckets": [
                {
                    "key": "Hamlet",
                    "doc_count": 4244,
                    "Per type": {
                        "doc_count_error_upper_bound": 0,
                        "sum_other_doc_count": 0,
                        "buckets": [
                            {
                                "key": "line",
                                "doc_count": 4219
                            },
                            {
                                "key": "scene",
                                "doc_count": 20
                            },
                            {
                                "key": "act",
                                "doc_count": 5
                            }
                        ]
                    }
                },
                ...
```
Il existe de nombreuses agrégations différentes dans Elasticsearch : agrégations utilisant les résultats d'agrégations, agrégations d'indicateurs tels que cardinality, agrégations de buckets tels que terms… Je vous laisse consulter la liste des agrégations possibles pour déterminer celles qui conviendraient aux cas d'utilisation que vous avez peut-être déjà en tête. Et pourquoi pas l'agrégation Significant terms pour déterminer ce qui est extraordinairement ordinaire ?

Nous sommes arrivés à présent au terme de cette deuxième section. Résumons ce que nous avons fait :

- Nous avons utilisé l'API Bulk pour ajouter des pièces de Shakespeare.
- Nous avons réalisé des recherches simples, en étudiant le format générique pour effectuer des requêtes via Query DSL.
- Nous avons procédé à une recherche spécifique de texte dans un champ.
- Nous avons exécuté une recherche composée, combinant deux recherches textuelles.
- Nous avons proposé d'ajouter une deuxième recherche combinée.
- Nous avons proposé de tester différentes options dans le corps de la requête.
- Nous avons présenté le concept d'agrégations, avec une introduction succincte aux types de mapping et de champ.
- Nous avons calculé le nombre de pièces qui se trouvent dans notre ensemble de données.
- Nous avons déterminé les pièces apparaissant le plus souvent dans l'ensemble de données.
- Nous avons combiné plusieurs agrégations pour connaître le nombre d'actes, de scènes et de lignes comptait chacune des dix pièces les plus fréquentes.
- Nous avons proposé de découvrir d'autres agrégations dans Elastic.

#Quelques conseils supplémentaires
Lors de ces exercices, nous avons abordé le concept de type dans Elasticsearch. Au final, un type n'est ni plus ni moins qu'un champ supplémentaire interne. Il est toutefois nécessaire de faire remarquer qu'à partir de la version 6, nous ne pourrons créer des index qu'avec un seul type, et qu'à partir de la version 7, les types devraient être supprimés. Pour en savoir plus, consultez cet article.

# Conclusion
Dans cet article, nous avons utilisé quelques exemples pour vous donner un aperçu d'Elasticsearch.

Mais ce n'est qu'une infime (très infime) partie de ce que vous pouvez faire dans Elasticsearch et dans la Suite Elastic. Un élément qu'il convient de mentionner avant que nous terminions : la pertinence. Avec Elasticsearch, la question qui se pose n'est pas seulement de savoir si le document que vous avez répond aux exigences de votre recherche, c'est également de savoir à quel point ce document répond à votre besoin. Et pour cela, Elasticsearch renvoie les résultats de recherche les plus pertinents pour votre requête. La documentation à ce sujet est vaste et pleine d'exemples.

Avant tout ajout de nouvelle fonctionnalité personnalisée, nous vous recommandons de vérifier dans la documentation si nous ne l'avons pas déjà mise en œuvre, ce qui vous permettrait d'en tirer facilement parti pour votre projet. Il est tout à fait possible qu'une fonctionnalité ou qu'une idée qui, d'après vous, pourrait être utile soit déjà disponible, étant donné que notre roadmap en matière de développement se base en très grande partie sur les suggestions de nos utilisateurs et de nos développeurs.

Vous avez besoin d'une authentification, d'un contrôle des accès, d'un chiffrement ou encore d'un audit ? Ces fonctionnalités sont d'ores et déjà disponibles dans Elastic Security. Vous souhaitez monitorer un cluster ? Vous avez tout ce qu'il vous faut dans le monitoring Elastic. Vous voulez créer des champs à la demande dans vos résultats ? Vous pouvez le faire via les champs Script. Vous avez besoin de créer des alertes par e-mail, Slack, Hipchat etc. ? Utilisez l'alerting. Vous aimeriez visualiser les données et les agrégations dans des graphes ? Nous vous offrons un environnement riche pour le faire : Kibana. Vous souhaitez indexer des données à partir de bases de données, de fichiers log, de files de gestion, ou de presque toutes les sources possibles et imaginables ? Logstash et Beats sont là pour ça. Quoi que vous ayez besoin, nous le proposons peut-être déjà. Jetez un œil à la documentation pour le savoir !



[Documentation tirée du site Officiel ](https://www.elastic.co/fr/blog/a-practical-introduction-to-elasticsearch)
