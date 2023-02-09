### Exercice exo.md

#### Question 1:
Affichez l’identifiant et le nom des salles qui sont des SMAC:
```javascript
//requête
db.salles.find({"smac": {$eq: true }},{"_id": 1, "nom":1})

//réponse

{
_id: 1,
nom: 'AJMI Jazz Club'
},
{
_id: 2,
nom: 'Paloma'
}
```


#### Question 2:
Affichez le nom des salles qui possèdent une capacité d’accueil strictement supérieure à 1000 places:
```javascript
//requête
db.salles.find({"capacite": {$gt: 1000 }},{"_id": 0,"nom":1})

//réponse
{
nom: 'Paloma'
}
```


#### Question 3:
Affichez l’identifiant des salles pour lesquelles le champ adresse ne comporte pas de numéro:
```javascript
//requête
db.salles.find({"adresse.numero": null},{"_id": 1})

//réponse
{
_id: 3
}
```


#### Question 4:
Affichez l’identifiant puis le nom des salles qui ont exactement un avis:
```javascript
//requête
db.salles.find({"avis": {$size: 1}},{"_id": 1,"nom": 1})

//réponse
{
_id: 2,
nom: 'Paloma'
}
```


#### Question 5:
Affichez tous les styles musicaux des salles qui programment notamment du blues:
```javascript
//requête
db.salles.find({"styles": "blues"},{"_id": 0,"nom":1, "styles":1})

//réponse
{
nom: 'AJMI Jazz Club',
styles: [
'jazz',
'soul',
'funk',
'blues']
}
{
nom: 'Sonograf',
styles: [
'blues',
'rock']
}
```


#### Question 6:
Affichez tous les styles musicaux des salles qui ont le style « blues » en première position dans leur tableau styles:
```javascript
//requête
db.salles.find({"styles.0": "blues"},{"_id": 0,"nom":1, "styles":1})

//réponse
{
nom: 'Sonograf',
styles: [
	'blues',
	'rock'
	]
}
```


#### Question 7:
Affichez la ville des salles dont le code postal commence par 84 et qui ont une capacité strictement inférieure à 500 places (pensez à utiliser une expression régulière):
```javascript
//requête
db.salles.find({ $and: [
	{"adresse.codePostal": /84/},
	{"capacite": {$lt:500}},
]},
{"_id": 0,"adresse.ville": 1})

//réponse
{
adresse:{
	ville:'Avignon'
	}
}
{
adresse:{
	ville:'Le Thor'
	}
}
```


#### Question 8:
Affichez l’identifiant pour les salles dont l’identifiant est pair ou le champ avis est absent:
```javascript
//requête
db.salles.find({ $or: [
	{"avis": null},
	{"_id": {$mod:[2,0]}}
]
},{"_id": 1})

//réponse
{
_id: 2
}
{
_id: 3
}
```


#### Question 9:
Affichez le nom des salles dont au moins un des avis comporte une note comprise entre 8 et 10 (tous deux inclus):
```javascript
//requête
db.salles.find({"avis.note": {$gte: 8}},{"_id": 0,"nom": 1})

//réponse
{
nom: 'AJMI Jazz Club'
}
{
nom: 'Paloma'
}
```


#### Question 10:
Affichez le nom des salles dont au moins un des avis comporte une date postérieure au 15/11/2019 (pensez à utiliser le type JavaScript Date):
```javascript
//requête
db.salles.find({"avis.date": {$gt: ISODate("2019-11-15T00:00:00.000+00:00")}},{"nom": 1, "_id": 0})

//réponse
{
nom: 'AJMI Jazz Club'
}
```


#### Question 11:
Affichez le nom ainsi que la capacité des salles dont le produit de la valeur de l’identifiant par 100 est strictement supérieur à la capacité:
```javascript
//requête
db.salles.find({  
    "nom": {$exists: 1},  
    "capacite": {$exists: 1},  
    $expr: {$gt: [{$multiply: ["$_id",100]}, "$capacite"]}  
},{  
"nom": 1,  
"_id": 1, "capacite": 1,  
})
//réponse
{
_id: 3,
nom: 'Sonograf',
capacite: 200
}
```


#### Question 12:
Affichez le nom des salles de type SMAC programmant plus de deux styles de musiques différents en utilisant l’opérateur $where qui permet de faire usage de JavaScript:
```javascript
//requête
db.salles.find({$where: "this.smac == true && this.styles?.length > 2"},{"_id": 0,"nom": 1})

//réponse
{
nom: 'AJMI Jazz Club'
}

```


#### Question 13
Affichez les différents codes postaux présents dans les documents de la collection salles:
```javascript
//requête
db.salles.find({},{"_id":0, "adresse.codePostal": 1})

//réponse
{
adresse: {
	codePostal: '84000'
	}
}
{
adresse: {
	codePostal: '30000'
	}
}
{
adresse: {
	codePostal: '84250'
	}
}
```

#### Question 14
Mettez à jour tous les documents de la collection salles en rajoutant 100 personnes à leur capacité actuelle:
```javascript
//requête
db.salles.updateMany(
{},
{
	 $inc:{"capacite": 200}
})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 3,
modifiedCount: 3,
upsertedCount: 0
}
```


