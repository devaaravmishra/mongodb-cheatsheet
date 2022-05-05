# mongodb-utils

A mongodb documentation contains all the information we need to get started with mongodb.

## Acknowledgements

-   [mongodb](https://www.mongodb.com/docs/manual/)
-   [mongosh](https://www.mongodb.com/docs/mongodb-shell/)

## Documentation

MongoDB is a NoSQL document based database. All the data in MongoDB is stored in JSON documents that are sorted into a variety of collections based on the type of data.

### Terminology

#### Database

A database is simply a container for collections. A database in MongoDB is the same as a database in SQL and usually a app/website will have one database full of different collections.

#### Collection

A collection is a grouping of documents inside a database. This is same as tables in a SQL database. generally we have one collection per data model. For example, wer app may have users, posts, and products collections.

#### Document

A document is just a record inside of a collection. This is same as a row in a SQL table. A document represents one single object within a collection. In MongoDB a document is just a JSON object.

#### Field

A field is just a key value pair within a document. This is same as a column in SQL. Each document contains a number of fields which contain information such as name, address, age, etc. An important difference between SQL and MongoDB is that a field can contain values such as JSON objects and arrays instead of just strings, numbers, booleans, etc. Also, MongoDB documents can have different fields defined within the same collection. In SQL all rows in a table must have the same columns, but in MongoDB one document in the users collection could have the fields name, and age, while another user document could have the fields name, address, and hobbies.

---

### Basic Commands

`mongosh` - is a command line utility that allows we to connect to a mongodb server and execute commands.

`showdbs` - Lists all the databases in the server.

`use <dbname>` - Connects to the database with the given name.

`db` - Displays the current database.

`cls` - Clears the console.

`show collections` - Lists all the collections in the current database.

`db.dropDatabase()` - Drops the current database.

`exit` - Exits the mongosh shell.

---

### CRUD Methods

The CRUD stands for Create, Read, Update, and Delete. All of these commands will run on a specific collection within a specific database.

#### Create (Insert)

Creating documents in MongoDB is pretty simple & fast then SQL since there are only two methods to do so and they both are very similar.

`insertOne` - Inserts a single document into a collection.

```js
// Insert a user with the name John
db.users.insertOne({ name: "John" });
```

`insertMany` - Similiar to insertOne, but it takes an array of objects to be inserted.

```js
// Insert a user with array of hobbies and a second user with the name Peter
db.users.insertMany([
	{ hobbies: ["Swimming", "Dancing", "Reading"] },
	{ name: "Peter" },
]);
```

#### Read (Find)

It is the most complex method in MongoDB. It is used to find documents in a collection.

`find` - Finds all documents in a collection.

```js
// Get all users
db.users.find();
```

`find(<filterObject>)` - Finds all documents in a collection that match the given filter. The filter object is a JSON object that contains the fields and values to match. By default the filter object will do equality comparisons and if we pass multiple fields in the same filter object it will only return documents that match all the fields in the filter.

```js
// Get all users with the name Peter
db.users.find({ name: "Peter" });

// Get all users whose address field has a zip field with the value 342001
db.users.find({ "address.zip": "342001" });
```

`find(<filterObject>, <selectObject>)` - find method also takes a second object to select which fields we want returned. The object contains a key which is the field and a value of either 0 or 1 to determine if that field is returned or not. By default the \_id property is always returned unless specified.

```js
// Get all users with the name John and return the name, age, and _id fields
db.users.find({ name: "Peter" }, { name: 1, age: 1 });

// Get all users and return all fields except the age field
db.users.find({}, { age: 0 });
```

`findOne` - Finds a single document in a collection.

```js
// Get the first user with the name Peter
db.users.findOne({ name: "Peter" });
```

`countDocuments` - Counts the number of documents in a collection.

```js
// Get the number of users with name Peter in the users collection
db.users.countDocuments({ name: "Peter" });
```

#### Update

Updating documents in MongoDB is a bit more complex than in SQL since there are actually many different ways we can update a document.

`updateOne` - Updates a single document in a collection. This will update the first document that matches the filter passed to it and will then update the document based on the information passed in the second parameter to updateOne() function.

```js
// Update the age of the first user from 07 to 09
db.users.updateOne({ age: 07 }, { $set: { age: 09 } });
```

`updateMany` - updateMany() is similar to updateOne() but it updates all documents that match the filter passed to it.

```js
// Update the age of all users with the age of 36 by adding 5 to it
db.users.updateMany({ age: 36 }, { $incr: { age: 5 } });
```

`replaceOne` - This works similarly to updateOne, but it will instead replace the entire document and not just update specific fields. Generally this is not something we want to do as it will delete all fields in the object (except the id field) that are not specified within the object passed to replaceOne().

```js
// Replace the first user with an address of zip 342001 with an object that only has a name field
db.users.replaceOne({ address.zip:342001 }, { name: "Emiley" });
```

#### Delete

It is the opposite of update. It will delete all documents that match the filter passed to it.

`deleteOne` - This method will delete the first object that matches the filter object passed to it.

```js
// Delete the first user with the age 36
db.users.deleteOne({ age: 36 });
```

`deleteMany` - This method will delete all objects that match the filter object passed to it.

```js
// Delete all users with the age of 26
db.users.deleteMany({ age: 26 });
```

---

## Adavanced Concepts

### Complex Filter Objects

Complex Filter Objects are used to filter documents based on multiple fields.

Also we can pass an object as the value of a field instead of a value and that object can specify a bunch of information about how to filter that field. we can also combine together and nest filters.

`$eq` - Matches values that are equal to a specified value. exactly the same as how our simple filters do.

```js
// This is same as db.users.find({ name: "John" })
db.users.find({ name: { $eq: "John" } });
```

`$ne` - The $neq filter is the exact opposite of $eq and checks for inequality.

```js
// Get all users with a name other than John
db.users.find({ name: { $neq: "John" } });
```

`gt` / `gte` - These filters check for when a value is greater than or greater than or equal to another value.

```js
// Get all users with an age greater than 13
db.users.find({ age: { $gt: 13 } });

// Get all users with an age greater than or equal to 19
db.users.find({ age: { $gte: 19 } });
```

`lt` / `lte` - These filters check for when a value is less than or less than or equal to another value.

```js
// Get all users with an age less than 19
db.users.find({ age: { $lt: 19 } });

// Get all users with an age less than or equal to 25
db.users.find({ age: { $lte: 25 } });
```

`$in` - This will return all documents that match one of the values in the array.

```js
// Get all users with the name Peter or John
db.users.find({ name: { $in: ["Peter", "John"] } });
```

`$nin` - This is the opposite of $in and returns all documents with values not in the array.

```js
// Get all users with a name other than Peter or John
db.users.find({ name: { $nin: ["Peter", "John"] } });
```

`$and` - This checks that all conditions in the array are true. It is not of much use, since passing multiple key value pairs to the same filter object by default does an and operation.

```js
// Get all users with the name Kyle and the age 36
db.users.find({ $and: [{ name: "John" }, { age: 36 }] });
// This is the same as db.users.find({ age: 36, name: "John" })
```

`$or` - This is the same as $and, but it does an or check instead of an and check.

```js
// Get all users with the name John or the age 36
db.users.find({ $or: [{ name: "John" }, { age: 36 }] });
```

`$not` - This checks for when all conditions in the array are false.

```js
// Get all users with a name other than John
db.users.find({ $name: { $not: { $eq: "John" } } });
```

`$nor` - Matches any value that matches none of the specified filters.

```js
// Get all users with neither name John nor age 36
db.users.find({ $nor: [{ name: "John" }, { age: 36 }] });
```

`$exists` - This will filter documents based on if a field exists on the document or not. This will only check the field, and not the value. That means if a document has a field with a value of null it will still show up in the $exists query.

```js
// Get all users with a name field defined
db.users.find({ $name: { $exists: true } });

// Get all users without a name field
db.users.find({ $name: { $exists: false } });
```

`$regex` - Matches any value that matches a specified regular expression.

`$options` - Specifies the regular expression options.

```js
{ field: { $regex: /pattern/, $options: '<options>' } }
```

`$type` - Matches any value that is of the specified type.

```js
{ field: { $type: "int", "bool", "string", ...	} }
```

`$all` - Matches any value that is an array and contains all elements specified in the array.

```js
{ name: { $all: ["Peter", "John"] } }
```

`$size` - Matches any value that is an array and contains the specified number of elements.

```js
// Finds the hobbies field that is an array and has 2 elements
db.user.find({ hobbies: { $size: 2 } });
```

`$elemMatch` - The $elemMatch operator matches documents that contain an array field with at least one element that matches all the specified query criteria.

```js
// Finds the user with age greater than 09 and less than or equal to 18
db.user.find({ age: $elemMatch: { $gt: 09, $lte: 18} });
```

> If we specify only a single <query> condition in the $elemMatch expression, and are not using the $not or $ne operators inside of $elemMatch, $elemMatch can be omitted, visit [single query conditions](https://www.mongodb.com/docs/manual/reference/operator/query/elemMatch/#std-label-elemmatch-single-query-condition) for more information.

`expr` - This filter allows we to do comparisons between multiple different fields on wer document.

```js
// Get all users that have a balance greater than their debt
db.users.find({ $expr: { $gt: ["$balance", "$debt"] } });
```

### Complex Update Object

In MongoDB we can pass a lot of information to the update function that isn't just field values.

`$set` - This will set the value of a field to the value passed in the update object.

```js
// Update the first user with an age of 26 to also have the name John
db.users.updateOne({ age: 26 }, { $set: { name: "John" } });
```

`$inc` - We can make use of the $inc option to add or subtract number to the given field.

```js
// Update the first user with an age 26 by subtracting 2 from its age
db.users.updateOne({ age: 26 }, { $inc: { age: -2 } });
```

`$rename` - This will rename a field to a new name.

```js
// Rename the age field to years on all users
db.users.updateMany({}, { $rename: { age: "years" } });
```

`$unset` - This lets we remove a field from a document. we must pass an empty string as the value for the field we want to remove.

```js
// Remove the age field from all users with the age of 26
db.users.updateOne({ age: 26 }, { $unset: { age: "" } });
```

`$push` - This lets we add a value to an array field.

```js
// Add Peter to the friends array for all users
db.users.updateMany({}, { $push: { friends: "Peter" } });
```

`$pull` - This is the opposite of `$push` and removes a value from an array field.

```js
// Remove Peter from the friends array for all users
db.users.updateMany({}, { $pull: { friends: "Peter" } });
```

### Read Modifiers

These methods can be passed end of read operation and will modify how the results are returned. we can also chain multiple of these modifiers onto one single read query.

`$sort` - This will sort the results by the given field. The results will be sorted in ascending order if we pass 1 to the field and descending order if we pass -1. In case of multiple fields they will be sorted in the order we pass them to sort.

```js
// Get all users sorted by name in ascending order and then sort names by age in descending order
db.users.find().sort({ name: 1, age: -1 });
```

`$limit` - This will limit the number of results returned.

```js
// Get the first 2 users
db.users.find().limit(2);
```

`$skip` - This will skip the first n results. This is useful in combination with limit to do pagination.

```js
// Get all users except the first 4
db.users.find().skip(4);
```

## Feedback

If we have any feedback, please reach out to me at [gmail](mailto:aaravmishra@gmail.com).
