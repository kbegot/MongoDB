### Préparation des données:
#### A)
**Importez les données de stations météorologiques dans MongoDB en utilisant la commande mongoimport:**

Pour commencer je suis aller chercher mon dataset sur Kagle. Ma base de données est sur le climat et les conditions météorologiques du Brésil.
[Source](https://www.kaggle.com/datasets/PROPPG-PPG/hourly-weather-surface-brazil-southeast-region) 

À savoir que dans mon repo git se trouve le jeu de données que j'ai utilisées avec des modifications. En effet  pour pouvoir manipuler parfaitement les données j'ai dû modifier quelque petite chose du dataset de base.


#### B)
**Assurez-vous que les données sont bien structurées et propres pour une utilisation ultérieure:**

J'ai donc commencé par importer mon fichier `weatherbrazil.csv` et j'ai du specifier chaque type des fields.
```javascript
"index": string
"Date": Date
"Heure": String
"precipitation_total_(mm)": Double
"pression_atmospherique_niveau_saison_(mB)": Double
"pression_atmospherique_max. donnee_fournie.AUT_(mB)": Double
"pression_atmospherique_min.donnee_fournie. AUT_(mB)": Double
"rayonnement_global(Kj/m²)": Int
"temperature_air_bulbe_sec_(°C)": Double
"temperature_pointe_rosee_(°C)": Double
"temperature_max.AUT_(°C)": Double
"temperature_min.AUT_(°C)": Double
"temperature_rosee_max.donnee_fournie.AUT_(°C)": Double
"temperature_rosee_min.donnee_fournie.AUT_(°C)": Double
"humidite_reel.MAX.donnee_fournie.AUT_(%)": int
"humidite_reel.MIN.donnee_fournie.AUT_(%)": int
"humidite_relative_air_horaire_(%)": int
"vent_direction_horaire_(gr)_(°(gr))": int
"vent_coupe_max_(m/s)": Double
"vent_vitesse_horaire_(m/s)": Double
"region": String
"state": String
"station": String
"station_code": String
"latitude": Double
"longitude": Double
"height": Double
```

Ensuite j'ai dû modifier certaines lignes dans ma base de données car il y avait des imperfections.
Donc j'ai update tous les fields qui avait comme valeur -9999 car il correspondait à une valeur nul mais il allait aussi fossé toutes mes requêtes par la suite.
```javascript 
//exemple d'un update
db.weatherbrazil.updateMany(
   { "pression_atmosherique_niveau_saison_(mB)": -9999 },
   { $set: { "pression_atmosherique_niveau_saison_(mB)": null } }
)
```



### Indexation avec MongoDB:
#### A)
**Créez un index sur le champ de la date pour améliorer les performances de la recherche. Utilisez la méthode createIndex ():**
```javascript
//requête
db.weatherbrazil.createIndex({"date": 1},{
"name":"idx_date"
})

//réponse
'idx_date'
```

L'index que j'ai créé va nous permettre d'optimiser un maximum des futures requêtes contenant le field `Date` et peut nous faire gagner pas mal de temps sur certaines requêtes


#### B)
**Vérifiez que l'index a été créé en utilisant la méthode listIndexes ().**
```javascript
//requête
db.weatherbrazil.getIndexes()

//réponse
[
	{ v: 2, key: { _id: 1 }, name: '_id_'},
	{ v: 2, key: { date: 1 }, name: 'idx_date'}
]
```

On s'aperçoit donc que notre index a bien éte crée sur le field `Date`



### Requêtes MongoDB: 

#### A) 
**Recherchez les stations météorologiques qui ont enregistré une température supérieure à 25°C pendant les mois d'été (juin à août). Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères:**

Avec la température MAX des stations météorologique:
```javascript
//requête
db.weatherbrazil.find({
  "temperature_max.AUT_(°C)": {$gt: 25},
  "Date": {
    $gte: new Date("2000-06-01"),
    $lte: new Date("2000-08-31")
  }}, {"Date": 1,"temperature_max.AUT_(°C)": 1})

db.weatherbrazil.countDocuments({ "Date": { $gte: new Date("2000-06-01"), $lte: new Date("2000-08-31") }, "temperature_max.AUT_(°C)": { $gt: 25 } })

//réponse
{  
_id: ObjectId("63e4d70d71704811001f3c29"),  
Date: 2000-06-09T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 25.6  }
}
{  
_id: ObjectId("63e4d70d71704811001f3c2f"),  
Date: 2000-06-09T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 26.2  }
}
{  
_id: ObjectId("63e4edf271704811001f4237"),  
Date: 2000-07-04T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 25.4  }
}
{  
_id: ObjectId("63e4edf271704811001f4238"),  
Date: 2000-07-04T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 25.1  }
}
_id: ObjectId("63e4edf271704811001f4555"),  
Date: 2000-08-06T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 25.9  }
}
{  
_id: ObjectId("63e4edf271704811001f4556"),  
Date: 2000-08-06T00:00:00.000Z,  
temperature_max: {    'AUT_(°C)': 27.7  }
}

//nombre de document avec la température supérieur à 25°C
4496
```

Ici nous listons bien les températures de cet été au-dessus de 25°C avec la méthode `.find()`


#### B) 
**Triez les stations météorologiques par pression atmosphérique, du plus élevé au plus bas. Utilisez la méthode sort () pour trier les résultats:**

```javascript
//requête
db.weatherbrazil.find(
{  
	"station": { $exists: 1 },  
	"pression_atmospherique_niveau_saison_(mB)": { $exists: 1, $ne: null}   
},{"station": 1,"pression_atmospherique_niveau_saison_(mB)": 1}).sort({"pression_atmospherique_niveau_saison_(mB)": -1})

//réponse
{  
_id: ObjectId("63e4edf371704811001f6340"), 'pression_atmospherique_niveau_saison_(mB)': 1012.5,  
station: 'MANAUS'
}
{  
_id: ObjectId("63e4edf371704811001f6357"),  'pression_atmospherique_niveau_saison_(mB)': 1012.5,  
station: 'MANAUS'
}
{  
_id: ObjectId("63e4f72b71704811001fa168"),  'pression_atmospherique_niveau_saison_(mB)': 1012.5,  
station: 'MANAUS'
}
{  
_id: ObjectId("63e4f72b71704811001fa17f"),  'pression_atmospherique_niveau_saison_(mB)': 1012.5,  
station: 'MANAUS'
}
```

Là je trie par ordre décroissant et document (donc plusieurs fois les mêmes stations car ce sont des relevés par jour et par heure pour certaines stations) les pressions atmosphériques.

**ou**
```javascript
//requête
db.weatherbrazil.aggregate([
   { $group: { _id: "$station", averagePressure: { $avg: "$pression_atmospherique_niveau_saison_(mB)" } } },
   { $sort: { averagePressure: -1 } }
])


//réponse
{  _id: 'MAUES',  averagePressure: 1006.9627118644067}
{  _id: 'MANAUS',  averagePressure: 1003.9946730317496}
{  _id: 'PARANA',  averagePressure: 977.4016949152542}
{  _id: 'ARIQUEMES',  averagePressure: 994.4744807121663}

```

ici la requête trie les pressions atmosphériques par station avec une moyenne de toutes les   pressions atmosphériques des stations.



### Framework d'agrégation: 

#### A) 
**Calculez la température moyenne par station météorologique pour chaque mois de l'année. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et grouper les données par mois:**
```javascript
//requête
db.weatherbrazil.aggregate([
   {
      $project: {
         month: { $month: { $convert: { input: "$Date", to: "date" } } },
         station: "$station",
         temperature_air_bulbe_sec: "$temperature_air_bulbe_sec_(°C)"
      }
   },
   {
      $group: {
         _id: {
            month: "$month",
            station: "$station"
         },
         avgTemperature: { $avg: "$temperature_air_bulbe_sec" }
      }
   }
])

//réponse
{  
	_id: {    
		month: 10,    
		station: 'ARIQUEMES'  
	},  
	avgTemperature: 26.808308605341246
}
{  
	_id: {    
	month: 1,    
	station: 'MANAUS'  },  
	avgTemperature: 26.68658536585366
}
{  
	_id: {    
	month: 5,    
	station: 'MANAUS'  },  
	avgTemperature: 26.386124401913875
}
{  
	_id: {    
	month: 12,    
	station: 'MANAUS'  },  
	avgTemperature: 27.687420584498096
}
```

Ici la requête va calculer la température moyenne de chaque station pour chaque mois de l'année. À savoir que le jeu de données n'a pas de relevé de température pour chaque station tous les mois


### B)
**Trouvez la station météorologique qui a enregistré la plus haute température en été. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et trouver la valeur maximale:**
```javascript
//requête
db.weatherbrazil.aggregate([
  {
    "$match": {
      "Date": {
        "$gte": ISODate("2000-06-01"),
        "$lte": ISODate("2000-08-31")
      }
    }
  },
  {
    "$project": {
      "station": 1,
      "temperature_max": 1,
      "Date": 1
    }
  },
  {
    "$group": {
      "_id": "$station",
      "max_temperature": { "$max": "$temperature_max" }
    }
  },
  {
    "$sort": { "max_temperature": -1 }
  },
  {
    "$limit": 1
  }
])

//réponse
{
	_id: 'MANAUS',
	max_temperature: {
	'AUT_(°C)': 34.1
	}
}
```

On a belle est bien trouver la station météorologique qui  à enregistrer la température la plus haute cet été avec `temperature_max` qui nous a directement permis de chercher dans les températures les plus hautes

### Export de la base de données: 

#### A)
**Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB:**

```javascript
//exemple de mongoExport
mongoexport --collection=weatherbrazil --db=tp --out=weatherbrazil.json
```

Lors de l'export de la base de données donnée `weatherbrazil.csv` j'ai bien sure exporter toute la collection ainsi que tous les fields qui s'y trouver sous le format `.csv`

La collection est disponible dans mon repo Git.

