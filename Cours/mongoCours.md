
### Les rôles

```javascript
db.createUser ({"user": "superUser", "pwd": "mdp", "roles": [{"roles":"readWriteAnyDatabase", "db": "admin"}, "readWriteAnyDatabase"]})
```



### Insertion d'un document

``` javascript
db.maCollection.insert([
{
	"une_cle": "ma_cle"
},
{
	"une_cle": "ma_cle",
	"cle_voiture": "ma_cle_voiture"
}
])
```


### Modification d'un document

```javascript
db.maCollection.update(
{"nom": "dupont"},
{
	$set : {"nom": "dupont"}
},
{"multi": true } 
// "multi": true permet de faire un updateMany sinon le update fait un updateOne
)
```

effectuer recherche sur la methode 'findAndModify'


### Validation des documents

```javascript
var athletes = [{
	"nom" : "Bolt",
	"prenom" : "Usain",
	"discipline" : "Course" 
},
{
	"nom" : "Eclair",
	"prenom" : "Pierre",
	"discipline" : "Course"
},
{
	"nom" : "Zeus",
	"prenom" : "Kevin"  
}]

db.athletes.insertMany(athletes)

var proprietes = {
"nom" : {
	"bsonType" : "string",
	"pattern" : "^[A-Z].*",
	"description" : "Chaîne de caractère + expre régulière"},
"prenom" : {
	"bsonType" : "string",
	"description" : "Chaîne de caractère - obligatoire"},
"discipline": {
	"enum": ["course", "lancer"]}
}

// Maintenant que nos reles sont etablies, nous allons modifier la collection a l'aide de la commande 'collMod'

db.runCommand(
{
"collMod" : "athletes",
"validator" : {
	$jsonSchema: {
		"bsonType": "object",
		"required": ["nom","prenom","discipline"],
		"properties": proprietes
	}
}})
```


### Rechercher des documents

$ne : different de
$gt : superieur a
$gte : superieur ou egal 
$lt : ...
$lte : ...
$in et $nin : absence ou presence

```javascript
db.maCollection.find({"age": {$lt: 50, $gt:20} })
db.maCollection.find({"age": {$nin: [23, 45, 78] }})
db.maCollection.find({"age": {$exists: true }})
```


### Les operateurs de tableau

```javascript
{ $push: {<champ>: <valeur>, ...} }
{ $pull: {<champ>: <valeur>, ...} } 
 
 db.hobbies.updateOne({"_id": 1}, {$push: {"passions": "Ma Passion"} })
 db.hobbies.updateOne({"_id": 1}, {$pull: {"passions": "Ma Passion"} })
```

### Les tableaux de docuements

```javascript
db.eleves.find({"notes.note": 10})
```

Renvoyer les documents dont les eleves ont au moins uen note entre 10 et 15 dans une matiere quelconque
```javascript
db.eleves.find({
"notes": {
	$elemMatch: {
		"note": {$gt: 10, $lte: 15}
	}
}
}) //fonctionne avec un ET logique

db.eleves.find({"notes.note": {$gt: 10, $lte: 15}},{"_id": 0, "nom": 1, "notes":1}) //fonctionne avec un OU logique

db.eleves.find({"notes.0.note": {$lt:10}},{"_id": 0, "nom": 1, "notes":1})
```


### Le tri


```javascript
curseur.sort(<tri>)
```


### Les index

La nature de votre application devra impacter votre logique d'indexation: est-elle ecriture (write-heavy) ? ou lecture (read-heavy) ?

