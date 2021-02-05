![mongodb](media/mongodb.png)


## MODIFICATION DES COLLECTIONS

MongoDB met à disposition plusieurs méthode de collection permettant de faire des requête de type INSERT, DELETE, UPDATE et REPLACE

### INSERT

Les insertions de documents sont gérées par les méthodes de collection insert(), insertOne() et insertMany() :
* insertOne() permet d'inserer 1 seul document
* insertMany() permet d'inserer plusieurs documents
* insert() permet d'insérer 1 ou plusieurs documents

Ces fonctions prennent le document à insérer en paramêtre.

Par défaut un paramêtre ***_id*** est automatiquement créé par mongodb lors de l'insertion.
Mais on a la possibilité de définir manuellement ***_id*** mais il doit obligatoirement être unique pour chaque document sinon une erreur est levée.

Ex : Insertion 1 document dans la collection _characters_ de la database _sw_ avec insertOne()

```javascript
//preparation du document
var doc = {
		"_id":1,
        "name": "Luke Skywalker",
		"gender": "male",
		"homeworld": "Tatooine",
		"species": "Human"
	}
//insertion du document dans la collection character
db.characters.insertOne(doc)

//insertion du document dans la collection character
db.characters.findOne()
=> Resultat : 
{
    "_id" : 1.0,
    "name" : "Luke Skywalker",
    "gender" : "male",
    "homeworld" : "Tatooine",
    "species" : "Human"
}

```

Ex: Insertion plusieurs document avec insert()

```javascript
var docs = [{
		"name": "C-3PO",
		"gender": null,
		"homeworld": "Tatooine",
		"species": "Droid"
	},
	{
		"name": "R2-D2",
		"gender": null,
		"homeworld": "Naboo",
		"species": "Droid"
	}]

db.characters.insert(docs)

db.characters.find()
=> Resultat :

/* 1 */
{
    "_id" : 1.0,
    "name" : "Luke Skywalker",
    "gender" : "male",
    "homeworld" : "Tatooine",
    "species" : "Human"
}

/* 2 */
{
    "_id" : 2.0,
    "name" : "C-3PO",
    "homeworld" : "Tatooine",
    "species" : "Droid"
}

/* 3 */
{
    "_id" : 3.0,
    "name" : "R2-D2",
    "homeworld" : "Naboo",
    "species" : "Droid"
}

```

Les méthodes insert() et insertMany() peuvent prendre un deuxième paramètre optionnel `{ordered:<bolean>}`.
Ce paramêtre permet de configurer l'ordre d'insertion dans la collection :
* Si _true_ les documents sont inséré dans l'ordre défini dans la requête.  
  En cas d'erreur lors de l'insertion d'un document, l'insertion des éléments suivant est ignorée
* Si _false_ les documents sont insérés dans le desordre.  
  Une erreur sur lors de l'insertion d'un document n'a auune incidence sur l'insertion des éléments suivants.


### DELETE

Les suppresions de documents sont gérées pas les méthodes de collection deleteOne(), deleteMany() et remove() :
* deleteOne() pour supprimer 1 seul document ()
* deleteMany() pour supprimer plusieurs documents
* remove() pour supprimer 1 ou plusieurs documents

Ces methodes prennent 2 paramêtres placé dans l'ordre suivant `deleteOne(filter, options)`: 
* _query_ : contient les paramêtres pour filter les documents à supprimer.
* _options_ : contient des options spécifiques pour l'update.  
  2 principales a retenir :
  * **justOne**: <boolean>, supprimer que le premier élément identifié pour la methode remove()
  * **hint** : <string|Document> Défini si la suppression ne doit s'appliquer que sur un index spécifique pour la méthode deleteOne().


Avec un paramêtre de requête vide la méthode deleteOne() supprime le premier document sinon il supprime le premier document renvoyé par le curseur.

Ex: deleteOne()

```javascript
//supression du premier document
db.characters.deleteOne({})

db.characters.find()

=> Resultat:
/* 1 */
{
    "_id" : 2.0,
    "name" : "C-3PO"
    "homeworld" : "Tatooine",
    "species" : "Droid"
}

/* 2 */
{
    "_id" : 3.0,
    "name" : "R2-D2",
    "homeworld" : "Naboo",
    "species" : "Droid"
}
```

