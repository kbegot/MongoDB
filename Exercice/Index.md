### Exercice index.md


### Question 1
Un bref examen de vos fichiers journaux a révélé que la plupart des requêtes effectuées sur la collection salles cible des capacités ainsi que des départements, comme ceci :
```javascript
db.salles.find({"capacite": {$gt: 500}, "adresse.codePostal": /^30/}) 
db.salles.find({"adresse.codePostal": /^30/, "capacite": {$lte: 400}})
```

**Que proposez-vous comme index qui puisse couvrir ces requêtes ?**
```javascript
//requête
db.salles.createIndex({"capacite": 1,"adresse.codePostal": 1},{
"name":"index_capcite_codepostal"
})

//réponse
'index_capcite_codepostal'

//voici l'index crée
{
v: 2,
key: { capacite: 1, 'adresse.codePostal': 1 },
name: 'index_capcite_codepostal'
}
```

Détruisez ensuite l’index créé:
```javascript
//requête
db.salles.dropIndex("index_capcite_codepostal")

//réponse
{ nIndexesWas: 2, ok: 1 }
```