#### Question 15
Ajoutez le style « jazz » à toutes les salles qui n’en programment pas
```javascript
//requête
db.salles.updateMany(
{ 
	"styles": { $ne: "jazz" }
},
{
	$push: {"styles": "jazz"}
})

ou

db.salles.updateMany({"styles": {$exists:1}},{$addToSet: {"styles": "jazz"}})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 0,
modifiedCount: 0,
upsertedCount: 0
}
```


### Question 16
Retirez le style «funk» à toutes les salles dont l’identifiant n’est égal ni à 2, ni à 3:
```javascript
//requête
db.salles.updateMany(
{
	"_id": {$nin: [2,3]}, 
	"styles": {$exists:1}
},
{
	$pull: {"styles": "funk"}
})
//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
```


### Question 17
Ajoutez un tableau composé des styles «techno» et « reggae » à la salle dont l’identifiant est 3:
```javascript
//requête
db.salles.updateMany({"_id": {$in: [3]}, "styles": {$exists: 1}},{$addToSet: {"styles": ["techno", "reggae"]}})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
```


### Question 18
Pour les salles dont le nom commence par la lettre P (majuscule ou minuscule), augmentez la capacité de 150 places et rajoutez un champ de type tableau nommé contact dans lequel se trouvera un document comportant un champ nommé telephone dont la valeur sera « 04 11 94 00 10 »:
```javascript
//requête
db.salles.updateMany(
{
	"nom": {$regex:"^[pP]"},
	"capacite": {$exists: 1}
}, 
{
	$inc: {"capacite": 150},
	$set: {"contact": [{"téléphone": "04 11 94 00 10"}]}
})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}

//aperçu du document en question

//requête
db.salles.find({"_id": 2},{"nom": 1, "contact":1})

//réponse
{
_id: 2,
nom: 'Paloma',
contact: [
	{
	'téléphone': '04 11 94 00 10'
	}
}
```







### Question 19
Pour les salles dont le nom commence par une voyelle (peu importe la casse, là aussi), rajoutez dans le tableau avis un document composé du champ date valant la date courante et du champ note valant 10 (double ou entier). L’expression régulière pour chercher une chaîne de caractères débutant par une voyelle suivie de n’importe quoi d’autre est [^aeiou]+$:
```javascript
//requête
db.salles.updateMany(
{
	"nom": {$regex:"^[aAeEiIoOuUyY]"}
},
{
	$addToSet: {"avis": {"date": new Date() , "note": 10}} 
})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 2,
modifiedCount: 2,
upsertedCount: 0
}

//affichage des données modifiées
{
_id: 1,
nom: 'AJMI Jazz Club',
avis: [
	{
	date: 2019-11-01T00:00:00.000Z,
	note: 8
	},
	{
	date: 2019-11-30T00:00:00.000Z,
	note: 9
	},
	{
	date: 2023-02-08T10:01:28.633Z,
	note: 10
	}
]}

```


### Question 20
En mode upsert, vous mettrez à jour tous les documents dont le nom commence par un z ou un Z en leur affectant comme nom « Pub Z », comme valeur du champ capacite 50 personnes (type entier et non décimal) et en positionnant le champ booléen smac à la valeur « false »:
```javascript
//requête
db.salles.updateMany({"nom": {$regex: "^[Zz]"}},{$set: {"nom": "Pub Z","smac": false, "capacite": 50}},{"upsert": true })

//réponse
acknowledged: true,
insertedId: ObjectId("63e396879aa5eea31f45ff27"),
matchedCount: 0,
modifiedCount: 0,
upsertedCount: 1
```


### Question 21
Affichez le décompte des documents pour lesquels le champ _id est de type « objectId »:
```javascript
//requête
db.salles.find({"_id": {$type: ["objectId"]}}).count()
//réponse
1
```


### Question 22
Pour les documents dont le champ _id n’est pas de type « objectId », affichez le nom de la salle ayant la plus grande capacité. Pour y parvenir, vous effectuerez un tri dans l’ordre qui convient tout en limitant le nombre de documents affichés pour ne retourner que celui qui comporte la capacité maximale:
```javascript
//requête
db.salles.find({"_id": {$not: {$type: ["objectId"]}}},{"nom": 1, "capacite": 1}).sort({"capacite":-1}).limit(1)
//réponse
{
_id: 2,
nom: 'Paloma',
capacite: 4350
}
```


### Question 23
Remplacez, sur la base de la valeur de son champ _id, le document créé à l’exercice 20 par un document contenant seulement le nom préexistant et la capacité, que vous monterez à 60 personnes:
```javascript
//requête
db.salles.updateMany({"_id": {$type: ["objectId"]}},{$set: {"capacite": 60},$unset: {smac: ""}})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
```


### Question 24
Effectuez la suppression d’un seul document avec les critères suivants : le champ _id est de type « objectId » et la capacité de la salle est inférieure ou égale à 60 personnes:
```javascript
//requête
db.salles.deleteOne({"_id": ObjectId("63e26684e3fba2fe2f04e88c"), "capacite": {$lte: 60}})

//réponse
{
acknowledged: true,
deletedCount: 0
}
```


### Question 25
À l’aide de la méthode permettant de trouver un seul document et de le mettre à jour en même temps, réduisez de 15 personnes la capacité de la salle située à Nîmes:
```javascript
//requête
db.salles.updateOne({"adresse.ville": "Nîmes"},{$inc: {"capacite": -15}})
//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
```

