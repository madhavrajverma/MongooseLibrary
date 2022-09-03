# Queries

- Queries and aggregations are how you fetch data from MongoDB into Mongoose.
- You can also use queries to update data in MongoDB without needing to fetch the documents.

## Query Execution

- Mongoose queries are just objects in Node.js memory until you actually execute them. There are 3 ways to execute a query:


- await on the query
-  Call Query #then
-  Call Query #exec

```js

const query = Model.findOne();
// Execute the query 3 times, in 3 different ways
await query; // 1

query.then(res => {}); // 2

await query.exec(); // 3

```

```js

// Chaining makes it easier to visually break up complex updates
await Model.
  find({ name: 'Will Riker' }).
  updateOne({}, { rank: 'Commander' });
// Equivalent, without using chaining syntax
await Model.updateOne({ name: 'Will Riker' }, { rank: 'Commander' });

```

## Query Operations

- There are 17 distinct query operations in Mongoose. 4 are deprecated: count(), update(), findOneAndRemove(), and remove(). The remaining 13 can be broken up into 5 classes: reads,
updates, deletes, and and modify ops, and other.


### Read
- **find()**, **findOne()**,**CountDoucments()**, and all take in a filter parameter and and document(s) that match the   .

```js
// find 
    const filter = { age: { $lt: 30 } };
      let  res  = await Model.find({filter});
     console.log(res[0].name);
     console.log(res[1].name);

     // find One
     const res = await Model.findOne();
     console.log(res.name);

     // countDocuments
     const count = await Model.countDocuments(filter);
     console.log(count);

```

### Delete

- **deleteOne()**, **deleteMany()** , and **remove()** all take a filter parameter and delete document(s) from the collection that match the given flter.

```js
// delteOne
    const res = await Model.deleteOne(filter)
    console.log(res);

    //deleteMany 
    const res = await Model.deleteMany(filter);
    console.log(res);

// `remove()` deletes all docs that match `filter` by default
res = await Model.remove(filter);
res.deletedCount; // 2
```

### Update

```js
const schema = Schema({ name: String, age: Number, rank: String });

const Model = mongoose.model('Model', schema);

await Model.insertMany([
  { name: 'Jean-Luc Picard', age: 59 },
  { name: 'Will Riker', age: 29 },
  { name: 'Deanna Troi', age: 29 }
]);

const filter = { age: { $lt: 30 } };

const update = { rank: 'Commander' };

// `updateOne()`
let res = await Model.updateOne(filter, update);

res.nModified; // 1

let docs = await Model.find(filter);

docs[0].rank; // 'Commander'
docs[1].rank; // undefined

// `updateMany()`
res = await Model.updateMany(filter, update);
res.nModified; // 2

docs = await Model.find(filter);
docs[0].rank; // 'Commander'
docs[1].rank; // 'Commander'

// `update()` behaves like `updateOne()` by default

res = await Model.update(filter, update);
res.nModified; // 1
```

### findAndModify

```js
const filter = { name: 'Will Riker' };
const update = { rank: 'Commander' };

// MongoDB will return the matched doc as it was **before**
// applying `update`

let doc = await Model.findOneAndUpdate(filter, update);
doc.name; // 'Will Riker'
doc.rank; // undefined

const replacement = { name: 'Will Riker', rank: 'Commander' };
doc = await Model.findOneAndReplace(filter, replacement);

// `doc` still has an `age` key, because `findOneAndReplace()`
// returns the document as it was before it was replaced.

doc.rank; // 'Commander'
doc.age; // 29

// Delete the doc and return the doc as it was before the
// MongoDB server deleted it.

doc = await Model.findOneAndDelete(filter);
doc.name; // 'Will Riker'
doc.rank; // 'Commander'
doc.age; // undefined

//To return the document as it is after the MongoDB server applied the update, use the new option.
const filter = { name: 'Will Riker' };
const update = { rank: 'Commander' };
const options = { new: true };

// MongoDB will return the matched doc **after** the update
// was applied if you set the `new` option

let doc = await Model.findOneAndUpdate(filter, update, options);
doc.name; // 'Will Riker'
doc.rank; // 'Commander'

const replacement = { name: 'Will Riker', rank: 'Commander' };
doc = await Model.findOneAndReplace(filter, replacement, options);
doc.rank; // 'Commander'
doc.age; // void 0

```

- To return the document as it is after the MongoDB server applied the update, use the new option.

```js
onst filter = { name: 'Will Riker' };
const update = { rank: 'Commander' };
const options = { new: true };

// MongoDB will return the matched doc **after** the update
// was applied if you set the `new` option

let doc = await Model.findOneAndUpdate(filter, update, options);
doc.name; // 'Will Riker'
doc.rank; // 'Commander'

const replacement = { name: 'Will Riker', rank: 'Commander' };
doc = await Model.findOneAndReplace(filter, replacement, options);
doc.rank; // 'Commander'
doc.age; // void 0
```


