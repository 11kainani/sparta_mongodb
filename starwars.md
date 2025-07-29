**Convert fields from string to int**

``` 
db.characters.updateMany(
  { height: { $type: "string" } },
  [
    {
      $set: {
        height: { $convert: 
        {
          input:"$height",
          to:"int",
          onError:""
        }
          }
      }
    }
  ]
)

CORRECTION 

db.characters.updateMany(
  {height: "unknown"},
  {$unset: {height: ""}}
)
db.characters.updateMany(
  {},
  [{$set: {height: {$toInt: "$height"}}}]
)
 
```

**Greater Than**
```
db.characters.find({
  height: {$gt : 100}
}, {
 _id:0, 
  name:1
})
 
```

**Equals**
```
db.characters.find({
  height: {$eq: 96}
})
```
**Greater than equals**
```
db.characters.find({
  height: {$gte: 96}
}, {
  _id:0, 
  name:1
   })
```
**In**
```
db.characters.find({
  eye_color: { $in: ["blue", "green"] }
})
```
**Not In**
```
db.characters.find({
  gender: { $nin: ["male", "unknown"] }
})

```
**Less than**
```
db.characters.find({
  gender: { $nin: ["male", "unknown"] }
})
```
**Less than or equal**
```
db.characters.find({
  height: { $lte: 150 }
})
```
**Not Equals**
```
db.characters.find({
  species: { $ne: "Human" }
})

```
**Exercise**
Convert the "mass" field to double. This will soon prove difficult because one value has a thousands comma separator. Replacing this in MongoDB would be extremely complicated for an apparently trivial task, so better to fix these issues earlier in the ETL pipeline. We'll fix it using a hard-coded update, even though this isn't best practice.

```
Change mass to double
 
db.characters.update(

  {mass: "1,358"},

  {$set: {mass: "1358"}}

)
 
db.characters.update(

  {mass: "unknown"},

  {$unset: {mass: ""}},

  {multi: true}

)
 
db.characters.update(

  {mass: {$exists: true}},

  [{$set: {mass: {$toDouble: "$mass"}}}],

  {multi: true}

)
 
 
```

Find the average mass and count per species. Filter out null values and sort by average mass ascending.

```
db.characters.aggregate([
  {
    $group: {
      _id: "$species.name",
      avg: {$avg: "$mass"},
      count: {$sum: 1}
    }
  }, { $match: {avg: {$ne: null}}},
  {$sort: {avg: 1}}
]).toArray()


```
```
Add starship:
db.starships.insertOne({name: "TIE Advanced x1",model: "Twin Ion Engine Advanced x1",manufacturer: "Sienar Fleet Systems",length: 9.2,max_atmosphering_speed: 1200,crew: 1,passengers: 0,pilot: ObjectId("6888a7acb50944b8fa848b91")})
 
 
Ad multiple pilots 

db.starships.insertOne({name: "Millenium Falcon",model: "YT-1300 Light Freighter",manufacturer: "Corellian Engineering Corporation",length: 34.37,max_atmosphering_speed: 1050,crew: 4,passengers: 6,pilot: [ObjectId("6888a7acb50944b8fa848b90"),ObjectId("6888a7acb50944b8fa848b86"),ObjectId("6888a7acb50944b8fa848b74"),ObjectId("6888a7acb50944b8fa848b4f")]})
```