
### Exercice validation.md

#### Question 1
Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

- nom sera obligatoire et devra être de type chaîne de caractères.
- capacite sera obligatoire et devra être de type entier (int).
- Dans le champ adresse, les champs codePostal et ville, tous deux de type chaîne de caractères, seront obligatoires.

```javascript
//requête
db.runCommand(
{
	"collMod": "salles",
	"validator": {
		$jsonSchema :{
			"bsonType": "object",
			"required": ["nom","adresse.ville","adresse.codePostal"],
			"properties": {
				"nom" : {
					"bsonType" : "string",
					"pattern" : "^[A-Z].*",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"capacite": {
					"bsonType" : "int",
				},
				"adresse.CodePostal" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"adresse.Ville" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				}
			}
		}
	}
})

//réponse
{ ok: 1 }
```


**Que constatez-vous lors de la tentative d’insertion suivante, et quelle en est la cause ?**
```javascript 
//requête
db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
)

//réponse
"MongoServerError:Document failed validation"

```

Quand j'execute la requête j'ai l'erreur suivante  `Document failed validation`. Cela veut donc dire dans un premier temp que mes regles de validation ont bien été prise en compte. 
Donc le problème vient pas de la requête en elle même, le problème viens des valeurs  que l'on souhaite inserer dans la base données. Si on regarde de plus prêt la requête on s'aperçoit  qu'il manque un champ obligatoire (`adresse.codePostal`).

**Que proposez-vous pour régulariser la situation ?**

```javascript
//requête
db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville", "codePostal": "26140 09" }} 
)

//réponse
{
acknowledged: true,
insertedId: ObjectId("63e3a1a54b4c44ad8d5dae03")
}
```

Donc quand on a juste a ajouter un valeur a adresse.codePostal et  on à plus d'erreur.



#### Question 2

Rajoutez à vos critères de validation existants un critère supplémentaire : le champ _id devra dorénavant être de type entier (int) ou ObjectId.

```javascript

//requête
db.runCommand(
{
	"collMod": "salles",
	"validator": {
		$jsonSchema :{
			"bsonType": "object",
			"required": ["nom","adresse.ville","adresse.codePostal"],
			"properties": {
				"_id": {
				"bsonType" : ["int", "objectId"]
				},
				"nom" : {
					"bsonType" : "string",
					"pattern" : "^[A-Z].*",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"capacite": {
					"bsonType" : "int",
				},
				"adresse.CodePostal" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"adresse.Ville" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				}
			}
		}
	}
})

//réponse
{ ok: 1 }

```


Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :
```javascript
//requête
db.salles.updateMany({}, {$set: {"verifie": true}}) 


//réponse
"MongoServerError:Document failed validation"
```

Dans un premier temps la requête casse car il nous manque les champs obligatoires. mais si on regarde notre base de données, on peut voir que ça à quand même ajouter le champ `verifie = true` dans nos documents, mais pas a n'importe quels de nos documents, qu'avec les documents ou leur schéma de validation est valide. Cela nous permet donc de trier les documents qui respecte les nouvelles règles et ce qu'il ne les respecte pas.

```javascript
db.salles.deleteMany({"verifie": true
})
```


**Supprimez les critères rajoutés à l’aide de la méthode delete en JavaScript**
```javascript
//requête
db.salles.updateMany(  
    {},  
    {  
        $unset: {"verifie": ""}  
    }  
)

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 0,
modifiedCount: 0,
upsertedCount: 0
}

```

### Question 3

Rajoutez aux critères de validation existants le critère suivant :

- Le champ smac doit être présent OU les styles musicaux doivent figurer parmi les suivants : "jazz", "soul", "funk" et "blues".
```javascript



//requête
db.runCommand(
{
	"collMod": "salles",
	"validator": {
		$jsonSchema :{
			"bsonType": "object",
			"required": ["nom","adresse.ville","adresse.codePostal"],
			"properties": {
				"_id": {
				"bsonType" : ["int", "objectId"]
				},
				"nom" : {
					"bsonType" : "string",
					"pattern" : "^[A-Z].*",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"capacite": {
					"bsonType" : "int",
				},
				"adresse.CodePostal" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"adresse.Ville" : {
					"bsonType": "string",
					"description" : "Chaîne de caractère - obligatoire"
				},
				"smac": {   
                "bsonType": [ "string", "null"],  
                "enum": [ "jazz", "soul", "funk", "blues"],  
                "description": "Doit être présent ou parmi les styles musicaux suivants : jazz, soul, funk, blues - facultatif" }
			}
		}
	}
})

//réponse
{ ok: 1 }
```


**Que se passe-t-il lorsque nous exécutons la mise à jour suivante ?**
```javascript
//requête
db.salles.update({"_id": 3}, {$set: {"verifie": false}})

//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
```

La requête va mettre à jour  le champ `verifie` égale à `false` sur le document dont son champ`_id` égal à `3`.  On fait cette requête car tout simplement le document ne respecte plus les nouvelles règles de validation.