# Other

- The distinct() and estimatedDocumentCount() operations don't quite fit in any of the other classes. Their semantics are sufficiently different that they belong in their own class.

```js
// Return an array containing the distinct values of the `age`
// property. The values in the array can be in any order.

let values = await Model.distinct('age');
values.sort(); // [29, 59]

const filter = { age: { $lte: 29 } };
values = await Model.distinct('name', filter);
values.sort(); // ['Deanna Troi', 'Will Riker']
```

- **estimatedDocuments()** returns the number of documents in the collection based on MongoDB's collection metadata. It doesn't take any parameters.

```js
// Unlike `countDocuments()`, `estimatedDocumentCount()` does **not**
// take a `filter` parameter. It only returns the number of documents
// in the collection.
const count = await Model.estimatedDocumentCount();
count; // 3
```



## Query Operation


```js
// multiple Query opertaion treat as a and 
const querySelectors = { $gt: 50, $lt: 60 };
const filter = { age: querySelectors };
```


- $eq: Matches values that strictly equal the specified value
- $gt: Matches values that are greater than the specified value
- $gte: Matches values that are greather than or equal to the specified value
- $in: Matches values that are strictly equal to a value in the specified array
- $lt: Matches values that are less than a given value
- $lte: Matches values that are less than or equal to the specified value
- $ne: Matches values that are not strictly equal to the specified value
- $nin: Matches values that are not strictly equal to any of the - specified values
- $regex: Matches values that are strings which match the specified regular expression


```js

// Matches if the `age` property is exactly equal to 42
let filter = { age: { $eq: 42 } };

// Matches if the `age` property is a number between 30 and 40
filter = { age: { $gte: 30, $lt: 40 } };

// Matches if `name` is 'Jean-Luc Picard' or 'Will Riker'
filter = { name: { $in: ['Jean-Luc Picard', 'Will Riker'] } };

// Matches if `name` is a string containing 'picard', ignoring case
filter = { name: { $regex: /picard/i } };

```


- $exists: Matches either documents that have a certain property, or don't have the specified property, based on whether the given value is true or false

```js

const schema = Schema({ name: String, age: Number, rank: String });
const Model = mongoose.model('Model', schema);

await Model.create({ name: 'Will Riker', age: 29 });

// Finds the doc, because there's an `age` property
let doc = await Model.findOne({ age: { $exists: true } });

// Does **not** find the doc, no `rank` property
doc = await Model.findOne({ rank: { $exists: true } });

// Finds the doc, because there's no `rank` property
doc = await Model.findOne({ rank: { $exists: false } });

// Finds the doc, because `$exists: true` matches `null` values.
// `$exists` is analagous to a JavaScript `in` or `hasOwnProperty()`
await Model.updateOne({}, { rank: null });
doc = await Model.findOne({ rank: { $exists: true } });

```


### Geospatial

MongoDB has 4 geospatial query selectors that help you query geoJSON data:

 - $geoIntersects
 - $geoWithin
 - $near
 - $nearSphere

 ### geoIntersects

 ```js
// Approximate coordinates of the capital of Colorado
const denver = { type: 'Point', coordinates: [-104.9903, 39.7392] };

// Approximate coordinates of San Francisco
const sf = { type: 'Point', coordinates: [-122.5, 37.7] };

let doc = await State.findOne({
  location: {
    // You need to specify `$geometry` if you're using
    // `$geoIntersects` with geoJSON.
    $geoIntersects: { $geometry: denver }
} });
doc.name; // 'Colorado'
doc = await State.findOne({
  location: {
    $geoIntersects: { $geometry: sf }
  }
});
doc; // null
 ```

 ### $geoWithin With $centerSphere

```js
const sfCoordinates = [-122.5, 37.7];
// $centerSphere distance is in radians, so convert miles to radians
const distance = 1000 / 3963.2;
const $geoWithin = { $centerSphere: [sfCoordinates, distance] };

let doc = await City.findOne({ location: { $geoWithin } });
// `doc` will be `null`
$geoWithin.$centerSphere[1] = distance / 2;
doc = await City.findOne({ location: { $geoWithin } });
```


### Array
- $all: matches arrays that contain all of the values in the given array
- :$size matches arrays whose length is equal to the given number
- :$elematch matches arrays which contain a document that matches the given filter

```js

await BlogPost.create([
  { comments: [{ user: 'jpicard', text: 'Make it so!' }] },
  { comments: [{ user: 'wriker', text: 'One, or both?' }] },
  { comments: [{ user: 'wriker' }, { user: 'jpicard' }] }
]);
// Find all blog posts that both 'wriker' and 'jpicard' commented on.
const $all = ['wriker', 'jpicard'];
let docs = await BlogPost.find({ 'comments.user': { $all } });
docs.length; // 1
// Find all blog posts that 'jpicard' commented on.
docs = await BlogPost.find({ 'comments.user': 'jpicard' });
docs.length; // 2

```