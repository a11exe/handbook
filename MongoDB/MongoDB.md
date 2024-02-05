# Mongo DB
MongoDB is a document database. It stores data in a type of `JSON` format called `BSON`.

MongoDB stores data in flexible documents. Instead of having multiple tables you can simply keep all of your related data together. 
This makes reading your data very fast.

You can still have multiple groups of data too. In MongoDB, instead of tables these are called `collections`.
A record in MongoDB is a `document`, which is a data structure composed of key value pairs similar to the structure of JSON objects.

## Install
using command
```bash
docker run --name mongo -p 27017:27017 -d mongodb/mongodb-community-server:latest
docker exec -it mongo mongosh
db.runCommand(
   {
      hello: 1
   }
)
```
or using [docker-compose.yml](./docker-compose.yml)
```
docker compose up mongodb
```

## Useful commands
```bash
show databases;
use mongo; # user or create database
db.dropDatabase()
db.createCollection("posts") # create collection
db.posts.insertOne({
  title: "Post Title 1",
  body: "Body of post.",
  category: "News",
  likes: 1,
  tags: ["news", "events"],
  date: Date()
}) # insert document into collection
db.posts.find( {category: "News"} ) # find document
db.posts.find({}, {title: 1, date: 1}) # second parameter called projection which fields to include in the results 1 to include a field and 0 to exclude a field
db.posts.updateOne( { title: "Post Title 1" }, { $set: { likes: 2 } } ) # find and update
# insert the document if it is not found, you can use the upsert option.
db.posts.updateOne( 
  { title: "Post Title 5" }, 
  {
    $set: 
      {
        title: "Post Title 5",
        body: "Body of post.",
        category: "Event",
        likes: 5,
        tags: ["news", "events"],
        date: Date()
      }
  }, 
  { upsert: true }
)
# Update likes on all documents by 1. For this we will use the $inc (increment) operator
db.posts.updateMany({}, { $inc: { likes: 1 } })
db.posts.deleteOne({ title: "Post Title 5" }) # delete one
db.posts.deleteMany({ category: "Technology" }) # delete many
db.posts.find( {$or:[{category: "News"}, {category: "Event"}] } )  # query with condition or
db.posts.find().sort({ likes: 1 }) # sorting 1 asc, -1 desc
db.posts.find().limit(2) # limit select 
db.posts.findOne( { _id: ObjectId('65c09ef81d71b73ade0c2b9f') } )  # find by id
```

## MongoDB Query Operators
There are many query operators that can be used to compare and reference document fields.

### Comparison
The following operators can be used in queries to compare values:

+ `$eq`: Values are equal
+ `$ne`: Values are not equal
+ `$gt`: Value is greater than another value
+ `$gte`: Value is greater than or equal to another value
+ `$lt`: Value is less than another value
+ `$lte`: Value is less than or equal to another value
+ `$in`: Value is matched within an array

### Logical
The following operators can logically compare multiple queries.

+ `$and`: Returns documents where both queries match
+ `$or`: Returns documents where either query matches
+ `$nor`: Returns documents where both queries fail to match
+ `$not`: Returns documents where the query does not match

### Evaluation
The following operators assist in evaluating documents.

+ `$regex`: Allows the use of regular expressions when evaluating field values
+ `$text`: Performs a text search
+ `$where`: Uses a JavaScript expression to match documents

## MongoDB Update Operators
There are many update operators that can be used during document updates.

### Fields
The following operators can be used to update fields:

+ `$currentDate`: Sets the field value to the current date
+ `$inc`: Increments the field value
+ `$rename`: Renames the field
+ `$set`: Sets the value of a field
+ `$unset`: Removes the field from the document

### Array
The following operators assist with updating arrays.

+ `$addToSet`: Adds distinct elements to an array
+ `$pop`: Removes the first or last element of an array
+ `$pull`: Removes all elements from an array that match the query
+ `$push`: Adds an element to an array
