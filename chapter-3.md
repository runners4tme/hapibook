# Chapter 3

In this chapter, we are going to cover the following points:

* What is mongoose js.
* Schema types that are supported by mongoose.
* How to add resources to the database.
* How to query resources from the database.
* How to stream resources from the database.
* How to create chained queries.
* User and Destination models.

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
$ cd model
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
    currency: {
        type: String,
        uppercase: true
    },
    url: {
        type: String
    },
    languages: [ String],
    capitalCity: {
        type: String,
        uppercase: true
    },
    activities: [ String ],
    population: {
        type: Number
    }
})

const Destination = mongoose.model('Destination', destinationSchema)

module.exports = Destination
```

The first step is always to import the module that we need to create the schema, then create an instance of the schema, the same way that we did for the users collection. We added the following properties in the schema:

* currency - This is a string. The currency that is used in the island should always be uppercase.
* url - This is a string. This is the internet page where we can learn more about our island.
* languages - This is an array. These languages will be a list of strings.
* activities - This is an array. These are the activities that are popular in the island, they are strings.
* population - This is a number. This is the number of people that live in the island.

We the create a model for our Destination and export it.

### S**ummary:**

1. What is mongoose js.
2. Supported Schema types.
3. ~~How to add resources to the database.~~
4. ~~How to query resources from the database.~~
5. ~~How to stream resources from the database.~~
6. ~~How to create chained queries.~~
7. User and Destination Models.



