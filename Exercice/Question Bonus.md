

### Créer une collection et ensuite la renommer:

```javascript
//création de la collection
db.createCollection("salle")

//renommer la collection
db.maCollection.renameCollection("mesSalles")
```


###  Index c'est plus rapide

Test de la rapiditer avec et sans l'index sur un gros jeux de données de météos
```javascript
//requête sans index
db.weatherbrazil.find({
  "temperature_max.AUT_(°C)": {$gt: 25},
  "Date": {
    $gte: new Date("2000-06-01"),
    $lte: new Date("2000-08-31")
  }}, {"Date": 1,"temperature_max.AUT_(°C)": 1}).explain("executionStats")
  
//réponse du explain("executionStats"):
executionTimeMillis: 61


//création index
db.weatherbrazil.createIndex({"date": 1},{
"name":"idx_date"
})


//requête avec index
db.weatherbrazil.find({
  "temperature_max.AUT_(°C)": {$gt: 25},
  "Date": {
    $gte: new Date("2000-06-01"),
    $lte: new Date("2000-08-31")
  }}, {"Date": 1,"temperature_max.AUT_(°C)": 1}).explain("executionStats")
  
//réponse du explain("executionStats"):
executionTimeMillis: 38
```
 A savoir que le `explain("executionStats")` nous permet davoir les stats d'execution de la requête. Comme on a pu le voir l'index réduit le temps d'éxecution casimpratiquement par 2.

Donc voilà la preuve que l'index nous permet d'aller plus vite dans nos requête.