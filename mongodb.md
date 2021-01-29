![mongodb](media/mongodb.png)


## PRESENTATION

MongoDB est un système de gestion de base de donnée de type NoSQL (base non relationnelle).
Elle est scalable (repartissable sur plusieurs serveurs).
Elle ne nécessite ni schéma prédéfini des données ni de typage de champs.

## LES DONNEES

Le systeme manipule des données au format BSON (JSON Binaire).
Ces données prennent la forme de **documents** eux-même enregistrés dans des **collections**.

Les **collections** peuvent être comparée aux tables SQL et les **documents** aux enregistrements (lignes) dans les tables. 

Contrairement au base SQL, les collections n'ont pas de schéma defini, les champs (colonnes en SQL, clés en JSON) des documents sont donc libres et peuvent varier d'un document à l'autre.
Le seul champ commun à chaque document est le champs ***_id***.

Des champs peuvent être ajouté, modifié ou supprimés a tout moment. 

### COMPARAISON SQL / BSON

#### SQL

Donnée au format SQL, le schémas defini le nombre et le type de données par enregistrement (ici : ID [int], Nom [String], Prenom [String], Age [int])

```SQL
ID, Nom, Prénom, Âge
6, DUMOND, Jean, 43
7, PELLERIN, Franck,
8, MARTIN, Emile, 62
9, DOE, John, 51
```

#### BSON (JSON Binaire) 

Données au format BSON, forme object avec clés:valeur pour chaque champs.
Pas de schémas imposé donc le nombre de champs et le type est libre.
Le seul champs commun est l'ID. 

```json
{
"_id": ObjectId("4efa8d2b7d284dad101e4bc7"),
"Nom": "DUMOND",
"Prénom": "Jean",
"Âge": 43
},
{
"_id": ObjectId("4efa8d2b7d284dad101e4bc8"),
"Nom": "PELLERIN",
"Prénom": "Franck",
"Adresse":{"Rue": "1 chemin des Loges", "Ville": "VERSAILLES"}
},
{
"_id": ObjectId("4efa8d2b7d284dad101e4bc9"),
"Nom": "DOE",
"Prenom": "John",
"Âge": "51",
"Taille":175
}
```

### LES INTERFACES 

MongoDB est fourni avec un shell permettant, via le terminal, de lancer de requêtes en **javascript** sur la base.
Il est disponible via la commande `mongo`.

