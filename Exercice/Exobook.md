### Exercice exobook.md

Créez une base de données sample nommée "sample_db" et une collection appelée "employees". Insérez les documents suivants dans la collection "employees":

```javascript
//requête
db.employees.insertMany([
{ "name": "John Doe", "age": 35, "job": "Manager", "salary": 80000 },
{ "name": "Jane Doe", "age": 32, "job": "Developer", "salary": 75000 }, 
{ "name": "Jim Smith", "age": 40, "job": "Manager", "salary": 85000 }
])

//réponse
{
acknowledged: true,
insertedIds: {
'0': ObjectId("63e10f6dfec1c8ae8eb062d9"),
'1': ObjectId("63e10f6dfec1c8ae8eb062da"),
'2': ObjectId("63e10f6dfec1c8ae8eb062db")
}}
```


Écrivez une requête MongoDB pour trouver tous les documents dans la collection "employees":

```javascript
//requête
db.employees.find().pretty()

//réponse
{
_id: ObjectId("63e10f6dfec1c8ae8eb062d9"),
name: 'John Doe',
age: 35,
job: 'Manager',
salary: 80000
}
{
_id: ObjectId("63e10f6dfec1c8ae8eb062da"),
name: 'Jane Doe',
age: 32,
job: 'Developer',
salary: 75000
{
_id: ObjectId("63e10f6dfec1c8ae8eb062db"),
name: 'Jim Smith',
age: 40,
job: 'Manager',
salary: 85000
}


```


Écrivez une requête pour trouver tous les documents où l'âge est supérieur à 33:
```javascript
//requête
db.employees.find({"age": {$gt:33 }})

//réponse
{
_id: ObjectId("63e10f6dfec1c8ae8eb062d9"),
name: 'John Doe',
age: 35,
job: 'Manager',
salary: 80000
},
{
_id: ObjectId("63e10f6dfec1c8ae8eb062db"),
name: 'Jim Smith',
age: 40,
job: 'Manager',
salary: 85000
}

```


Écrivez une requête pour trier les documents dans la collection "employees" par salaire décroissant:
```javascript
//requête
db.employees.find().sort({"salary": -1})

//réponse
{
_id: ObjectId("63e10f6dfec1c8ae8eb062db"),
name: 'Jim Smith',
age: 40,
job: 'Manager',
salary: 85000
},
{
_id: ObjectId("63e10f6dfec1c8ae8eb062d9"),
name: 'John Doe',
age: 35,
job: 'Manager',
salary: 80000
},
{
_id: ObjectId("63e10f6dfec1c8ae8eb062da"),
name: 'Jane Doe',
age: 32,
job: 'Developer',
salary: 75000
}
```


Écrivez une requête pour sélectionner uniquement le nom et le job de chaque document:
```javascript
//requête
db.employees.find({"_id": {$exists: true}},{"_id": 0, "name": 1, "job": 1 })

//réponse
{
name: 'John Doe',
job: 'Manager'
},
{
name: 'Jane Doe',
job: 'Developer'
},
{
name: 'Jim Smith',
job: 'Manager'
}
```


Écrivez une requête pour compter le nombre d'employés par poste:
```javascript
//requête
db.employees.aggregate([{$group: {_id: "$job", NombreEmployer: {$count: {} }}} ])

//réponse
{
_id: 'Manager',
NombreEmployer: 2
}
{
_id: 'Developer',
NombreEmployer: 1
}
```

Écrivez une requête pour mettre à jour le salaire de tous les développeurs à 80000:
```javascript
//requête
db.employees.update(
{"job": "Developer"},
{
	$set : {"salary": 80000}
},
{"multi": true })

ou

db.employees.updateMany(
{"job": "Developer"},
{
	$set : {"salary": 80000}
})


//réponse
{
acknowledged: true,
insertedId: null,
matchedCount: 1,
modifiedCount: 1,
upsertedCount: 0
}
