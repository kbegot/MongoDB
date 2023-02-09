### Exercice aggregation.md


### Question 1
Écrivez le pipeline qui affichera dans un champ nommé ville le nom de celles abritant une salle de plus de 50 personnes ainsi qu’un booléen nommé grande qui sera positionné à la valeur « vrai » lorsque la salle dépasse une capacité de 1 000 personnes. Voici le squelette du code à utiliser dans le shell :
```javascript
//requêtes
var pipeline = [ {  
        $match: {  
        "capacite": {$gt: 50}  
        }},{  
        $project: {  
        "_id": 0,  
        "ville":"$nom",  
        "grande": {$gt: ["$capacite", 1000]}  
    },}]
db.salles.aggregate(pipeline)

//réponse
{
ville: 'AJMI Jazz Club',  
grande: false
}
{
ville: 'Paloma',  
grande: true
}
{
ville: 'Sonograf',  
grande: false
}
{
ville: 'Super salle',  
grande: true
}
```


### Question 2
Écrivez le pipeline qui affichera dans un champ nommé apres_extension la capacité d’une salle augmentée de 100 places, dans un champ nommé avant_extension sa capacité originelle, ainsi que son nom:
```javascript
//requête
var pipeline = [ {  
        $project: {  
        "_id": 0,  
        "avant_extension": "$capacite",  
        "apres_extension": {$add: ["$capacite", 100]}  
    },}]   
db.salles.aggregate(pipeline)
    
//réponse  
{
avant_extension: 400,  
apres_extension: 500
}
{
avant_extension: 4235,  
apres_extension: 4335
}
{  
avant_extension: 300,  
apres_extension: 400
}
{
avant_extension: 1500,  
apres_extension: 1600
}
```


### Question 3
Écrivez le pipeline qui affichera, par numéro de département, la capacité totale des salles y résidant. Pour obtenir ce numéro, il vous faudra utiliser l’opérateur $substrBytes dont la syntaxe est la suivante :
```javascript
//requête
var pipeline = [
  {
    $project: {
      "_id": 0,
      "Numero_departement": { $substrBytes: ["$adresse.codePostal", 0, 2] },
      "CapaciteTotal": "$capacite",
    },
  },
  {
    $group: {
      _id: "$Numero_departement",
      "CapaciteTotaleDepartement": { $sum: "$CapaciteTotal" }
    }
  }
]

db.salles.aggregate(pipeline)

//réponse
{  
_id: '26',  
CapaciteTotaleDepartement: 1500
}
{  
_id: '84',  
CapaciteTotaleDepartement: 700
}
{  
_id: '30',  
CapaciteTotaleDepartement: 4235
}

```


### Question 4
Écrivez le pipeline qui affichera, pour chaque style musical, le nombre de salles le programmant. Ces styles seront classés par ordre alphabétique:
```javascript
//requête
var pipeline = [    
{ $unwind: "$styles" },  
{   
    $group: {   
        _id: "$styles",   
        count: { $sum: 1 } }   
    },   
    {   
        $sort: { _id: 1 }   
    },   
    {   
        $project: {   
            style_musical: "$_id",   
            nombre_de_salles: "$count",   
            _id: 0   
    }   
}  
]  
db.salles.aggregate(pipeline)
//réponse
{
style_musical: 'blues',  
nombre_de_salles: 2
}
{  
style_musical: 'jazz',  
nombre_de_salles: 3
}
{  
style_musical: 'rock',  
nombre_de_salles: 1
}
{  
style_musical: 'soul',  
nombre_de_salles: 1
}
{  
style_musical: [    'techno',    'reggae'  ],  
nombre_de_salles: 1
}

```



### Question 5
À l’aide des buckets, comptez les salles en fonction de leur capacité :
- celles de 100 à 500 places
```javascript
//requête
var pipeline = [ {   
        $bucket: {   
            groupBy: "$capacite",   
            boundaries: [100, 500],  
            default: "Autres salles avec une capacite inférieur ou supérieur",   
            output: {   
                count: { $sum: 1 }   
            }   
        }   
    }  ]
    
db.salles.aggregate(pipeline)

//réponse
{  
_id: 100,  
count: 2
}
{  
_id: 'Autres salles avec une capacite inférieur ou supérieur',  
count: 2
}

```

- celles de 500 à 5000 places
```javascript
//requête
var pipeline = [ {   
        $bucket: {   
            groupBy: "$capacite",   
            boundaries: [500, 5000],  
            default: "Autres salles avec une capacite inférieur ou supérieur",   
            output: {   
                count: { $sum: 1 }   
            }   
        }   
    }  ]
    
db.salles.aggregate(pipeline)

//réponse
{ 
_id: 500,  
count: 2
}
{  
_id: 'Autres salles avec une capacite inférieur ou supérieur',  
count: 2
}

```


### Question 6
Écrivez le pipeline qui affichera le nom des salles ainsi qu’un tableau nommé avis_excellents qui contiendra uniquement les avis dont la note est de 10:
```javascript
//requête
var pipeline = [  
  {  
    $unwind: "$avis"  
  },  
  {  
    $group: {  
      _id: "$nom",  
      avis_excellents: {   
        $push: {  
          $cond: [  
            { $eq: ["$avis.note", 10] },  
            "$avis",  
            null  
          ]  
        }  
      }  
    }  
  },  
  {  
    $project: {  
      _id: 0,  
      nom: "$_id",  
      avis_excellents: {  
        $filter: {  
          input: "$avis_excellents",  
          as: "avis",  
          cond: { $ne: ["$$avis", null] }  
        }  
      }  
    }  
  }  
]
db.salles.aggregate(pipeline)
//réponse
{
nom: 'AJMI Jazz Club',
avis_excellents: [
	{
	date: 2023-02-08T16:18:17.956Z,
	note: 10
	}
]}
{
nom: 'Sonograf',
avis_excellents: []
}
{
nom: 'Paloma',
avis_excellents: [
	{
	date: 2019-07-06T00:00:00.000Z,
	note: 10
	}
}

```