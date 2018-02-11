# Chapter 6

In this chapter, we are going to cover the following topics:

* Understand what joi is all about.
* How to validate payload for our routes.
* How to validate params for our routes.
* How to validate query for our routes.

## **Introduction**

Let's add Joi to our package so that we will be able to handle validation of data from the client.

```
$ yarn add joi
```

We are going to create a folder called validators. inside it, we will create a file called destinations.js and define validators for payload, query and params as well. Let's create it below.

```
$ mkdir validators
```

We are going to use Joi which is a package that works really well with hapi framework, this will make it very easy to handle all the information that is sent to our api by the client.

Now we need to validate our users to make sure that users don't try to register and login with incomplete data.

Let's start by a user schema to make sure that the users provide all the required information.

```js
const Joi = require('joi')

module.exports.userSchema = Joi.object().keys({
    username: Joi.string().required(),
    password: Joi.string().required(),
    email: Joi.string().optional()
})
```

Let's apply it to our handler to validate the data when we sign up.

```js
options: {
    auth: false,
    validate: {
      payload: userSchema
    }
  }
```

Let's also validate the logging in of our users.

```js
options: {
    auth: false,
    validate: {
      payload: userSchema
    }
  }
```

## Payload validation

Let's also create a file to handle validation for our destinations

```
$ touch destinations.js
```

add this code

```js
const Joi = require('joi')

module.exports.rawDestinationSchema = Joi.object().keys({
    currency: Joi.string().required(),
    url: Joi.string().required(),
    languages: Joi.array().required(),
    capitalCity: Joi.string().required(),
    population: Joi.string().required()
})
```

Now we need to add the validator to the object that contains the handler for the route, this will make sure that we get proper error on the client when there has been a problem with the data the server.

This is the first function that will be executed every time a request hits this url and will reply immediately if there is any problem.

```js
options: {
        validate: {
            payload: rawDestinationSchema
        }
    }
```

## Params Validation

Lets also create a validator for the id of finding a destination.

```js
module.exports.idSchema = Joi.object().keys({
    id: Joi.string().length(24). required()
})
```

Now we will be able to get the get destination by id which is pretty much straight forward.

```js
options: {
        validate: {
            params: idSchema
        }
    }
```

Lets also create a validator for the query of finding destinations. All these parameters are optional in this case since will only use them to filter the results that we will get from the database.

```js
module.exports.destinationsSchema = Joi.object().keys({
    currency: Joi.string().optional(),
    url: Joi.string().optional(),
    languages: Joi.array().optional(),
    capitalCity: Joi.string().optional(),
    population: Joi.string().optional()
})
```

Now we will be able to get the get destinations and filter them using the queries that we are going to provide which is pretty much straight forward.

```js
options: {
        validate: {
            payload: destinationSchema,
            params: idSchema
        }
    }
```

## Query Validation

Lastly we need to make sure that we are sending the correct data to the server when we want to update a destination.

Now we will be able to get the get destination by id which is pretty much straight forward. We just need to re use the idSchema that we created above to complete this task.

```js
options: {
        validate: {
            query: destinationSchema
        }
    }
```

### S**ummary:**

We have learnt the following aspects from the chapter

1. Understand what joi is all about.
2. How to validate payload for our routes.
3. How to validate params for our routes.
4. How to validate query for our routes.