Avec un paramêtre de requête vide les méthodes deleteMany() et remove() suppriment la totalité des documents de la collection.

Ex : remove()

```javascript
//supression du premier document
db.characters.remove({})

db.characters.find()

=> Resultat : // 0 elements

```

> Pour la suite du tutoriel on importe le fichier ***sw_characters.json*** dans la collection **characters** de la DB ***sw***. 

Le paramêtres de requête pour la selection des éléments à supprimer sont identiques à celles utilisées dans les méthodes find()...

Ex: Je veux supprimer toutes les documents avec une valeur _species_ à null

```javascript
//suppression des document avec species:null
var res = db.characters.remove({species:null})

//je fait un print de l'objet renvoyé 
print(res.toString())

=> Resultat:
WriteResult({ "nRemoved" : 5 }) //5 suppressions
```

On peut utiliser l'option _JustOne_ dans la méthode _remove()_ pour ne supprimer qu'un seul élément.

Ex :
```javascript 
var query = {species:null}

db.characters.remove(query, {justOne:true});

```

### UPDATE

Les updates de documents sont gérées par les méthodes de collection update(), updateOne() et updateMany() :
* updateOne() pour supprimer 1 seul document ()
* updateMany() pour supprimer plusieurs documents
* update() pour supprimer 1 ou plusieurs documents

Ces fonction prennent 3 paramêtres placés dans l'ordre suivant `update(filter, update, options)` :
* _filter_ : contient les paramêtres pour filter les documents à updater
* _update_ : contient les parametres d'update (objet ou pipeline)
* _options_ : contient des options spécifiques pour l'update

#### OPTIONS

Le paramêtre _options_ prend la forme d'un objet contenant des clès spécifique.
Il est utilisé en complément de notre parametre _update_ pour définir comment MongoDB doit réagir dans certaine situation.

On utilisera 3 clés principales pour notre besoin :

* **upsert**: <boolean>  
  Si le curseur renvoyé par _filter_ est vide, defini si un nouveau document décris par _update_ doit être intégré.
  Par défaut à _false_

* **arrayFilters**: [ <filterObject>, ... ]  
  Pour les updates sur les tableau défini des critère de filtre spécifique supplémentaires.
  Par défaut à _null_

* **hint**: <document|string>  
  Défini si l'update ne doit s'appliquer que sur un **index** spécifique.

* **multi**: <boolean>  
  Utilisé par la méthode _update()_ permet de définir si l'update doit s'effectuer sur le premier document renvoyé (multi:false) ou sur l'ensemble des documents renvoyés (multi:true).

#### FILTER

Le paramètre _filter_ est sous la même forme que les objets filtre utilisés dans les requête find(), findOne() et findMany()

#### UPDATE

