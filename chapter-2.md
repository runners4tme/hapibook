# Chapter 2

In this chapter, we are going to cover the following points.

* What is MongoDB.
* Basic MongoDB commands.
* How to insert a document to a collection.
* How to edit a document in a collection.
* How to find documents in the database.
* How to remove a document.

## **Introduction**

**MongoDB is a schemaless database**. It stores the data as BSON\(binary representation of JSON\) documents. A database in MongoDB hold multiple collections of documents. Therefore every time you need to store something in the database, you need to create a collection. This is because MongoDB store documents inside collections. Each single entry to the database is called a document. the documents are stored in json format which makes it very easy to use with api's since we don't need to serialize the data when we get it from the database. MongoDB ships with some default configurations which makes it easy to started a mongo server. If you don't specify a port for on your mongo shell, it will automatically connect to the 27017 running on the localhost of your machine.

Let's start playing around with our local MongoDB, you can run the following command to connect to the server of your database. Run the following command in your console. It will open the interactive shell console that will allow us to write documents into the database.

```
$ mongo
```

There is a nifty command which is used to list all the databases that we have locally. By default MongoDb comes with a database call test which is very useful during development locally.

```
$ show dbs
```

We now to specify the database that we want to use for our application, the command below will create a database called destinations if it doesn't exist already, if it does, it will switch to the database. Thus we are going to be using a database called destinations throughout this book.

```
$ use destinations
```

## Adding documents to the database.

We can add a document using insertOne inside our database.

We just have to specify the collection name before we insert it to the database. It is important to be sure that the data is in json format otherwise the request will not be processed at all. Write the following code inside the mongo shell.

```
$ db.beaches.insertOne({
    "currency": "Rand",
    "url": "www.capepoint.com",
    "languages: ["English"],
    "capitalCity": "Cape Town",
    "population": 123560,    
    "interests" ["Surfing, Fishing"]
})
```

If you press enter on your keyboard, it will return id of the newly created destination. Let's take a closer look at the method that we just used to insert a destination into our database.

* beaches =&gt; This refers to the collection name that we want to insert our document.

* insertOne =&gt; This refers to the method that we want to perform on the database.

We can also add multiple documents to the database at the same time, We just have to use a similar method to the one that we used to create a destination.

```
$ db.beaches.insertMany([{
    "currency": "Rand",
    "url": "www.capepoint.com",
    "languages: ["English"],
    "capitalCity": "Cape Town",
    "population": 123560,    
    "interests" ["Surfing, Fishing"]
},{
    "currency": "Rand",
    "url": "www.capepoint.com",
    "languages: ["English"],
    "capitalCity": "Cape Town",
    "population": 123560,    
    "interests" ["Surfing, Fishing"]
}])
```

Let's go throw the code together.

* beaches =&gt; This refers to the current collection that we are inserting our documents.

* insertMany =&gt; This refers to the method that we want to perform on the database. This method takes in an array of all the documents that have to be inserted.

## Updating a document in a database.

Updating a document that already exist in our database is very easy to do, we just need to pass an id of the document that we want to update and also the new information that we want to be added to be added to the document. We can also pass any other properties that are part of our destination that we have created, This function will return the newly updated document.

```
$ db.beaches.updateOne({ id: "16163871738"}, { $set:{
  "capitalCity": "Cape Town",
    "population": 123560,
}})
```

* beaches =&gt; This refers to the collection that contains the document that has to be updated.
* updateOne =&gt; This is the method that that will update the destination and return the newly updated destination.

You can also perform multiple update on the documents inside your database using the following command.

```
$ db.beaches.updateMany({},{ $set: {
    "sharksPresent":true
}}})
```

* beaches =&gt; This refers to the collection that contains the documents that we want to be update at the same time.
* updateMany =&gt; This refers to the method that we want to perform and it takes in update filter, and the update action. This method can be used for batch updates.

## Finding documents in a database.

Querying for documents from our database is also easy, you can do it by calling the following command.

```
$ db.beaches.find({}).limit(10)
```

* beaches =&gt; This refers to the collection that contains the documents that we want to query.
* find =&gt; This is the method that will use to select all the documents in the collection.
* limit =&gt; This refers to the query modifier. This is use to limit the number of documents return form the query.

This command will return all the documents inside the collections. You can also use the following command to add find only one document from the collection. You have to provide a parameter that will match the document that you are looking for inside the database. Make sure that the parameter is index to make the query perform faster.

```
$ db.beaches.findOne({id: "1y9319893" })
```

* beaches =&gt; This refers to the collection that contains the documents that we want to query.
* findOne =&gt; this method will return only one document where the id is the specified number that is given above.

## Deleting documents from a database.

Deleting a document from the database is straight forward, all we do is that we just going to provide the id of the document that we want to delete and the document will be removed. The command below will remove the object we just added to our local database.

```
$ db.beaches.deleteOne({ id: "16163871738" })
```

* beaches =&gt; This refers to the collection that contains the documents that we want to delete.
* deleteOne =&gt; This is the method used to delete one document.

We can also delete multiple documents in our database in situation where we just feel like starting from a clean slate. The following command can be used to accomplish this task.

```
$ db.beaches.deleteMany({})
```

* beaches =&gt; This refers to the collection that contains the documents to be deleted.
* deleteMany =&gt; This is the method used to delete many documents from a collection.

### **Summary:**

1. What is MongoDB.
2. Basic MongoDB commands.
3. How to insert a document to a collection.
4. How to edit a document in a collection.
5. How to find documents in the database.
6. How to remove a document.



