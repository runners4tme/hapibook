# Chapter 3

In this chapter, we are going to cover the following points:

* What is mongoose js.
* Mongoose js schema types. 
* User and Destination models.
* Mongoose: Adding a document to the database.
* Mongoose: Editing a document in a database.
* Mongoose: Querying documents from a database.
* Mongoose: Deleting a document from a database.

## Introduction

Mongoose is a very popular ORM for MongoDB. it is very robust and support the latest MongoDB. We are going to interact with the database using it, so we need to add this package to the project by running the following command:

```
$ yarn add mongoose
```

When you run yarn for the first time, it will create a lock file which will lock all the versions of the packages that we are going to create. This file has to be commit to any version control system that we are using to keep our packages consistent. This command is also going to create a folder called node\_modules for us, this folder will host all the modules that we will install for this project.

## Schema Types

Mongoose supports a lot schema types and the following types are supported:

1.1 String.

1.2 Date.

1.3 Object.

1.4 Number.

1.5 Array.

1.6 ObjectId.

1.7 Mixed.

To understand more about the schema types mentioned above, let's get started on creating a schema for our collection. Before we can continue, we need to create a new folder to hold all our models.

```
$ mkdir api
$ cd api
$ mkdir models
$ cd models
```

The first collection that we will create is called User. This collection will hold all the information about users that will be interacting with our application. It is a good convention to capitalize all the files for collection names. Now create a file for our destination called Destination.js inside the models folder.

## User Model

```
$ touch User.js
```

Let's write the following code inside our newly created file.

```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const userSchema  = new Schema({
    username:  {
        type: String,
        lowercase: true,
        index: true,
        unique: true
    },
    email: {
        type: String,
        lowercase: true,
        index: true,
        unique: true
   },
    password: {
        type: Schema.Types.Mixed,
        min: 6
    },
    name: {
        type: String,
        lowercase: true
    },
    age: {
       type: Number,
       min: 18
    },
    hobbies: [
     String
    ],
    dateOfBirth: {
        type: Date
    }
})

const User = mongoose.model('User', UserSchema)

module.exports = User
```

Let's go through the code together. We firstly imported the mongoose that will allow us to interact with the database. After that we created an instance of the Schema class that is available as a static method. A schema is a skeleton of all the documents inside a collection.  This allows us to control the properties of our documents by specifying the type, minimum length, maximum length and other attributes as well. We can also do validation to make sure that we save the correct data in our database.

Now let's go through some of the properties of our user's document.

* username - This is a string, which is required and should be unique for every user on our database. We have also created an index for this property which allows us to query for users faster versus properties that are not indexed at all. 
* email - This is a string, which is required and automatically converted to a lowercase before being saved to the database, we have also created an index for this field which will be used when we query the users' collection.
* password - This is mixed, we have set the minimum length of the password to 6 characters.
* name - This is a string, There are no restrictions for this property.
* age - This is a number. The minimum age of a user is 18 years. Anything below that will not be accepted.
* hobbies - This is an array. This will host a list of interests that the user has which are all strings.
* dateOfBirth - This is a date. This is the birth date of the user.

After setting up the schema for our User's collection, We create a model called User using the model static method. This method takes in a string as the first argument, which is the name of the collection and an object as the second argument, which is basically the schema of the collection. We lastly need to export our model so that we can use it anywhere in our application.

## Destination Model

We are done with the schema for collection of our users, we can now move forward to the schema for the collection of destinations. The destinations are going to be islands, so we are going to need to collect information about the langauges spoken there, the population and activities that a person can do amongst other things. This one promises to be an interesting one since we all love to travel.

Let's create a file for our destinations called Destination.js.

```
$ touch User.js
```

Then inside the file, add the following code to create the schema for destinations.

```js
const mongoose = require('mongoose')
const Schema = mongoose. Schema

const destinationSchema = new Schema({
    name: {
      type: String,
      uppercase: true
    },
    currency: {
        type: String,
        uppercase: true
    },
    languages: [ String],
    activities: [ String ],
    population: {
        type: Number
    },
    freeWifi: Boolean
})

const Destination = mongoose.model('Destination', destinationSchema)

module.exports = Destination
```