Le parametre _update_ peut prendre la forme d'un objet ou d'un pipeline.  
Dans les 2 cas on utilise les [Update Operateur](https://docs.mongodb.com/manual/reference/operator/update-field/) pour définir les étapes de mise à jour.

##### LES OPERATEURS DE CHAMPS

* **Operateur \$set**

\$set permet de définir un nouveau champs avec une valeur ou remplacer la valeur d'un champs existant.

Ex: Ajout de 2 nouveaux champs modif et modif_count pour tous les documents

```javascript
//je prepare mon parametre update
var update = {$set:{
	//creation champs modif_date avec NOW
        "modif_date": new Date(),
	//creation champs modif_count:1
        "modif_count":1
    }}

//envoi de la requete, filtre vide pour updater tous les doc
db.characters.updateMany({}, update)

=> Resultat : 
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 87.0,
    "modifiedCount" : 87.0
}

db.characters.findOne()
=> Resultat:

/* 1 */
{
    "_id" : 7,
    "name" : "R5-D4",
    "gender" : null,
    "homeworld" : "Tatooine",
    "species" : "Droid",
    "score" : 192,
    "modif_count" : 1.0,
    "modif_date" : ISODate("2021-02-04T07:38:09.077Z")
}
```

Autre Ex : Integrer des informations supplémentaires sur la planete Tatooine en remplaçant le champs homeworld existant

```javascript

//preparation des data
var tatooine = {'name':"Tatooine",
    'sector': 'Arkanis',
    'system':'Tatoo'}

//preparation du filtre
var filter = {homeworld:'Tatooine'}
//preparation du update : je remplace la valeur existante par mon objet tatooine
var update = {$set:{homeworld:tatooine}}

//envoi de la requete
var res = db.characters.updateMany(
    filter,
    update
)

print(res)

=> Resultat :
{
    "acknowledged" : true,
    "matchedCount" : 10.0,
    "modifiedCount" : 10.0
}

db.characters.findOne({"homeworld.name":"Tatooine"})

/* 1 */
{
    "_id" : 7,
    "name" : "R5-D4",
    "gender" : null,
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 192,
    "modif_count" : 1.0,
    "modif_date" : ISODate("2021-02-04T07:38:09.077Z")
}
```

Tester les connaissances : remplir les informations concernant _Naboo_ (Infos sur [Wookipedia](https://starwars.fandom.com/fr/wiki/))

* **Operateur \$unset**

\$unset permet de supprimer des champs de nos documents en utilisant le format `{$unset:{<champs1>:"", ...}}`.  

Ex: Supprimer le champs gender pour tous les documents décrivant un _Droid_.

```javascript
//filtre sur species
var filter = {species:"Droid"}

//update : suppression de gender 
var update = {$unset:{gender:""}}

//requete
var res = db.characters.updateMany(filter, update)

print(res)

=>Resultat :
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 5.0,
    "modifiedCount" : 5.0
}

db.characters.findOne({species:"Droid"})

/* 1 */
{
    "_id" : 7,
    "name" : "R5-D4",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 192,
    "modif_date" : ISODate("2021-02-04T07:38:09.077Z"),
    "modif_count" : 1.0
}

```

* **Operateur \$rename**

\$rename permet de renommer un ou plusieurs champs.

```javascript
//parametre update avec rename champs modif en modifications
var update = {
		$rename:{
			modif_date: "last_modif_date"
		}
	}

db.characters.updateMany({}, update)

db.characters.findOne()

=> Resultat :

/* 1 */
{
    "_id" : 7,
    "name" : "R5-D4",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 192,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T07:38:09.077Z")
}
```

* **Operateur \$inc**

\$inc permet d'incrémenter la valeur d'un champs en fonction de valeur mise en paramêtre.

Ex: Modification des Skywalker, j'incrémente le compteur _modif\_count_ je met à jour _last\_modif\_date_

```javascript

//filtre sur les skywalker
var filter = {name:{$regex:/Skywalker/i}}
var update = {
    //j'update last_modif_date avec un objet DATE (par défaut NOW)
    $set:{last_modif_date:new Date()},
    //j'incremente modif_count
    $inc:{modif_count:1}
}

var res = db.characters.updateMany(filter, update)

print(res)

=> Resultat:
{ "nMatched" : 3, "nUpserted" : 0, "nModified" : 3 }

db.characters.find(filter)

=> Resultat:

/* 1 */
{
    "_id" : 10,
    "name" : "Anakin Skywalker",
    "gender" : "male",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Human",
    "score" : 154,
    "modif_count" : 2.0,
    "last_modif_date" : ISODate("2021-02-04T07:48:34.991Z")
}
...
```

* **Operateur \$currentDate**

\$currentDate nous permet d'intégrer un objet Timestamp() ou un objet Date() correspondant à ***NOW*** dans un champs des documents.

Le choix de l'objet (Timestamp ou Date) est fait au moyen de l'opérateur ***$type*** sous le format :

```json
    {$currentDate:{
        <champs>:{$type:<timestamp|date>}
    }
}
```

Ex: Modification de _Darth Vader_, j'incrémente le compteur _modif\_count_ et je met à jour _last\_modif\_date_ avec l'opérateur ***$currentDate***

```javascript
//filtre sur name : Darth Vader
var filter = {name:"Darth Vader"}

//update 
var update = {
            //j'update last_modif_date avec la current date type DATE
                $currentDate:{last_modif_date:{$type:'date'}},
            //j'incremente modif_count
                $inc:{modif_count:1}
            }

var res = db.characters.updateOne(filter, update)
print(res)
            
=> Resultat :
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)

=> Resultat :
/* 1 */
{
    "_id" : 3,
    "name" : "Darth Vader",
    "gender" : "male",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Human",
    "score" : 197,
    "modif_count" : 2.0,
    "last_modif_date" : ISODate("2021-02-04T08:21:34.447Z")
}
```

* **Operateurs \$min / \$max**

\$min ou $max permettent de modifier un champs seulement si la valeur de l'opérateur est supérieur (pour \$max) ou inférieur (pour \$min) à la valeur actuelle du champs.

Ex: Je decide de définir un score min à 50 et un score max à 150 pour chaque personnage.

```javascript
//update max pour remplacer les valeur inférieure à 50
var updateMax = { 
            $max:{score:50}
        }
//update min pour remplacer les valeur supérieure à 150
var updateMin ={ 
            $min:{score:150}
        }

//Mongo leve un conflit si les parametre min et max sont appliqué en même temps sur le meme champs
// donc update en 2 fois

db.characters.updateMany({}, updateMax)
var res = db.characters.updateMany({}, updateMin)

print(res)
=> Resultat :

/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 87.0, // 87 elements trouvé
    "modifiedCount" : 21.0 // 21 elements remplacé
}

db.characters.findOne()

=> Resultat :

/* 1 */
{
    "_id" : 7,
    "name" : "R5-D4",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 150.0,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z")
}

```

Si les champs associés aux opérateurs \$min et \$max ne sont pas définis dans les documents, ils sont créé lors de l'opération avec la valeur donnée.

Ces opérateurs fonctionne également sur des données de type date sous le format suivant :

```javascript

var update = {
            $min:{date_field : Date("2021-02-04")}
        }

```

* **Operateur \$mul**

\$mul remplace la valeur d'un champs sa valeur initiale multiplié par le paramêtre associé au champs.
Il prend la forme suivante :

```json
// update du champs 1 par sa valeur multiplié par 2
// update du champs 1 par sa valeur multiplié par 5
{ 
    $mul: { <champs1>: 2, <champs2>:5} 
}
```

Si le champs passé en paramêtre n'existe pas il le créé avec une valeur 0.

* **Operateur \$setOnInsert**

\$setOnInsert est associé au paramêtre **upsert** de l'objet _options_.

Quand **upsert** est défini à **true** et que le paramêtre filter ne renvoi aucun résultat, le document défini par le paramêtre _update_ est inséré dans la collection.  

\$setOnInsert permet de définir des champs qui seront créés dans dans le document que si on se retrouve dans cette situation.

Ex : Je veux updater un Darth Vadrr, species Droid. Si il n'existe pas je veux le créér.

```javascript
// filter Darth Vader, species Droid
var filter = {name:"Darth Vader", species:'Droid'}

// update
var update = {
    //update du résultat avec les parametre
   $set:{name:"Robot Darth Vader","species":"Droid", "homeworld":"Unknow"},
   //Si il non existant, nouveau champ newEntry
   $setOnInsert:{"newEntry":true}
}
//option upsert pour cree le document si non existant
var options={upsert:true}

db.characters.updateMany(filter, update, options)

=> Resultat :

/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 0.0,
    "modifiedCount" : 0.0,
    "upsertedId" : ObjectId("601c30cdfa6df235afe252b8")
}


db.characters.findOne({name:"Robot Darth Vader"})

=> Resultat :

/* 1 */
{
    "_id" : ObjectId("601c30cdfa6df235afe252b8"),
    "name" : "Robot Darth Vader",
    "species" : "Droid",
    "homeworld" : "Unknow",
    "newEntry" : true
}

```

##### LES OPERATEURS DE TABLEAUX

Les opérateur de tableaux permettent d'appliquer des modifications sur des items précis d'un tableau contenus dans un champs.
Il permettent également d'ajouter ou de supprimer un items précis.

> Avant de décrire les opérateurs, on va intégrer un champs supplémentaire dans tous nos documents.
> Nom du champs : ***grades***, valeur : ***[85, 80, 92, 80]***.
> On utilise ***updateMany()***

* **Operateur \$**

Associé à un champs contenant un tableau \$ permet de modifier le premier item, identifié par le filtre, du tableau.  

Ex : Pour tous les _Droid_ remplacer le premier grades code 80 par le grade code 81.

```javascript
//filtre sur species droid et les items 80 de grades
var filter = {species:'Droid', grades: 80}

// grades.$ represente l'index du premier item trouve
// je remplace sa valeur par 81
var update = {
   $set:{"grades.$": 81 }
}
        
db.characters.updateMany(filter, update)

=> Resultat
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 5.0,
    "modifiedCount" : 5.0
}

db.characters.findOne({species:'Droid'})

=> Resultat :
{
    "_id" : 7,
    "name" : "R5-D4",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 150.0,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ 
        85.0, 
        81.0, //item à 81
        92.0, 
        80.0
    ]
}
```

* **Opérateur $[]**

Associé à un champs contenant un tableau \$[] permet de modifier tous les items du tableau par une valeur donnée. 

Ex : Remplacer tous les grades associé à Darth Vader par 100.

```javascript

var filter = {name:"Darth Vader"}
var update = {
   $set:{"grades.$[]": 100 }
}
        
db.characters.updateOne(filter, update)

=>Resultat:
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne({species:'Human'})

=> Resultat : /* 1 */
{
    "_id" : 3,
    "name" : "Darth Vader",
    "gender" : "male",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Human",
    "score" : 150.0,
    "modif_count" : 2.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.156Z"),
    "grades" : [ 
        100.0, 
        100.0, 
        100.0, 
        100.0
    ]
}
```

* **Operateur $[\<identifier\>]**

Cet opérateur est lié au parametre _arrayFilters_ de l'objet _options_.

Associé à un champs contenant un tableau \$[\<identifier\>] permet de modifier un/des éléments spécifique en fonction du résultats renvoyé par le tableau _arrayFilters_.

L'objet passé en valeur de arrayFilters est similaire à ceux utilisés par les methode find()... 

Ex: Dans le document décrivant Obi-Wan Kenobi je veux remplacer les valeur de grade inférieure à 85 par la valeur 82.

```javascript
// filtre sur name
var filter = {name:"Obi-Wan Kenobi"}

// update de tous les items renvoyé par arrayFilter. 
// remplacement de la valeur existante par 82
var update = {$set:{"grades.$[item]":82}}

// option arrayFilter format tableau, recherche des item < 85
var options = {arrayFilters:[
                    {"item":{$lt:85}}
                    ]
               }

db.characters.updateOne(filter, update, options)

=> Resultat :

/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)

=> Resultat :

/* 1 */
{
    "_id" : 9,
    "name" : "Obi-Wan Kenobi",
    "gender" : "male",
    "homeworld" : "Stewjon",
    "species" : "Human",
    "score" : 96,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ 
        85.0, 
        82.0, 
        92.0, 
        82.0
    ]
}
```
* **Operateur \$pop** 

L'opérateur \$pop peut prendre 2 valeurs : 1 ou -1
Il permet de supprimer le premier élément (valeur 1) ou le dernier élément (valeur -1) d'un tableau.

Ex : Dans le document décrivant Anakin Skywalker je veux supprimer le dernier item de grades.

```javascript
//filter _id : 10 
var filter = {"_id" : 10}
// suppression de dernier item de grades
var update = {$pop:{grades:-1}}

db.characters.updateOne(filter, update)

=> Resultat:
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)

=> Resultat:

/* 1 */
{
    "_id" : 10,
    "name" : "Anakin Skywalker",
    "gender" : "male",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Human",
    "score" : 150.0,
    "modif_count" : 2.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.154Z"),
    "grades" : [ 
        80.0, 
        92.0, 
        80.0
    ] // le dernier item a ete supprime
}
```

* **Operateur \$pull / \$pullAll**

\$pull permet de supprimer les éléments d'un tableau en fonction de l'objet filtre passé en valeur.

Ex : Dans le document décrivant Chewbacca je veux supprimer les valeurs de grade supérieur à 90.

```javascript
// filtre
var filter = {name:"Chewbacca"}

// upgrade : suppression item supérieur à 90
var update = {$pull:{grades:{$gt:90}}}

db.characters.updateOne(filter, update)

=> Resultat :
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)

=> Resultat :
/* 1 */
{
    "_id" : 12,
    "name" : "Chewbacca",
    "gender" : "male",
    "homeworld" : "Kashyyyk",
    "species" : "Wookiee",
    "score" : 150,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ 
        85.0, 
        80.0, 
        80.0
    ]// valeur 92 supprime
}
```

\$pullAll est similaire à \$pull mais il prend en valeur un tableau.   
Toutes les valeurs égales à celles contenues dans le tableau sont supprimées.

```javascript
// suppression des valeurs 85,88,90 et 95 dans le tableau grades 
var update = {$pullAll:{grades:[85,88,90,95]}}

```

* **Operateur \$push**
  
\$push permet d'ajouter un/des éléments dans un tableau.  

En utilisant le format `{<champs>:valeur}` il permet d'insérer un élément unique à la fin du tableau.

Des opérateurs de modification permettent de faire des insertions plus spécifique :

* \$each : permet d'insérer plusieur éléments listé dans un tableau.
* \$position : permet de selectionner la position d'insertion. 
  Il indique la position de l'index à partir du début du tableau si positif, à partir de la fin si négatif
* \$sort : permet de trier les éléments insérés
  Il peut prendre comme valeur 1 (ascending), -1 (descending) ou un objet listant les champs du tableau avec l'ordre du tri (-1 ou 1) en valeur.
* \$slice : permet de selectionner les éléments et le nombre d'éléments intégrés dans le tableau final.
  Il prend en valeur :
  * 0 : tableau final vide
  * valeur positive : premiers éléments dans le tableau final
  * valeur negative : derniers éléments dans le tableau final
  

Ex : Dans le document décrivant C-3PO, j'intègre les valeurs **[75, 94, 83]** dans le tableau grades.
Je veux garder uniquement les 4 valeurs les plus faible après update.


```javascript

=> Document avant update

* 13 */
{
    "_id" : 1,
    "name" : "C-3PO",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 50.0,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ 
        85.0, 
        81.0, 
        92.0, 
        80.0
    ]
}

=> Requete :
//filtre
var filter = {name:"C-3PO"}
//update
var update = {
    //ajout dans tableau grades avec $push
    $push:{
        grades:{
            //ajout de plusieurs valeur avec $each
            $each:[75, 94, 83],
            //à la position 0 du tableau grades
            $position:0,
            //tri croissant du tableau grade après insertion
            $sort: 1,
            //je garde uniquement les 4 premiers élément du tableau grades
            $slice:4
    }
}}

db.characters.updateOne(filter, update)
=> Resultat :

/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)
=> Resultat
/* 1 */
{
    "_id" : 1,
    "name" : "C-3PO",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 50.0,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ //4 valeurs trié en ascending 
        75.0, 
        80.0, 
        81.0, 
        83.0
    ]
}
```

> ATTENTION : Essayer d'intégrer un tableau de données sans l'opérateur \$each donne le résultat suivant : `grades : [81,83, [75,94]]`

* **Operateur $addToSet**

\$addToSet permet d'ajouter un/des éléments dans un tableau comme si c'était un objet Set().
Si l'élément est déjà présent dans le tableau d'origine, il est ignoré.

On peut le coupler avec l'opérateurs de modification \$each pour intégrer plusieurs éléments.

Ex: Dans le document décrivant C3-PO, je veux ajouter les valeurs de grade **[72, 81, 83, 92]** si elle ne sont pas déjà présentes.

```javascript

var filter = {name:"C-3PO"}
var update = {$addToSet:
                {grades:
                    {$each:[72, 81, 83, 92]}
                }
            }
db.characters.updateOne(filter, update)

=> Résultat :
/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}
db.characters.findOne(filter) 
=> Resultat:

/* 1 */
{
    "_id" : 1,
    "name" : "C-3PO",
    "homeworld" : {
        "name" : "Tatooine",
        "sector" : "Arkanis",
        "system" : "Tatoo"
    },
    "species" : "Droid",
    "score" : 50.0,
    "modif_count" : 1.0,
    "last_modif_date" : ISODate("2021-02-04T13:48:59.147Z"),
    "grades" : [ /
        75.0, 
        80.0, 
        81.0, 
        83.0, 
        72.0, 
        92.0
    ]
    //seules les valeur non présentes dans le tableau 
    //d'origine sont intégrées
}
```

##### UPDATE AVEC PIPELINE

Le paramètre _update_ des methode updateOne(), updateMany() et update() peuvent également être sous la forme d'un pipeline (liste de stage).
Dans cette configuration seul les stages suivant sont disponible:
* \$addField (opérateur \$set) => créer /updater des champs
* \$project (opérateur \$unset) => supprimer des champs
* \$replaceRoot (opérateur \$replaceWith) => remplacer un document

Ex: Dans le documents décrivant le droid "R5-D4" , concaténer les opérations :
* compléter le homeworld
* supprimer le champs gender

```javascript
//filtre
var filter = {'name': "R5-D4"}
//update sous la forme d'un pipeline (liste de stage)
var update = [
    //stage set pour updater homeworld
    $set:{
        "homeworld":{
            "name" : "Tatooine",
            "sector" : "Arkanis",
            "system" : "Tatoo"
        }
    },
    //stage unset pour supprimer gender
    $unset:{gender:""}
]

var res = db.characters.update(filter, update)
```

### REPLACE

La methode _replaceOne()_ permet de remplacer un élément dans la collection.

Elle prend 3 paramètres placés dans l'ordre suivant `replaceOne(filter, replacement, options)` :
* _filter_ : idem paramètre update.
* _replacement_ : document à intégrer.
* _options_ : options similaires à celles utilisées dans l'update.
  2 options à retenir:
  * **upsert**:<boolean> : Si le curseur renvoyé par _filter_ est vide, defini si le document de remplacement doit être ajouté.
  * **hint**: <document|string> : option identique à celle utilisée dans l'update.

Ex : Remplacement du document décrivant Robot Darth vader

```javascript
//filter
var filter = {name:"Robot Darth Vader"}
//document de remplacement
var replacement = {
        "name" : "Robot Darth Vader",
        "species" : "Droid",
        "homeworld" : "Unknow"
    }
db.characters.replaceOne(filter, replacement)

=> Resultat :

/* 1 */
{
    "acknowledged" : true,
    "matchedCount" : 1.0,
    "modifiedCount" : 1.0
}

db.characters.findOne(filter)  

=> Résultat :

/* 1 */
{
    "_id" : ObjectId("601c30cdfa6df235afe252b8"),
    "name" : "Robot Darth Vader",
    "species" : "Droid",
    "homeworld" : "Unknow"
}
```

### VALEUR DE RETOUR

En cas de succès des fonction xxxxOne() et xxxxMany() (inserOne(), insertMany(), deleteOne()...) renvoie un objet contenant le status de la requête et les _\_id_ des documents inséré.

```json
/* 1 */
{
    "acknowledged" : true, // requete reconnu : true
    "insertedId" : [1.0, 2.0] // _id des documents iséré
}
```

En cas de défaut ses 2 fonction renvoi un objet de type **WriteError**.

Ex: Insertion d'un document avec un _\_id_ existant.
```json
WriteError({
	"index" : 0,
	"code" : 11000,
	"errmsg" : "E11000 duplicate key error collection: sw.characters index: _id_ dup key: { : 1.0 }",
	"op" : [{
		"_id" : 1,
		"name" : "Luke Skywalker",
		"gender" : "male",
		"homeworld" : "Tatooine",
		"species" : "Human"
    },
    {   
        "_id":2,
		"name": "C-3PO",
		"homeworld": "Tatooine",
		"species": "Droid"
	}]
})
```

En cas de succès des fonction insert(), update(), remove() l'objet renvoyé est un objet plus complexe de type [WriteResult](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#writeresult).

```json
{
	"writeErrors" : [ ],
	"writeConcernErrors" : [ ],
	"nInserted" : 1,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
}

```

En cas de défaut elle renvoi également un objet WriteResult contenant un objet writeError décrivant l'erreur.

```json
{
	"writeErrors" : [
		{
			"index" : 0,
			"code" : 11000,
			"errmsg" : "E11000 duplicate key error collection: sw.characters index: _id_ dup key: { : 4.0 }",
			"op" : {
				"_id" : 4,
				"name" : "Luke Skywalker",
				"gender" : "male",
				"homeworld" : "Tatooine",
				"species" : "Human"
			}
		}
	],
	"writeConcernErrors" : [ ],
	"nInserted" : 0,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
}
```

### FIND AND PROCESS

MongoDB comporte aussi des méthodes de collection permettant de récupérer les éléments actuel avant faire des modification sur la base :
* [findAndModify](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/)
* [findOneAndDelete](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndDelete/)
* [findOneAndReplace](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndReplace/)
* [findOneAndUpdate](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndUpdate/)

Le fonctionnement de ces méthodes est similaire au méthode standard décrite plus haut.
Pour plus d'info, veuillez consulter la documentation.

