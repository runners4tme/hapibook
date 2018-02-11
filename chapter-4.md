# Chapter 4

In this chapter, we are going to cover the following points:

* How to setup a hapi server.
* Setting up routes for our server.
* How to handle request sent to our server.
* How to send response from our server.
* Extending our server with Plugins.

## **Introduction**

This chapter is going to explain what makes hapi framework above a notch compared to our NodeJs frameworks . Hapi Js was build through with the idea that configuration is better than writing code. This is why most of the aspects that you will be doing with hapi, you actually won't be writing any code to handle things like errors, you will configure your routes in such a way that they will be able to handle the errors in a clean and maintainable manner. We are going to create a simple hapi server to get our application up and running. We will also explore the request and helper toolkit that ships with Hapi JS and lastly learn how to extend our server with plugins.

## **1. The server.**

We are going to use a Hapi JS to create our server. The server object is the foundation of our application, everything part of our application must connect with this object. All hapi servers have options that can be configured to control the behavior of the object and also extend it if necessary.

Now let's change directory to the main folder of our application, from there, create a file call server.js using the following command. This file will hold all the configuration for our server.

```
$ touch server.js
```

Install hapi into our project using yarn.

```
$ yarn add hapi.js
```

We are now ready to setup our server for testing. There is a class method inside hapi package which we are going to use to create the server. Lets create a new server and also configure the host and port. Each hapi server can only accommodate one connection at a time, that is why we have to set the host and port when we create an instance of the server. The method to create a connection is no longer available in the latest version of Hapi JS.

```js
const hapi = require('hapi')
const server = new hapi.Server({
    host: 'localhost',
    port: 3000
})
```

Above we have setup a server that is hosted on localhost and running on port 3000.

The following methods amongst others are available to assist you with server configuration.

* **server.route** - This method is for configuring routes for your server. It accepts a object or an array of objects as a parameter.
* **server.start** - This method is useful for starting the server. This method returns a promise and throws an error if the server is not correctly configured or will not be able to listen to incoming events.
* **server.stop** - this method is useful for stoping the server. It accepts options that can be used to override the time needed to stop all the incoming requests to the server.
* **server.register** - this method is used to register plugins to the server. It accepts a plugin object and optional parameters. 

Now let's start the server and see it in action by creating a single route to test it. We are going to create a single route using the server.route method. We are going to create an object that will handle the request when with hit '/' on our server. The object can have the following four properties, the fourth property is optional.

* **path** - This is the absolute path for all the incoming requests.

* **method** - This is the HTTP method we want to perform on the resource. This can be a single method or any array of methods that are assigned to the same path specified.

* **handler** - This is the function that will handle the request and handle the authentication, validation  and generate the correct response for our route.

* **options **- This is an object that is used for setting additional parameters for our route like validation, auth strategies, etc.

Add the following code inside server.js

```js
server.route({
    path: '/',
    method:  'GET',
    handler: (request, handler) => {
        try  {
            return 'api for destinations'
        }  catch (error)  {
            return error
        }
    }
})
```

Let's now use another method that comes with our server. We are going to server.start to initiate our server, this returns a promise**,** we can chain events to the promise that will be returned by the built-in method. And we will use it to log the port where we are running our server. We will log the error if something goes wrong during this process.

```js
server.start()
.then(() => {
    console.log(`running on ${server.info.port}`)
})
.catch(err => {
    console.log(err)
})
```

Run the following command to start the server:

```
$ yarn start
```

The command will run node with our entry file called server.js, this will start up our server. Open your favorite browser and run the following command on your console. When we visit the localhost:3000, the browser should respond with the following message.

```
'api for destinations'
```

Now that we are sure that the server is running, we can create separate scripts to start and stop the server with different configureations. Let's create a new folder called bin.

```
$ mkdir bin
```

Let create two files, called start.js and stop.js to hold our scripts. Let's start with the file that will be used for configuring the server and connecting to the database.

```
$ touch start.js
```

We can remove the code for starting the server from our main server file.You can add the following code inside start.js.

```js
const server = require('./server')

(async () => {
    try {
        await server.start()
        await mongoose.connect('mongodb://127.0.0.1/test', { useMongoClient: true })
        console.log(`Server running at ${server.info.port} and connected to the Db!`)
    } catch(err) {
        console.log(err)
    };
})();
```

We are using a function that calls itself that wraps around our async function. Inside it we start the server and then connect to our database, then console log the information about the port that we are listening to and also make it clear that we are connected to the database. We catch any errors that might occur when we start the server or connection to the database.

Now let's create a simple file called stop.js to hold our code to stop the server.