Les 2 interfaces graphiques majeures pour dialoguer avec les bases MongoDB sont :
* [Compass](https://www.mongodb.com/products/compass)
* [Robo3T](https://studio3t.com/download/).

Sinon MongoDB est livré avec des API pour s'interfacer avec les principaux langages de programmation (Python, JavaScript, C++, Java, Go ...).

### MES PREMIERES REQUETES DANS ROBO3T / SHELL MONGO 

#### AFFICHER/SELECTIONNER LES DATABASE

Via le shell l'affichage et la selection des databases se fait via les commandes :
* **affichage des databases** : `show dbs`
* **selection d'une database** : `use ma_database`

Robo3T permet de selectionner la database et la collection voulue sur le serveur via une arborescence.

> La commande `use` peut prendre en parametre un database non-existante, elle sera créé automatiquement lors de la première insertion dans sa première collection.

#### AFFICHER LES COLLECTIONS

Après selection d'une database, on peut visualiser ses collections via la commande : `show collections`

> Bien sur la commande n'affiche rien si la database est inexistante.


#### INSERER UN DOCUMENT

Pour inserer un nouveau document dans la collection on utilise les fonction :
* insertOne() => insertion d'un document dans la collection
* insertMany() => insertion de plusieurs document
* insert() => insertion de 1 ou plusieurs documents 

**Inserer un document**
```javascript
use sw //selection de ma database nommé sw

// definition de l'element obi
obi = {'fname':'Obi-Wan', 'lname':'Kenobi', 'job':'Jedi'}
// insertion de obi dans la collection "alliance" de la database "sw"
db.alliance.insertOne(obi)
// db.alliance.insert(obi) fonctionne aussi
```

**Inserer plusieurs documents**

```javascript
var millenium=[{"fname":"han", "lname":"solo", "job":"contrebandier"},
               {"fname":"Chewbacca", "job":"contrebandier"}]
db.alliance.insertMany(millenium)
// db.alliance.insert(millenium) fonctionne aussi
```

Si la database et la collection n'existaient pas, elle sont créé lors de la première insertion.  
On peut le controller via les commandes `show dbs` et `show collections` ou la fonction de collection exist() `db.alliance.exist()`.

#### RECHERCHE DE DOCUMENTS

Pour récupérer les documents d'une collection on utilise les commandes:
* findOne() => récupération d'un document (le premier document par défaut)
* find() => récupération de plusieurs documents (tous les documents par défaut)

On peut intégrer des paramêtres à ces fonction pour filtrer les recherches

* **Récupération de plusieurs documents**
```javascript
db.alliance.find()

=> resultat :

```javascript
/* 1 */
{
    "_id" : ObjectId("5ff2c5aeb3cbf1107ee2b6d5"),
    "fname" : "Obi-Wan",
    "lname" : "Kenobi",
    "job" : "Jedi"
}

/* 2 */
{
    "_id" : ObjectId("5ff2c7f3b3cbf1107ee2b6d6"),
    "fname" : "Han",
    "lname" : "Solo",
    "job" : "contrebandier"
}

/* 3 */
{
    "_id" : ObjectId("5ff2c7f3b3cbf1107ee2b6d7"),
    "fname" : "Chewbacca",
    "job" : "contrebandier"
}
```

* **Récupération du premier document**

```javascript
db.alliance.findOne()

=> Resultat
```javascript
/* 1 */
{
    "_id" : ObjectId("5ff2c5aeb3cbf1107ee2b6d5"),
    "fname" : "Obi-Wan",
    "lname" : "Kenobi",
    "job" : "Jedi"
}
```

#### CHOIX DES CHAMPS A AFFICHER

Les fonction find() et findOne() prennent un premier paramêtre pour filtre les documents renvoyés : **le paramêtre de requête**
Il peuvent également prendre un deuxième paramêtre : **le paramêtre de projection**; qui va nous permettre  de choisir les champs que l'on désire afficher sous la forme d'un objet.

```javascript
//find avec la requete en premier parametre (ici vide)
//et le deuxieme parametre pour le choix des champs a afficher (valeur 1) ou cacher (valeur 0)

db.alliance.find({}, {fname:1, lname:1, _id:0})

=> Resultat :

/* 1 */
{
    "fname" : "Obi-Wan",
    "lname" : "Kenobi"
}

/* 2 */
{
    "fname" : "Han",
    "lname" : "Solo"
}

/* 3 */
{
    "fname" : "Chewbacca"
}
```

#### COMPTER LES ELEMENT ENVOYES

Pour compter les élément envoyé, je peux utiliser :
* la methode count() (ou length()) sur le résultat de la fonction find()
* la methode count() de la collection qui prend en parametre une requete pour filtrer les éléments renvoyé à l'aide des opérateurs décris plus bas

```javascript
//fonction de curseur count()
db.alliance.find().count()
//ou
db.alliance.find().length()

//ou
db.alliance.count()

=> Resultat : 3
```

#### AFFICHER LES ELEMENTS UNIQUE PAR CHAMPS

La fonction de collection distinct() permet d'afficher les éléments unique d'un champs passé en paramêtre.

```javascript
db.alliance.distinct("job")

=> Resultat :
/* 1 */
[
    "Jedi",
    "contrebandier"
]

```

### CHARGER DES DONNEES STRUCTUREE DANS MONGODB

Dans les outils intégré à l'environnement MongoDB, la commande ***mongoimport*** permet d'intégrer un fichier de données structurées (format CSV, TSV ou JSON) dans une collections.

#### CHARGER UN FICHIER JSON

**Commande :**

```bash
mongoimport --db new_york --collection restaurants --type JSON --file restaurants.json --drop
```

**Détail des options :**
* --db => nom de la database pour l'import (créée si inexistante)  
* --collection => nom de la collection (créée si inexistante) 
* --type => le type de fichier (JSON, CSV ou TSV), par défaut JSON
* --file => fichier contenant les données 
* --drop => suppression de la collection si elle est déjà existante

#### CHARGER UN FICHIER CSV

**Commande :**

```bash
mongoimport --db meae --collection organismes --type CSV --file ambassade_complete.csv --headerline --drop --ignoreBlanks --columnsHaveTypes
```

**Détail des options :**
* --headerline => premiere ligne du fichier utilisée pour le nom des champs
* --ignoreBlanks => ignore les champs vide d'un document
* --columnsHaveTypes => cherche le type dans le nom du champs (nom des champs sous la forme name.type())

> L'import des tableau a partir des fichiers CSV est laborieux, donc j'ai choisi d'importer les élément tableau au format string et d'appliquer un processing dans mongo pour les retransformer en tableau.
> Je lance la commande suivante dans le terminal mongo
> ```
> use meae
> db.organismes.find({pays_sup:{$exists:1}}).forEach((r)=>
>{
>   db.organismes.updateOne({_id:r._id}, {$set:{pays_sup:r.pays_sup.split('|')}})
>});
> ```


### FILTRER MES REQUETES (_WHERE_)

Comme vu précedement, les fonctions find() et findOne() permettent de récupérer des documents dans la collection.  
En intégrant le bon object en parametre on peut filtrer la recherche pour ne récupérer que le/les documents qui nous interresse(nt).

#### FILTRE PAR CHAMPS

Pour filtrer les documents par valeur de champs on défini un objet sous le format suivant :
`{'<champs_1>':'valeur', '<champs_2>':'valeur' ...}`

* Ex 1: Dans la collections **organismes**, recupérer tous les documents décrivants les organismes en **ASIE**.

```javascript
var query = {'continent':'ASIE'}
db.organismes.find(query)

=> Resultat
...
/* 2 */
{
    "_id" : ObjectId("5ffb368c71851da7672ff695"),
    "nom" : "Ambassade de France en  Arabie saoudite",
    "type" : "ambassade",
    "adresse" : "Diplomatic Quarter, PO Box 94367",
    "ville" : "RIYAD",
    "pays" : "b'ARABIE SAOUDITE'",
    "telephone" : "+966114344100",
    "url" : "https://sa.ambafrance.org/",
    "latitude" : 24.684612808608,
    "longitude" : 46.628272533417,
    "iso2" : "SA",
    "fax" : "+966114344179",
    "email" : "admin-francais.riyad-amba@diplomatie.gouv.fr",
    "ouvert" : true,
    "iso3" : "SAU",
    "continent" : "ASIE"
}

/* 3 */
{
    "_id" : ObjectId("5ffb368c71851da7672ff696"),
    "nom" : "Consulat général de France à Djeddah",
    "type" : "consulat",
    "adresse" : "Quartier Al Hamra, rue Al Shouara",
    "ville" : "b'DJEDDAH'",
    "pays" : "b'ARABIE SAOUDITE'",
    "telephone" : "+966126681550",
    "url" : "https://djeddah.consulfrance.org/",
    "latitude" : 21.530841363155,
    "longitude" : 39.174941848163,
    "iso2" : "SA",
    "fax" : "+966126108910",
    "email" : "contact@consulfrance-djeddah.org",
    "ouvert" : true,
    "iso3" : "SAU",
    "continent" : "ASIE"
}

// 81 éléments en tout, a controler avec .count()
```

* Ex 2: Dans la collections **organismes**, recupérer tous les documents décrivants les **ambassades** en **ASIE**.

```javascript
var query = {'continent':'ASIE', 'type':'ambassade'}
db.organismes.find(query)

=> Resultat:
...
/* 2 */
{
    "_id" : ObjectId("5ffb368c71851da7672ff695"),
    "nom" : "Ambassade de France en  Arabie saoudite",
    "type" : "ambassade",
    "adresse" : "Diplomatic Quarter, PO Box 94367",
    "ville" : "RIYAD",
    "pays" : "b'ARABIE SAOUDITE'",
    "telephone" : "+966114344100",
    "url" : "https://sa.ambafrance.org/",
    "latitude" : 24.684612808608,
    "longitude" : 46.628272533417,
    "iso2" : "SA",
    "fax" : "+966114344179",
    "email" : "admin-francais.riyad-amba@diplomatie.gouv.fr",
    "ouvert" : true,
    "iso3" : "SAU",
    "continent" : "ASIE"
}

/* 3 */
{
    "_id" : ObjectId("5ffb368c71851da7672ff69c"),
    "nom" : "Ambassade de France en  Arménie",
    "type" : "ambassade",
    "adresse" : "8 rue Grigor Loussavoritch",
    "ville" : "b'EREVAN'",
    "pays" : "b'ARMENIE'",
    "telephone" : "+37460651950",
    "url" : "https://am.ambafrance.org/",
    "latitude" : 40.174077785656,
    "longitude" : 44.506623744965,
    "iso2" : "AM",
    "email" : "cad.erevan-amba@diplomatie.gouv.fr",
    "ouvert" : true,
    "iso3" : "ARM",
    "continent" : "ASIE"
}
...
// 42 éléments en tout
```

#### LES OPERATEURS DE REQUETES

Les operateurs permettent de faire des filtres plus élaborés sur les documents.

##### LES OPERATEURS DE COMPARAISON

Les operateurs de comparaisons permettent de comparer nos champs à des valeurs données ou à des listes.

**Liste des operateurs de comparaison disponibles :** 

|code|operateur|description|exemple|eq SQL| 
|:-:|:-:|---|---|---|
|\$eg|=|Egal à|"a":{"$eg":10}|WHERE a = 10|
|\$ne|≄|Différent de (not equal)|"a" : {"\$ne" : 10}|WHERE a != 10|
|\$gt, \$gte|>, ≥|Plus grand que (greater than)|"a" : {"\$gt" : 10}|WHERE a > 10|
|\$lt, \$lte|<, ≤|Plus petit que (less than)|"a" : {"\$lt" : 10}|WHERE a < 10|
|\$in|∈|Est dans la liste|"a" : {"\$in" : [10, 12, 15, 18] }|WHERE a IN (10, 12, 15, 18)|
|\$nin|∉|N'est pas dans la liste|"a" : {"\$in" : [10, 12, 15, 18] }|WHERE a NOT IN (10, 12, 15, 18)|

* **Operateur $eq**: 

Dans les requêtes les plus simple, cet operateur est plus complexe à utiliser que son équivalent `{<champs>:'valeur'}`.
On le rencontre surtout dans les requêtes les plus complexes (associé à l'opérateur $not défini plus loin).

Ex : Recupérer tous les documents décrivants les organismes en **ASIE**.

```javascript
var query = {continent:{$eq:"ASIE"}}
// idem query = {continent:"ASIE"}
db.organismes.find(query)

=> Resultat : ... //81 elements
```

* **Operateur $ne**

Cet opérateur est l'inverse de _$eq_.

Ex : Recupérer tous les documents décrivants les organismes **qui ne sont pas** en **ASIE**.

```javascript
var query = {continent:{$ne:"ASIE"}}
db.organismes.find(query)

==> Resultat: ... //224 elements
```

* **Operateur $gt et $gte**

Cet opérateur filtre les éléments dont la valeur du champs est plus grand (ou plus grand ou egal pour $gte) que la valeur donnée.

Ex : Recupérer tous les documents décrivants les organismes **situé en hemisphère nord** (latitude > 0).

```javascript

var query = {latitude:{$gt:0}}
db.organismes.find(query)

==> Resultat: ... //261 elements
```


* **Operateur $lt**

Cet opérateur filtre les éléments dont la valeur du champs est plus petit (ou plus petit ou egal pour $lte) que la valeur donnée.

Ex : Recupérer tous les documents décrivants les organismes **situé en hemisphère sud** (latitude < 0).

```javascript

var query = {latitude:{$lt:0}}
db.organismes.find(query)

==> Resultat: ... //44 elements
```

* **Operateur $in**
  
Cette operateur filtre les éléments dont la valeur du champs est contenue dans la liste.

Ex : Recupérer tous les documents décrivants les organismes **situés en ASIE ou en EUROPE**.

```javascript
var query = {continent:{$in:["EUROPE", "ASIE"]}}
db.organismes.find(query)

=> Resultat: ...//162 elements
```

* **Operateur $nin**
  
Cette operateur filtre les éléments dont la valeur du champs n'est pas contenue dans la liste.

Ex : Recupérer tous les documents décrivants les organismes qui **ne sont pas situés en ASIE ou en EUROPE**.

```javascript
var query = {continent:{$nin:["EUROPE", "ASIE"]}}
db.organismes.find(query)

=> Resultat: ...//143 elements
``` 

##### LES OPERATEURS LOGIQUES

Les operateurs logiques permettent de chainer les conditions pour filtrer encore plus finement le résultat. 

**Liste des opérateur logique disponibles :**

|code|operateur|description|exemple|eq SQL|
|:-:|:-:|---|---|---|
|\$and|៱|ET logique|"a" : {“\$and” : [{"\$lt" : 10}, {“$gt” : 5} ] }|WHERE a < 10 AND a > 5|
|\$or|៴|OU logique|"a" : {“\$or” : [{"\$gt" : 10}, {“$lt” : 5} ] }|WHERE a > 10 OR a < 5| 
|\$not|￢|Négation|“a" : {“\$not” : {"\$lt" : 10} }|WHERE NOT a < 10|
|\$exists|∃|La clé existe dans le document|“a” : {“\$exists” : 1}||
|\$size|-|test sur la taille d'une liste (uniquement par égalité)|“a” : {“\$size” : 5}||

* **Operateur $and**

$and permet de chainer des conditions et de renvoyer les résultats qui les respectent toutes.

On lui préfère souvent la formulation plus simple qui consiste a lister les conditions dans un objet

Ex: Récupérer tous les documents décrivants les **ambassades** en **ASIE**.

```javascript
var query = {$and:[{continent:"ASIE"}, {type:"ambassade"}]}
// idem var query = {continent:"ASIE", type:"ambassade"}
db.organismes.find(query)

=> Resultat: //42 elements
```

* **Operateur $or**

$or permet de chainer les conditions et de renvoyer les résultat qui en respecte au moins une.

Ex: Récupérer tous les documents décrivants les organismes situé **dans l'hémisphère nord** ou **en OCEANIE**.

```javascript
var query = {$or:[
                {
                    latitude:{$gt:0}
                }, 
                {continent:"OCEANIE"}
            ]}
db.organismes.find(query)

=> Resultat:... //267 elements
```

* **Operateur $not**

$not permet d'inverser la condition de  pris en paramêtre.

Ex: Récupérer tous les documents décrivants les organismes qui **ne sont pas situé dans l'hemisphère nord** (donc situé dans l'hémisphère sud).

```javascript
var query = {latitude:{$not:{$gt:0}}}
db.getCollection('organismes').find(query).count()

=> Resultat: ...//44 elements
```

Ex: Récupérer tous les documents décrivants les organismes qui **ne sont pas situé dans l'hemisphère nord** (donc situé dans l'hémisphère sud) et ne sont pas des ambassades.

```javascript
var query = {$and:[
                {latitude:{$not:{$gt:0}}},
                {type:{$not:{$eq:"ambassade"}}}
                ]
             }
db.getCollection('organismes').find(query)

=> Resultat: ...//13 elements
```

* **Operateur $nor**

$nor permet de filtrer les documents qui ne respectent aucune des conditions passés en valeur.

Ex: Récupérer tous les documents décrivant les organismes qui ne sont pas ouvert et qui ne sont pas des ambassades.

```javascript
var query = {$nor:[{ouvert:true}, {type:"ambassade"}]}
db.getCollection('organismes').find(query)

=> Resultat: 
/* 1 */
{
    "_id" : ObjectId("5ffb368c71851da7672ff79d"),
    "nom" : "Section consulaire de l'Ambassade de France à Damas",
    "type" : "consulat",
    "adresse" : "Rue Ata-al-Ayyoubi - Al Afif",
    "ville" : "b'DAMAS'",
    "pays" : "b'SYRIE'",
    "telephone" : "+963113390200",
    "url" : "https://sy.ambafrance.org/",
    "latitude" : 33.525395200088,
    "longitude" : 36.285677,
    "iso2" : "SY",
    "ouvert" : false,
    "iso3" : "SYR",
    "continent" : "ASIE"
}
```

##### LES OPERATEURS D'ELEMENTS

Les opérateurs d'élément permettent de filtrer les documents en fonction des champs qu'il contient ou du types des données

* **Operateur $type**

 $type permet de filter les documents en fonction du type de données contenues dans un champs.

* **Operateur $exist**

$exist permet de filtrer les documents en fonction de la présence ou non du champs en clef.

Ex: Récupérer tous les documents décrivants les organismes qui ont une information fax enregistré.

```javascript
var query = {fax:{$exists:1}}
db.getCollection('organismes').find(query)

=> Resultat: ... //72 elements
```

Ex: Recuperer tous les documents décrivant les organismes qui n'ont pas d'information fax enregistré.

```javascript
var query = {fax:{$exists:0}}
db.getCollection('organismes').find(query)

=> Resultat: ...//233 elements
```

##### LES OPERATEURS DE TABLEAU

Les opérateurs de tableau permettent de faire des recherche plus spécifique sur les tableau contenues dans nos données.

* **Operateur $size** :

$size permet de filtrer les documents en de la taille des tableaux contenus dans no champs.
Dommage,  $size ne peut pas contenir de plage de valeur. Pour tester plusieur taille on doit effectuer plusieurs requêtes.


Ex : Recuperer tous les documents décrivant les organisme qui sont référent pour 1 (et seulement 1) pays autre que selui où ils sont implantés (donc pays_sup contient un tableau de 1 élément).

```javascript
var query = {pays_sup:{$size:1}}
db.organismes.find(query)

=> Resultat : ... //12 elements
```

* **Operateur $all**

$all permet de filtrer les document suivant la valeur **exact** d'un tableau.

Ex: Recuperer tous les documents décrivant l'organisme référent pour une liste de pays décrite par la liste suivant : `["AG", "BB", "DM", "KN", "VC"]` 

```javascript
var query = {pays_sup:{$all:[ 
        "AG", 
        "BB", 
        "DM", 
        "KN", 
        "VC"
    ]}}
db.organismes.find(query)

=> Resultat : ... //1 element
```
* **Operateur $elemMatch**

$elemMatch permet de filter les documents en integrant des conditions sur le contenu des tableau en valeur des champs.
C'est surement l'opérateur le plus puissant/utile des opérateurs de tableau (iso2:DM).

Ex: Récupérer le document décrivant l'organisme référent pour "la Dominique".
![Dominique](https://s1.qwant.com/thumbr/0x0/a/8/bfcacc0ed04ef77aac4e9d7436d8e91bb7acf705942ba57bda51b6280a7531/coeur_de_drapeau_de_la_dominique_cartes_postales-r1c980a789c834cf8a124008d91bdd184_vgbaq_8byvr_324.jpg?u=https%3A%2F%2Frlv.zcache.fr%2Fcoeur_de_drapeau_de_la_dominique_cartes_postales-r1c980a789c834cf8a124008d91bdd184_vgbaq_8byvr_324.jpg&q=0&b=1&p=0&a=1)

```javascript
var query = {$or:[
                {iso2:'DM'},
                {pays_sup:{
                    $elemMatch:{$eq:'DM'}
                    }   
                }
              ]
            }
db.organismes.find(query)

=> Resultat : ... //1 element
```

$elemMatch fonctionne sur un tableau de valeur mais également sur un tableau contenant des objet.

Ex : Si notre champs pays_sup contenait les informations suivantes 

```javascript 
"pays_sup" : [ 
        {iso2: "AG", pays:"Antigua et Barbuda"},
        {iso2:"BB", pays:"Barbade"},
        {iso2:"DM", pays:"La Dominique"},
        {iso2:"KN", pays:"Saint Kitts et Nevis"},  
        {iso2:"VC", pays:"Saint Vincent et les Grenadines"}
    ]
```

La requête suivante renverai le même résultat :

```javascript
var query = {$or:[
                {iso2:'DM'},
                {pays_sup:{
                    $elemMatch:{iso2:'DM'}
                    }   
                }
              ]
            }
db.organismes.find(query)
```

> Pour les tableau contenant des valeur numérique, les opérateur de comparaison peuvent être utilisés.  

Des [opérateur de projection](https://docs.mongodb.com/manual/reference/operator/projection/) spécifiques permettent de faire une selection supplémentaire sur les valeurs du tableau retournées.  

###### Operateur projection $slice

L'opérateur de projection [$slice](https://docs.mongodb.com/manual/reference/operator/projection/slice/) permet de selectionner une valeur du tableau par index.

Il peut prendre comme valeur un nombre (le nombre de valeurs à retourner à partir de l'index 0) ou un tableau avec 2 valeurs numériques (index de départ, nombre d'éléments à partir de l'index)

Ex : Sur l'exemple précédent, sélectionner uniquement le premier index dans le tableau, ainsi que le nom, la ville et le pays de l'organisme

```javascript
var query = {$or:[
                {iso2:'DM'},
                {pays_sup:{
                    $elemMatch:{$eq:'DM'}
                    }   
                }
              ]
            }

// je selectionne le premier item de pays_sup, le nom, la ville, et le pays de chaque document 


var projector = {
    pays_sup:{$slice:1},
    nom:1,
    ville:1,
    pays:1
}

db.organismes.find(query, projector)

=> Resultat : 
{
    "_id" : ObjectId("600ae56023ad32971de0f28f"),
    "nom" : "Ambassade de France à Sainte-Lucie",
    "ville" : "b'CASTRIES'",
    "pays" : "b'SAINTE-LUCIE'",
    "pays_sup" : [ 
        "AG"
    ]
}
```
> Pour info : je peux utiliser des valeur négative pour récupérer les derniers éléments : \$slice:-3 ou \$slice: [-3, 3]. 

###### Opérateur projection $elemMatch

L'opérateur de projection [$elemMatch](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch) permet de sélectionner un/des elements en fonction de leur valeur.

Ex : Sur l'exemple précédent, retourner seulement les pays_sup avec une valeur "DM".

```javascript
var query = {$or:[
                {iso2:'DM'},
                {pays_sup:{
                    $elemMatch:{$eq:'DM'}
                    }   
                }
              ]
            }
//projector pour renvoyer uniquement la valeur DM du tableau
var projector = {
    pays_sup:{$elemMatch:{$eq:'DM'}},
    nom:1,
    ville:1,
    pays:1
}

db.organismes.find(query, projector)

=> Resultat:
/* 1 */
{
    "_id" : ObjectId("600ae56023ad32971de0f28f"),
    "nom" : "Ambassade de France à Sainte-Lucie",
    "ville" : "b'CASTRIES'",
    "pays" : "b'SAINTE-LUCIE'",
    "pays_sup" : [ 
        "DM"
    ]
}
```

##### LES OPERATEURS D'EVALUATIONS

* **Operateur $expr**

Dans son utilisation la plus basique, \$expr permet de filtrer les documents en comparant 2 champs entre eux.

> Attention : on change de DB pour passer sur la DB "games".

Ex : Récupérer les documents décrivant les jeux vidéo qui on été mieux vendu en Europe qu'en Amérique du Nord

```javascript

var query = {$expr:{
                $gt:['$EU_Sales', '$NA_Sales']
            }}
db.sales.find(query)

 => Resultat : //2494 éléments

```
> Dans l'exemple ci-dessus on voit que les références au champ sont représenté par le nom du champs précédé du caractère $

\$expr peut également servir a comparer un champs avec des conditions plus complexe définie avec des opérateurs d'agregation ($add, $multiply ...)

Ex : Récupérer les documents décrivant les jeux vidéo dont la vente an Amérique du Nord dépasse celle de l'Europe et le Japon réunie. 

```javascript
var query = {$expr:{
                $gt:[
                        '$NA_Sales',
                        {$add: ['$EU_Sales', '$JP_Sales']}
                    ]
            }}
db.sales.find(query)

=> Resultat : //9490 éléments
```

* **Operateur $regex**
  
\$regex permet de faire une recherche textuelle sur un champs en utilisant les expressions régulières.

$regex est associés à l'opérateur $option qui contient les options liés au expression régulières :
* i	(case insensitive) => ignore la casse du texte.
* x (extended) => ignore les espaces sauf ceux déclaré dans la regex.
* s => autorise l'operateur regex '.' à représenter tous les caractère en incluant \n et \r.
* m (multiline) => intègre la notion de multiline pour les regex contenant les opérateur '^' et '$'. Ces operateurs sont recherché à chaque début/fin de ligne au lieu de debut/fin de l'ensemble de la chaine.
  
Ex : Récupérer les documents décrivant les Mario Kart, donc recherche sur le Name contenant "mario" et "kart".

```javascript

var query = {Name:{$regex:/(mario).?(kart)/, $options:'i'}}
db.sales.find(query)

=> Resultat : //9 elements
```

* **Operateur $text**

\$text permet de faire une recherche de mots clef dans un ou plusieurs champs de notre collection.
Seul contrainte, il faut dabord que ces champs ai été déclaré dans la liste des _Indexes_ de la collection sous le type _text_.

Pour déclarer des index :

```javascript
// creation par champs
db.sales.createIndex({
    Name:"text",
    Genre:"text"
})
//creation avec tous les champs type string en index
db.sales.createIndex(
    {"$**":"text}
)
```

Après cette étape il suffit de déclarer un opérateur $search dans $text pour filter les documents.

Ex : Récupérer les documents contenant _Mario_ et/ou _Kart_ dans un des champs indexé de la collection.

```javascript
var query = {$text:{$search:"mario kart"}}
db.sales.find(query)

=> Resultat : //128 elements
```

Le caratère '-' permet d'exclure un mot de la recherche

Autre Ex: Récupérer les documents contenant _Mario_ et/ou _Karant t_ dans un des champs indexé de la collection.

```javascript
var query = {$text:{$search:"mario -kart"}}
db.sales.find(query).count()

=> Resultat : //103 elements
```

##### LES OPERATEUR DE GEOLOCALISATION

Les [opérateurs de géolocalisation](https://docs.mongodb.com/manual/reference/operator/query-geospatial/)
 permettent d'effectuer des filtres sur des données type [latitude, longitude].

Cette serie d'opérateur spéialisé pour traiter des données de géolocalisation.

Ex:  plus loin dans la partie pipeline \$match.


#### LES FONCTIONS DE CURSOR

Comme décris dans la documentation de MongoDB la methode de collection [find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/) renvoi un objet [Cursor](https://docs.mongodb.com/manual/reference/method/js-cursor) représentant le résultat de la requête.

Cette objet comporte certaines fonctions permettant de manipuler le résultat renvoyé par la requête.

##### FONCTION COUNT/SIZE

Renvoi la taille du curseur (le nombre de documents renvoyé).

###### >> FONCTION SORT

La fonction sort() permet de trier les documents en fonction des conditions mises en paramêtre.

Ces condition sont sous la forme `{<champs>:1}` pour un tri croissant sur le champs ou `{<champs>:-1}` pour un tri décroissant.

Ex: Recupérer les documents décrivant les jeux les plus vendus dans l'année 2006 dans l'ordre décroissant.

```javascript
var query = {Year:2006}
//projector pour selectionner les champs
var projector = {_id:0, Name:1, Genre:1, Global_Sales:1}
//parametre sort sur Global_Sales en décroissant
var sort = {Global_Sales:-1}

db.sales
    .find(query, projector)
    .sort(sort)

Resultat=>
/* 1 */
{
    "Name" : "Wii Sports",
    "Genre" : "Sports",
    "Global_Sales" : 82.74
}

/* 2 */
{
    "Name" : "New Super Mario Bros.",
    "Genre" : "Platform",
    "Global_Sales" : 30.01
}
...
```

###### >> FONCTION LIMIT

La fonction limit() permet de tronquer le résultat et de ne renvoyer que les premiers éléments suivant le paramètre donné.

Ex: Recupérer les documents décrivant les 5 jeux les plus vendus dans l'année 2006.

```javascript
var query = {Year:2006}
//projector pour selectionner les champs
var projector = {_id:0, Name:1, Genre:1, Global_Sales:1}
//parametre sort sur Global_Sales en décroissant
var sort = {Global_Sales:-1}

db.sales
    .find(query, projector)
    .sort(sort)
    .limit(3)

Resultat=>
/* 1 */
{
    "Name" : "Wii Sports",
    "Genre" : "Sports",
    "Global_Sales" : 82.74
}

/* 2 */
{
    "Name" : "New Super Mario Bros.",
    "Genre" : "Platform",
    "Global_Sales" : 30.01
}

/* 3 */
{
    "Name" : "Wii Play",
    "Genre" : "Misc",
    "Global_Sales" : 29.02
}
```

###### >> FONCTION SKIP

La fonction skip() permet d'éjecter les premiers résultat en fonction du paramêtre donné.

Ex: Recupérer les documents décrivant les 3 jeux **après** les 3 plus vendus dans l'année 2006.

```javascript
var query = {Year:2006}
//projector pour selectionner les champs
var projector = {_id:0, Name:1, Genre:1, Global_Sales:1}
//parametre sort sur Global_Sales en décroissant
var sort = {Global_Sales:-1}

db.sales
    .find(query, projector)
    .sort(sort)
    .limit(3)
    .skip(3)

Resultat=>

* 1 */
{
    "Name" : "Pokemon Diamond/Pokemon Pearl",
    "Genre" : "Role-Playing",
    "Global_Sales" : 18.36
}

/* 2 */
{
    "Name" : "The Legend of Zelda: Twilight Princess",
    "Genre" : "Action",
    "Global_Sales" : 7.31
}

/* 3 */
{
    "Name" : "Gears of War",
    "Genre" : "Shooter",
    "Global_Sales" : 6.11
}
```

#### LES PIPELINE D'AGGREGATION

Les pipeline d'aggregation sont un puissant framework de MongoDB permettant d'appliquer une série de transformations sur les documents entrant sous la forme d'opérations (de stage) successivent.

Chaque stages fait un processing spécifique sur les documents en entrée et passe les documents résultant au stage suivant.

##### LES STAGES

Les operateurs de stage représentent les étapes de processing appliqués sur les documents.

* **Stage $addFields**

\$addField permet de créer des champs supplémentaires dans les documents renvoyé.
On peut remplir ce nouveau champs avec une valeur ou en utilisant les autres champs du document (processing avec des opérateurs d'aggretation)

Ex : Dans la database meae creation d'un nouveau champs position qui est une combinaison de la latitude et la longitude

```javascript
// creation du stage addFields
var add_field_stage = {$addFields:
                        {
                            location:['$latitude', '$longitude']
                        }
                      };
// operation aggregate
db.organismes.aggregate([add_field_stage])

=> Resultat: 
/* 1 */
{
    "_id" : ObjectId("60019dd82f51f740a857f599"),
    "nom" : "Ambassade de France en  Afghanistan",
    "type" : "ambassade",
    "adresse" : "Avenue de Cherpour, Chahr-e-Nao",
    "ville" : "b'KABOUL'",
    "pays" : "b'AFGHANISTAN'",
    "telephone" : "+93700284032",
    "url" : "https://af.ambafrance.org/",
    "latitude" : 34.529204747183,
    "longitude" : 69.179513454437,
    "iso2" : "AF",
    "ouvert" : true,
    "iso3" : "AFG",
    "continent" : "ASIE",
    "location" : [ 
        34.529204747183, 
        69.179513454437
    ]
}
//  ... 
```

Les [opérateurs d'aggregation](https://docs.mongodb.com/manual/reference/operator/aggregation/) permettent d'appliquer des processing du plus simple au plus complexe lors de la création de nos nouveau champs.

Ex: Dans la DB games, création d'un nouveau champs JP_EU_Sales (addition des champs JP_Sales et EU_Sales) pour chaque documents.

```javascript

var addFieldsStage = {$addFields:
                        {
                            JP_EU_total:{
                                $add:['$EU_Sales', '$JP_Sales']
                            }
                        }
                    }

db.sales.aggregate([addFieldsStage])

=> Resultat : 
/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb6d"),
    "Rank" : 1,
    "Name" : "Wii Sports",
    "Platform" : "Wii",
    "Year" : 2006,
    "Genre" : "Sports",
    "Publisher" : "Nintendo",
    "NA_Sales" : 41.49,
    "EU_Sales" : 29.02,
    "JP_Sales" : 3.77,
    "Other_Sales" : 8.46,
    "Global_Sales" : 82.74,
    "JP_EU_total" : 32.79
}
//...
```

* **Stage $project**

\$project permet de selectionner les champs qui nous intéresse dans nos documents mais également de créér des champs (un peu comme $addFields) 

Ex : Selection des champs nom, adresse et ville et création d'un champs localisation pour chacun des documents

```javascript

var project_stage = {$project:
                        {
                            nom:1,
                            adresse:1,
                            ville : 1,
                            location:['$latitude', '$longitude']
                        }
                      };

db.organismes.aggregate([project_stage])

=> Resultat :
/* 1 */
{
    "_id" : ObjectId("60019dd82f51f740a857f599"),
    "nom" : "Ambassade de France en  Afghanistan",
    "adresse" : "Avenue de Cherpour, Chahr-e-Nao",
    "ville" : "b'KABOUL'",
    "location" : [ 
        34.529204747183, 
        69.179513454437
    ]
}
...
```

On peut également remplacer la valeur d'un champs déjà présent dans nos documents en en les redefinissant.

Ex : je redefinie la valeurs des champs NA_Sales, EU_Sales, JP_Sales et Other_Sales pour renvoyer une valeur en % de vente (XX_Sales/Global_Sales).

```javascript
var projectStage = { $project:{
                Name:1,
                Platform:1,
                NA_Sales:{
                      // j'utilise les operateurs $divide et $multiply pour calculer le pourcentage 
                      $multiply:[{$divide:['$NA_Sales', '$Global_Sales']}, 100]
                },
                EU_Sales:{
                      $multiply:[{$divide:['$EU_Sales', '$Global_Sales']}, 100]
                },
                JP_Sales:{
                      $multiply:[{$divide:['$JP_Sales', '$Global_Sales']}, 100]
                },
                Other_Sales:{
                      $multiply:[{$divide:['$Other_Sales', '$Global_Sales']}, 100]
                },
           }
        }
db.sales.aggregate([projectStage])

=> Resultat :
/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb6d"),
    "Name" : "Wii Sports",
    "Platform" : "Wii",
    "NA_Sales" : 50.1450326323423,
    "EU_Sales" : 35.0737249214407,
    "JP_Sales" : 4.55644186608654,
    "Other_Sales" : 10.2248005801305
}

// ...
```

* **Stage $match**

\$match permet de filtrer les documents renvoyés suivant la requête en paramêtre.
Les filtres appliqués à $match sont similaire à ceux employé en paramètre des fonctions find() et findOne().

Ex :  Dans la base games, je récupère tous les documents dont les ventes en Amerique du Nord représente 50% ou plus.

```javascript
//je prépare un stage $project pour redefinir NA_Sales
var projectStage = { $project:{
                Name:1,
                Platform:1,
                NA_Sales:{
                      $multiply:[{$divide:['$NA_Sales', '$Global_Sales']}, 100]
                }
           }
        }
// je prépare un stage $match pour filtrer mes document
var matchStage = {$match:{
                    NA_Sales:{$gte:50}
                }
            }
// j'enchaine les 2 stages pour obtenir les document
db.sales.aggregate([projectStage, matchStage])


=>Resultat : //8674 elements
```

Profitons en pour intégrer un exemple utilisant un opératueur de géolocalisation.
j'utilise \$geoWithin qui cherche les element contenu dans une zone donnée avec l'operateur \$center qui me permet de définir un cercle avec une localisation central et un rayon.

Ex : Dans la DB meae, rechercher les documents décrivant le organisme se trouvant dans un rayon de 200 km du Sculpture park en Hongrie.
Position : [48.006952412229225, 17.160799083406104]
200 km égal environ 2 pt de latitude longitude.

```javascript 
// stage addFields pour creer le nouveau champs position 
var addFieldsStage = {$addFields:{position:['$latitude','$longitude']}}
// stage de tri, je cherche une position contenu dans un cercle de 2pt autour de la position [48.006952412229225, 17.160799083406104]
var matchStage = {$match:{
                    position:{
                        $geoWithin:{
                            $center:[[48.006952412229225, 17.160799083406104], 2]
                        }
                    }
                 }}

db.organismes.aggregate([addFieldsStage, matchStage])

```

* **Stage $count**
  
\$count permet de compter les documents renvoyé par le stage précédent

Ex: Dans la base games, récupérer le nombre de documents décrivant les jeux qui on été vendu à 10 M d'exemplaires ou plus.

```javascript
// stage pour le filtre
var matchStage = {$match:{NA_Sales:{$gte:10}}}
// stage pour le comptage
var countStage = {$count:'NA_Sales'}

db.sales.aggregate([matchStage, countStage])

=> Resultat :
/* 1 */
{
    "NA_Sales" : 13
}
```

* **Stage sort**

\$sort permet de trier les documents renvoyés en fontion de la valeur d'un ou plusieurs champs.
Ce tri peut être ascending (valeur 1) ou descending (valeur -1 ).

Ex : Dans la base games, récupérer les documents décrivant les jeu vendu à 10 M d'exemplaire ou plus au USA.
Et les trier du plus vendu au moins vendu.
Renvoyer uniquement les champs NA_Sales, Name, Platform et Publisher

```javascript

// stage selection des champs
var projectStage = {$project: {NA_Sales:1, Name:1, Platform:1, Publisher:1}}
//stage filtre
var matchStage = {$match:{NA_Sales:{$gte:10}}}
//stage tri descending sur NA_Sales
var sortStage = {$sort:{NA_Sales:-1}}

db.sales.aggregate([projectStage, matchStage, sortStage])


=> Resultat : //13 elements
/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb6d"),
    "Name" : "Wii Sports",
    "Platform" : "Wii",
    "Publisher" : "Nintendo",
    "NA_Sales" : 41.49
}

```

\$sort permet également de trier des éléments de type text en s'appuyant sur un "textScore" calculé lors d'une recherche de text durant un stage $match.

```javascript
// stage match => recherche texte Mario et/ou Kart
var matchStage = {$match:{$text:{$search: 'Mario Kart'}}}
// stage de tri en fonction du score type textScore de la recherche
var sortStage =  { $sort: { score: { $meta: "textScore" }, posts: -1 } }

db.sales.aggregate([matchStage, sortStage])

=> Resultat : //128 element

/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a8583c19"),
    "Rank" : 16545,
    "Name" : "Mario & Luigi: Paper Jam & Mario Kart 7 Double Pack",
    "Platform" : "3DS",
    "Year" : 2015,
    "Genre" : "Misc",
    "Publisher" : "Nintendo",
    "NA_Sales" : 0,
    "EU_Sales" : 0,
    "JP_Sales" : 0.01,
    "Other_Sales" : 0,
    "Global_Sales" : 0.01
}

/* 2 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fba8"),
    "Rank" : 64,
    "Name" : "Mario Kart 64",
    "Platform" : "N64",
    "Year" : 1996,
    "Genre" : "Racing",
    "Publisher" : "Nintendo",
    "NA_Sales" : 5.55,
    "EU_Sales" : 1.94,
    "JP_Sales" : 2.23,
    "Other_Sales" : 0.15,
    "Global_Sales" : 9.87
}
//...
```

* **Stage $limit**

\$limit permet de limiter le nombre de documents renvoyé à un nombre choisit.

Ex : Dans la base games, récupérer les documents décrivant les 3 jeu les plus vendus au USA.
Renvoyer uniquement les champs NA_Sales, Name, Platform et Publisher

```javascript
// stage selection des champs
var projectStage = {$project: {NA_Sales:1, Name:1, Platform:1, Publisher:1}}
//stage tri descending sur NA_Sales
var sortStage = {$sort:{NA_Sales:-1}}
//stage limit pour renvoi des 3 premiers
var limitStage = {$limit:3}

db.sales.aggregate([projectStage, sortStage, limitStage])

=> Resultat : 
/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb6d"),
    "Name" : "Wii Sports",
    "Platform" : "Wii",
    "Publisher" : "Nintendo",
    "NA_Sales" : 41.49
}

/* 2 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb72"),
    "Name" : "Super Mario Bros.",
    "Platform" : "NES",
    "Publisher" : "Nintendo",
    "NA_Sales" : 29.08
}

/* 3 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb76"),
    "Name" : "Duck Hunt",
    "Platform" : "NES",
    "Publisher" : "Nintendo",
    "NA_Sales" : 26.93
}
```
* **Stage $skip**

Sous le même format que \$limit, le stage $skip permet d'afficher les documents du stage précédent en ignorant les premiers document

Ex : Dans la base games, récupérer le documents décrivant le 3° jeu le plus vendu au USA.
Renvoyer uniquement les champs NA_Sales, Name, Platform et Publisher

```javascript
// stage selection des champs
var projectStage = {$project: {NA_Sales:1, Name:1, Platform:1, Publisher:1}}
//stage tri descending sur NA_Sales
var sortStage = {$sort:{NA_Sales:-1}}
// stage limit pour garder les 3 premier
var limitStage = {$limit:3}
//stage $skip pour ignorer les 2 premiers
var skipStage = {$skip : 2}

db.sales.aggregate([projectStage, sortStage, limitStage, skipStage])

=> Resultat : 

/* 1 */
{
    "_id" : ObjectId("6001c3b22f51f740a857fb76"),
    "Name" : "Duck Hunt",
    "Platform" : "NES",
    "Publisher" : "Nintendo",
    "NA_Sales" : 26.93
}
...
```

* **Stage $sample**

\$sample permet de récupérer un échantillon de documents parmis l'ensemble du résultat renvoyé par la requête.

Ex : Dans la base game, récupérer 5 documents décrivant des jeux vidéo sortis en 2010.

```javascript

var projectStage = {$project: {NA_Sales:1, Name:1, Platform:1, Publisher:1}}

var matchStage = {$match:{Year:2010}}

var sampleStage = {$sample:{size:5}} 

db.sales.aggregate([matchStage, projectStage, sampleStage])

=> Resultat: //5 elements
...
```

* **Stage $group**

\$group permet de renvoyer des documents groupé suivant le champs passé en valeur de la cle _id.
On peut intéger un [operateur d'accumulation](https://docs.mongodb.com/manual/reference/operator/aggregation/group/#accumulators-group) sur les paramêtre suivant pour :
* selectionner un document en fonction de la valeur de son champs => operateur \$last, \$first, \$max, \$min ...
* lister les valeur d'un champ => operateur \$addToSet, \$push ...
* concatener les valeurs d'un champs => operateur \$sum, \$avg ...

Ex : Récupérer les document décrivant les 3 editeurs qui on vendu le plus de jeu au USA et en Europe.

```javascript
// stage group par Publicher, avec 2 champs résultant de la somme de NA_Sales et EU_Sales
var groupStage = {$group:{_id:"$Publisher", sumOfSalesNA:{$sum: "$NA_Sales"}, sumOfSalesEU: {$sum: "$EU_Sales"}}}
// stage sort pour tri des doc par sumOfSalesNA et sumOfSalesEU
var sortStage = {$sort:{sumOfSalesNA:-1, sumOfSalesEU:-1}}
// stage limit pour renvoi des 3 premier resultat
var limitStage = {$limit:3}

db.sales.aggregate([groupStage, sortStage, limitStage])

=> Resultat: 
/* 1 */
{
    "_id" : "Nintendo",
    "sumOfSalesNA" : 816.87,
    "sumOfSalesEU" : 418.74
}

/* 2 */
{
    "_id" : "Electronic Arts",
    "sumOfSalesNA" : 595.07,
    "sumOfSalesEU" : 371.27
}

/* 3 */
{
    "_id" : "Activision",
    "sumOfSalesNA" : 429.7,
    "sumOfSalesEU" : 215.53
}
```

Bien sur on peut grouper suivant plusieurs critère les chainant dans le premier parametre de \$group

Ex : Récupérer les document décrivant les premier 3 editeurs historique qui on vendu le plus de jeu au USA et en Europe .

```javascript
//group stage
var groupStage = {$group:{_id:{date:"$Year", pub:"$Publisher"},sumOfSalesNA:{$sum: "$NA_Sales"}, sumOfSalesEU: {$sum: "$EU_Sales"}}}
//sort stage 
var sortStage = {$sort:{"_id.date": 1, sumOfSalesNA:-1, sumOfSalesEU:-1}}
// limit stage
var limitStage = {$limit:3}

db.sales.aggregate([groupStage, sortStage, limitStage])

=> Resultat :
/* 1 */
{
    "_id" : {
        "date" : 1980,
        "pub" : "Atari"
    },
    "sumOfSalesNA" : 7.77,
    "sumOfSalesEU" : 0.49
}

/* 2 */
{
    "_id" : {
        "date" : 1980,
        "pub" : "Activision"
    },
    "sumOfSalesNA" : 2.82,
    "sumOfSalesEU" : 0.18
}

/* 3 */
{
    "_id" : {
        "date" : 1981,
        "pub" : "Activision"
    },
    "sumOfSalesNA" : 7.95,
    "sumOfSalesEU" : 0.46
}
```

* **Stage $bucket**

\$bucket est similaire au \$group mais en version plus évolué.
Il nous permet de faire des group par plage de valeurs.

Ex : Récupérer la somme des vente des Jeu au USA et en Europe par décennies.

```javascript 
// 
var bucketStage = {$bucket:{
        // je groupe par année
        groupBy: '$Year',
        // je definie mes plages (valeur basses)
        boundaries: [ 1980, 1990, 2000, 2010,2020],
        // je definie un nom de groupe pour les valeurs hors plage 
        default: "Other Years",
        // et mes valeurs de sortie
        output: {
            sumOfSalesNA:{$sum: "$NA_Sales"},
            sumOfSalesEU: {$sum: "$EU_Sales"}
        }
      }
  }

db.sales.aggregate([bucketStage])

=> Resultat :
/* 1 */
{
    "_id" : 1980.0,
    "sumOfSalesNA" : 235.66,
    "sumOfSalesEU" : 31.2
}

/* 2 */
{
    "_id" : 1990.0,
    "sumOfSalesNA" : 576.11,
    "sumOfSalesEU" : 282.87
}

/* 3 */
{
    "_id" : 2000.0,
    "sumOfSalesNA" : 2408.91,
    "sumOfSalesEU" : 1256.18
}

/* 4 */
{
    "_id" : 2010.0,
    "sumOfSalesNA" : 1112.48,
    "sumOfSalesEU" : 838.87
}

/* 5 */
{
    "_id" : "Other Years",
    "sumOfSalesNA" : 59.79,
    "sumOfSalesEU" : 25.01
} 
```
### EXERCICES

Un petit exercice sur les requête MongoDB est disponible sur [ce lien](https://www.w3resource.com/mongodb-exercises/#PracticeOnline).

#### AVANT DE SE LANCER

##### FORMAT DU DOCUMENT

Voici le format d'un document décrivant un restaurant :

```json
/* 1 */
{
    "_id" : ObjectId("60070049c063695401760cca"),
    "address" : {
        "building" : "469",
        "coord" : {
            "type" : "Point",
            "coordinates" : [ 
                -73.961704, 
                40.662942
            ]
        },
        "street" : "Flatbush Avenue",
        "zipcode" : "11225"
    },
    "borough" : "Brooklyn",
    "cuisine" : "Hamburgers",
    "grades" : [ 
        {
            "date" : ISODate("2014-12-30T00:00:00.000Z"),
            "grade" : "A",
            "score" : 8
        }, 
        {
            "date" : ISODate("2014-07-01T00:00:00.000Z"),
            "grade" : "B",
            "score" : 23
        }, 
        {
            "date" : ISODate("2013-04-30T00:00:00.000Z"),
            "grade" : "A",
            "score" : 12
        }, 
        {
            "date" : ISODate("2012-05-08T00:00:00.000Z"),
            "grade" : "A",
            "score" : 12
        }
    ],
    "name" : "Wendy'S",
    "restaurant_id" : "30112340"
}
```

Ce format présente des arborescences d'objets dans notre document.
Pour accéder à un object pour une comparaison ou une projection il suffit de chainer les champs comme on le fait en Javascript : `"para.subpara.subsubpara":valeur` (attention : le chainage doit être délimité par des ")

Ex : Je veux récupérer les documents décrvant les restaurant avec un zipcode 11225

```javascript
var query = {"address.zipcode":"11225"}
db.restaurants.find(query)
```
##### TYPER LES VALEURS

Pour filter nos documents ils est parfos necessaire de comparer nos champs avec un paramêtre du même type que la valeur du champs (ex pour les date en ISODate).
Pour identifier les types disponibles dans le shell, référez vous à la [Documentation](https://docs.mongodb.com/manual/core/shell-types/).

##### LA DOCUMENTATION 

Les éléments donné dans ce tutoriels sont suffisant pour cet exercice mais ne décrivent qu'une infome partie des éléments disponibles pour effectuer les requêtes.
Pour plus d'information n'hésitez pas à parcourir la [documentation](https://docs.mongodb.com/manual/reference) 