```javascript
//requête générique
db.collection.createIndex(<champ + type>, <option?>)

//requête
db.personnes.createIndex({"age": -1})

//réponse
'age_-1'


//requête
db.personnes.getIndexes()

//réponse
[
{ v: 2, key: { _id: 1 }, name: '_id_' },
{ v: 2, key: { age: -1 }, name: 'age_-1' }
]


db.personnes.insertMany( [
{"nom": "Jean", "prenom": "René", "interets": ["jardinage", "bricolage"], "age": 77},  
{"nom": "Grimes", "prenom": "Rick", "interets": ["bridge", "cuisine"], "age": 75},  
{"nom": "Dixon", "prenom": "Daryl", "interets": ["jardinage",   "pétanque"], "age": 79},  
{"nom": "Grimes", "prenom": "Carl", "interets": ["cuisine"], "age": 66},  
{"nom": "Apouroux", "prenom": "Agathe", "interets": ["cuisine", "pétanque"], "age": 57},  
{"nom": "Grimes", "prenom": "Michone","interets": ["jardinage"], "age": 80},  {"nom": "Walking", "prenom": "Aron","interets": ["jardinage"], "age": 75},  
{"nom": "Gaby", "prenom": "Mariette","interets": ["jardinage", "bridge"], "age": 20},
{"nom": "Paul", "prenom": "Jean","interets": ["jardinage", "bridge"], "age": 66},
{"nom": "Walking", "prenom": "Magna","interets": ["jardinage", "bridge"], "age": 35},
{"nom": "Porter", "prenom": "Eugene","interets": ["jardinage", "bridge"], "age": 56},
{"nom": "Gaby", "prenom": "Michelle","interets": ["jardinage", "bridge"], "age": 13},
{"nom": "Michelle", "prenom": "John","interets": ["jardinage", "bridge"], "age": 54},
{"nom": "Bernard", "prenom": "Mariette","interets": ["jardinage", "bridge"], "age": 36},
{"nom": "Grimes", "prenom": "Judith","interets": ["jardinage", "bridge"], "age": 36},
{"nom": "Dixon", "prenom": "LeChien","interets": ["jardinage", "bridge"], "age": 46},
{"nom": "Walking", "prenom": "Maggie","interets": ["jardinage", "bridge"], "age": 25},
{"nom": "Walking", "prenom": "Negan","interets": ["jardinage", "bridge"], "age": 54},
{"nom": "Maxime", "prenom": "Bernard","interets": ["jardinage", "bridge"], "age": 36},
{"nom": "Mauraux", "prenom": "Arthur","interets": ["jardinage", "bridge"], "age": 56},
{"nom": "Gaby", "prenom": "Popins","interets": ["jardinage", "bridge"], "age": 72},
{"nom": "Popins", "prenom": "Mick","interets": ["jardinage", "bridge"], "age": 46},
{"nom": "Walking", "prenom": "Yumiko","interets": ["jardinage", "bridge"], "age": 23},
{"nom": "Denverre", "prenom": "John","interets": ["jardinage", "bridge"], "age": 71},
{"nom": "Potter", "prenom": "Harry","interets": ["jardinage", "bridge"], "age": 56},
{"nom": "Wesley", "prenom": "Hermione","interets": ["jardinage", "bridge"], "age": 23},
{"nom": "Wesley", "prenom": "Ron","interets": ["jardinage", "bridge"], "age": 30},
{"nom": "Jedusor", "prenom": "Tom","interets": ["jardinage", "bridge"], "age": 10},
{"nom": "Rogue", "prenom": "Cevrus","interets": ["jardinage", "bridge"], "age": 30},
{"nom": "Dumbeldor", "prenom": "Albus","interets": ["jardinage", "bridge"], "age": 20},
{"nom": "Potter", "prenom": "Albus","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Londubard", "prenom": "Nevil","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lothbrok", "prenom": "Ragnar","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lothbrok", "prenom": "Lagerta","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lothbrok", "prenom": "Biorn","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lothbrok", "prenom": "Ivar","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "viking", "prenom": "Floki","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "viking", "prenom": "Hegbert","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Black", "prenom": "Sirius","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Drako", "prenom": "Malefoy","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lestrange", "prenom": "Beattrix","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Lupin", "prenom": "Remus","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Pettigrow", "prenom": "Peter","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Potter", "prenom": "Ginny","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Drakono", "prenom": "Norbert","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Malefoy", "prenom": "Lucius","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Potter", "prenom": "Hedwige","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Chang", "prenom": "Cho","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Walsh", "prenom": "Shane","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Grimes", "prenom": "Lori","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Harrison", "prenom": "Andrea","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Horvath", "prenom": "Dale","interets": ["jardinage", "bridge"], "age": 40},
{"nom": "Rhee", "prenom": "Glenn","interets": ["jardinage", "bridge"], "age": 40},




]  )

```