```
$ touch stop.js
```

We are going to manage the stop our server in a similar manner like the way we start our server. You can also add the following code to stop our server.

```js
const server = require('./server')

(async () => {
    try {
        await server.stop()
        console.log(`Server stopped running at ${server.info.port}`)
    } catch(err) {
        console.log(err)
    };
})();
```

Here we are also using a function that calls itself. Inside this function, we actually don't do that much, other than the fact that we stop the server and log a message which informs us that the server has stopped, we also catch error, if they the occur when we are stopping our server.

## **2. The request.**

The request object in hapi js is created internally for each incoming request and holds all the details about the request that you are sending to the server, this include the headers, events, info, query, params, payload and authentification details that are all available for you to use inside the handler function. The request object has the following important properties that we need to understand a bit before we go any further with extending the functionality of our server.

_**2.1 request.payload**_

This is the body object which contains the properties and values that are sent with the request.

_**2.2 request.params**_

This is the object that contains the parameters is passed through the url when a request is processed.

**2.3 request.query**

This is the object that contains the parsed query string 3that is passed through the url by the client.

## **3. The helper toolkit.**

The helper toolkit contains the methods that is used to send information to the client after it has request something from the server. This can also be extended to include the status code. The following methods are part of the helper toolkit and will come in handy later on in the book.

* **helper.response** - This method takes in a parameter and returns a response object, you can specify headers, types etc.
* **helper.redirect** - This method takes in a path and redirect the client to the the given parameter.
* **helper.authenticated** - This method takes in an object and passes the valid credentials to the request object.
* **helper.unauthenticated** - This method takes in an object and passes an error and invalid credentials when authentication fails.

We can use some of the above methods to create controllers for our application. Controllers will allow us to interact with oiur database, Create the controllers folder.

```
$ mkdir controllers
```

Let's make a folder for handlers inside our controllers folder. This folder will hold all the handlers for our routes.

```
$ cd controllers
$ mkdir handlers
```

Handlers need to be created first before we can create the routes for our services. Inside our handle we also need to handle errors accordingly, we will be using a package called boom to handle errors for our application. Let's install the package.

```
$ yarn add boom
```

Now we are all set to start creating our handlers. We will start with our Destinations. We are going to create a handler for making a  new destination.

Inside the handlers folder, create a file called destination.js

```
$ cd handlers
$ touch destination.js
```

Import the Destination model and also boom for handling our errors.

```js
const Boom = require('boom')
const Destination = require('../../models/Destination')
```

Add the following code for creating a new destination.