The first step is always to import the module that we need to create the schema, then create an instance of the schema, the same way that we did for the users collection. We added the following properties in the schema:

* name - This is a string. The name of the destination.
* currency - This is a string. The currency that is used in the island should always be uppercase.
* languages - This is an array. These languages will be a list of strings.
* activities - This is an array. These are the activities that are popular in the destination, they are strings.
* population - This is a number. This is the estimated number of people that live in the destination.
* freeWifi - This is a boolean. This is to tell whether the destination has free public wifi or not.

We created our model for our Destination and the last step is to export it. Now lets take a look a some of the ways that we can interact with the models that we have created above using Mongoose. We are going to use our destination model for illustration purposes.

## Mongoose: Adding a document to the database.

Mongoose has a simple api of creating a document, lets look how we can do that below. All the code that we are writing below has to be deleted once you are done with this chapter, otherwise you can create a separate file where you will import the Destination model and write the following code there. Let's create our first destination and save it to the database using a built-in save method.

```js
const capeTown = new Destination({
    name: "Cape Town",
    currency: "Rand",
    languages: ["Xhosa", "Afrikaans", "English"],
    population: 12356000,    
    interests: ["Surfing", "Fishing"],
    freeWifi: true
})

(async() => {
  await capeTown.save()
  console.log(`Destination saved`)
})()
```

The save method is asynchronous, this means that we need to call it and pass a callback as well which will let us to know that the database operation has completed. However i prefer to use self executing async functions, I think they are much more cleaner compared to callbacks and they also help us avoid the callback hell. Let's now move on to editing the population of Cape Town.

## Mongoose: Editing a document in a database.

```js
const capeTownId = capeTown._id

(async() => {
  const updatedCapeTown = await Destination.findOneAndUpdate({ _id: capeTownId}, {
    population: 423560
  }, { new: true } )
  console.log(`Destination updated`)
})()
```

As you can see, updating a destination is also easy as well, We use a method call findOneAndUpdate, which takes in three parameters, the first one is the query is which used to find the destination that we want to update, the second parameter is the object of what we want to update in the destination, the third one is optional, which is used to state that when we do the update, we want the updated destination to be returned. Now let's look at querying for destinations.

## Querying for documents with Mongoose

```js
const windHoek = new Destination({
    capitalCity: "Gaborone",
    currency: "Pula",
    languages: ["Tswana", "English"],
    population: 118500,    
    interests: ["Biking"],
    "freeWifi": true
})

(async() => {
  await windHoek.save()
  console.log(`Destination saved`)
  const allDestinations = await Destination.find({})
  console.log(allDestinations)
  const destinationsByPopulation = await Destination.find({}).where('population').gt(5000).exec()
  console.log(destinationsByPopulation)
  const oneDestination = await Destination.findOne({_id: capeTownId})
  console.log(oneDestination)
})()
```

There are many ways the query documents that we are only to cover the basic methods in this chapter. Firstly we add another destionation to our database.We have two destinations on our database now, we find all of them by using the built-in find method which takes it a query and returns all the destinations that matches to that query. We can also use the same method to write a chained query as well like in the example where we wanted a destination with a population greater than 5000. Lastly we look at how we can find a single destination using the findOne method, which returns the first destination that matches with the query provided. Let's move to the step step where we are going to learn how to remove a destination.

## Deleting a document with mongoose

```js
const windHoekId = windHoek._id

(async() => {
  await Destination.findOneAndDelete({ _id: windHoekId})
  console.log(`Destination deleted`)
})()
```

The last step that we need to learn now is how to delete a document from our destinations that we have stored in the database. There are more than one way to do this, we are going to cover the easiest way to remove a destination using its own id. We use a method called findOneAndDelete which allows us to find a destination using a query in this case by the id and after that we deleted it. We can easily see that Mongoose is quite easy to use and also very useful with regards to interacting with a MongoDb database. Let's take a look at what we have learnt so far.

### S**ummary:**

1. What is mongoose js.
2. Supported Schema types.
3. User and Destination Models.
4. Mongoose: How to add a document.
5. Mongoose: How to edit a document.
6. Mongoose: How to query for documents.
7. Mongoose: How to delete a document.