MongoDb permet l'utilisation de deux types d'index qui permettetn de gerer les requetes geospatiales:
- Les index de type `2dsphere` sont utiliser par des requetes geospatiales intervenant sur une srface spherique
- les index `2d` conernent des requetes intervenant sur un plan Euclidien.

Pour un champ nomme `donneSpatiales`d'une collection `cartographe` vous pouvez par exemple creer un index de type `2d` avec la commande:
```javascript
db.cartographie.createIndex{{"donneesSpatiales": "2d"}}
```

Pour la création d'un index `2dsphere` on utilisera plutot:
```javascript
db.cartographie.createIndex{{"donneesSpatiales": "2dsphere"}}
```

Les insex `2d`font intervenir des coordonnes de type `legacy` 

```javascript

db.plan.insertOne({"nom": "firstPoint", "geoData": [1,1] })

db.plan.insertOne({"nom": "firstPoint_bis", "geoData": [4.7, 44.5]})

db.plan.insertOne({"nom": "firstPoint_bis2", "geoData": {"lon":4.7, "lat": 44.5}
})

```



### Les objets GeoJSON


```javascript
{type: <type objet>, coordinates: <coordonnees>
}
```


### Le type Point

```javascript
{
"type": "Point",
"coordinates": [14.0, 1.0]
}
```

### Le type Multipoint

```javascript
{
"type": "Point",
"coordinates": [
	[14.0, 1.0], [10.0, 15.5]
]
}
```



```javascript

var polygon =[
[43.9548, 4.88143],
[43.95475, 4.88779],
[43.95045, 4.81897],
[43.4657, 4.80449],
[43.9548, 4.80143]
]

db.avignon.find({
"localisation": {
$geoWithin: {
	$geometry: {
		type: "Polygon",
		coordinates: [polygon]
		}
	}
}
}
}, {"_id": 0, "nom":1})
```



### Le framwork d'agregation

 met a disposition un puissant outil d'analyse et de traitement de l'information: le pipeline d'agragation (ou framwork) Metaphore du tapis roulant d'usine

Mehtode utilisee:
```javascript
db.collection.aggregate(pipeline, options)
```
- pipeline: designe un tableau d'étapes
- options: designe un document

Parmi les options, nous retiendrons:
- collation, permet d'affecter une collation a l'operation d'aggregation
- bypassDocumentValidation: fonctionne avec un operateur appele $out et permet de passer au travers de la validation des documents.
- allowDiskUse: donne la possibilite de faire deborder les operations d'ecriture sur le disque

vous pouveez appeler aggregate sans argument:
```javascript
db.personnes.aggregate()
```

```javascript

var pipeline2 = [{   
    $match : {  
        "interets": "jardinage"  
    },  
    $project: {  
        "_id": 0,  
        "nom": 1,  
        "prenom": 1,  
        "super_senior": {$gte: ["$age",70]}  
    }  
},  
{  
    $match: {  
        "super_senior": true  
    }  
}]

var pipeline3 = [{
	$match : {
		"interets": "jardinage"
	}},{
	$project:{
		"id": 0,
		"nom": 1,
		"prenom": 1,
		"ville": "$adresse.ville"
	}
},
{
	$match:{
		"ville": {$exists: true}
	}
}]

db.personnes.aggregate(pipeline3)
```