```js
module.exports.newDestination = async (request, helper) => {
  try {
    const payload = { ...request.payload }
    const destination = await Destination.create(payload)
    return helper.response({ id: destination._id }).code(201)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

To create a new destination is simple, We create an async function, inside it, we firstly create a new object called payload using object spread. This is just a short-hand for the built-in object assign method. Then we create and save a new destination to our database. Lastly we use the response method to generate a response for our route and specify the response code. If anything goes wrong, we use boom to create an error response and return it.

Now let's handle showing a destination. Add the following code for showing a destination.

```js
module.exports.showDestination = async (request, helper) => {
  try {
    const id = request.params.id
    const destination = await Destination.findOne({_id: id})
    return helper.response(destination).code(200)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

We start off by creating an async function, inside it we create a new variable called id, which is the id of the destination that we want to show. We use the findOne method to find the destination for our collection. Now we lastly create a response which is all the information stored for this destination and set the response code as well. If anything goes wrong, we use boom to create an error response and return it.

Now we need to update a destination, add the following code for updating a destination.

```js
module.exports.updateDestination = async (request, helper) => {
  try {
    const id = request.params.id
    const payload = { ...request.payload }
    const destination = await Destination.findOneAndUpdate({_id: id}, payload, {new: true})
    return helper.response(destination).code(200)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

Updating a destination involves two operations, finding the destination that we want to update and updating the destination. Luckily we can use findOneAndUpdate method to do these two operations at the same time, we start off by creating an async function, inside it, we declare two variables, id which is the id of the destination we want to update and payload which is the data that we want to update our destination with, we then update the destination with the findOneAndUpdate method. We can now send the updated destination with our response. If anything goes wrong, we use boom to create an error response and return it.

We need to do is to remove a destination, if we don't want it anymore. Add the following code for deleting a destination.

```js
module.exports.removeDestination = async (request, helper) => {
  try {
    const id = request.params.id
    const destination = await Destination.findOneAndRemove({_id: id})
    return helper.response(destination).code(204)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

We need only need to know the id of the destination that we want to delete to get this done, so we start by creating an async function, then we create a variable call id passed through url parameters. Now we can use the findOneAndRemove method to delete it. We respond with nothing and appropriate code for this operation.

Lastly we need to find all the destinations. Add the following code for finding all the destinations.

```js
module.exports.showDestinations = async (request, helper) => {
  try {
    const query = { ...request.query }
    const destinations = await Destination.find(query)
    return helper.response(destinations).code(200)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

This is the last handler for our destinations. We need to use the query object from our request for this task. We create an async function, inside this function, we create a variable called query which will hold all the parameters that we want to filter our destinations with when we request for them. This means that we can filter destinations by currency, population, etc. We then use our helper toolkit to create a response and return it. If we encounter an error, we respond with a bad request.

Now we need create the object that will be used for configuring our routes, let's create a folder called routes inside the controllers directory.

```
$ mkdir routes
```

Let's change directory and get into the folder and create a file called destinations.js.

```
$ cd routes
$ destinations.js
```

Inside the file, we need to import the handlers that we just created:

```js
const { 
  newDestination, 
  showDestination, 
  updateDestination, 
  removeDestination, 
  showDestinations 
} = require('../handlers/destination')
```

We are going to create to create a destination on path '/destinations' and the method use is POST, we will use the newDestination handler.

```js
const createDestination = {
    path: '/destinations',
    method: 'POST',
    handler: newDestination
}
```

We are going to find a destination using an id, thus the path for finding a single destination is '/destinations/{id}' and the method is GET, we will use the showDestination handler in this task.

```js
const findDestination = {
    path: '/destinations/{id}',
    method: 'GET',
    handler: showDestination
}
```

We are going to update a destination using an id, thus the path for finding a single destination is '/destinations/{id}' and the method is PUT, we will use the updateDestination handler in this task.

```js
const editDestination = {
    path: '/destinations/{id}',
    method: 'PUT',
    handler: updateDestination
}
```

We are going to delete a destination using an id, thus the path for finding a single destination is '/destinations/{id}' and the method is DELETE, we will use the deleteDestination handler in this task.

```js
const deleteDestination = {
    path: '/destinations/{id}',
    method: 'DELETE',
    handler: removeDestination
}
```

We are going to find all the destinations using the path '/destinations'. The method is GET and the showDestinations will handle this operation.

```js
const findDestinations = {
    path: '/destinations', 
    method: 'GET',
    handler: showDestinations
}
```

We also need to combine our route helpers into an array and export them.

```js
module.exports = [ 
  createDestination, 
  findDestination, 
  editDestination, 
  findDestinations, 
  deleteDestination
]
```

We got to create an index file to export all our routes so that they can be used inside our server.js file.

```
$ cd ..
$ touch index.js
```

Add the following code to the file to import our objects for configuring routes and combine all of them.

```js
const destinations = require('./routes/destinations')

module.exports = [...destinations]
```

We have successfully created all the configuration for our destination routes, let's extend our server further with plugins.

## **4. Plugins.**

We are going to register a couple of packages as plugins so that we will be able to handle logs for our server on the console. We are going to use a package called good, we can define options for it below.

We need to register the plugins before we start the server and calling it after registering all the packages to our server. We are going to use monitor events that will be happening to our server. Whenever such events occurs, I want to log the errors, response and requests so that I can immediately pick up if there is anything not working well on our api server.

Let's install some of the packages that we want to add as plugins to our server.

```
$ yarn add good good-console good-squeeze
```

We are going to add our plugins and edit the code to start the server once the registration is complete. We start by creating options for our registration of the server. After that we register good as a plugin using the built-in method register. Then we start the server and connect to the database.

```js
const server = require('../server');
const mongoose = require('mongoose');
const good = require('good');
mongoose.Promise = global.Promise;

const options = {
    ops: {
        interval: 1000
    },
    reporters: {
        myConsoleReporter: [{
            module: 'good-squeeze',
            name: 'Squeeze',
            args: [{log: '*', error: '*', requests: '*', response: '*'}]
        }, {
            module: 'good-console'
        }, 'stdout']
    }
};

(async () => {
    try {
        await server.register({ plugin: good, options })
        await server.start()
        await mongoose.connect('mongodb://127.0.0.1/test', { 
          useMongoClient: true 
        })
        console.log(`Server running at ${server.info.port} and connected to the Db!`)
    } catch(err) {
        console.log(err)
    };
})();
```

Whenever you now make a request to our server, the path, status code and other details will be logged to our console.

### S**ummary:**

1. How to setup a hapi server.
2. Setting up routes for our server.
3. How to handle request sent to our server.
4. How to send response from our server.
5. Extending our server with Plugins.



