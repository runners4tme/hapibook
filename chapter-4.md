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
$ mkdir handlers
```

Handlers need to be created first before we can create the routes for our services. Inside our handle we also need to handle errors accordingly, we will be using a package called boom to handle errors for our application. Let's install the package.

```
$ yarn add boom
```

Now we are all set to start creating our handlers. We will start with our Destinations. We are going to create a handler for making a  new destination.

Inside the handlers folder, create a file called destination.js

```
$ touch destination.js
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

Now let's handle showing a destination.

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

Now we need to update a destination

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

We can also delete a destination if we are keen on doing so below.

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

Lastly we need to find all the destinations.

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

Now we need to add some controllers for our routes, let's create a folder called route on the route of our project.

```
$ mkdir routes
```

Let's change directory and get into the folder and create a file called destinations.js.

```
$ destinations.js
```

Inside the file, let's write the following code:

```js
const Destination = require('../../models/Destination')
const { routeCreator } = require('../../helpers/routeCreator')

const createDestination = {
    path: '/destinations',
    method: 'POST',
    handler: newDestination
}
```

Let's find this destination that we have just created.

```js
const findDestination = {
    path: '/destinations/{id}',
    method: 'GET',
    handler: showDestination
}
```

Firstly we have created an object that will handle the create of a destination, the next object will handle finding a single destination using an id.

```js
const editDestination = {
    path: '/destinations/{id}',
    method: 'PUT',
    handler: updateDestination
}
```

We are also allow to delete documents in the our destination collection.

```js
const deleteDestination = {
    path: '/destinations/{id}',
    method: 'DELETE',
    handler: removeDestination
}
```

The above object will handle the editing and finding all the destinations that are stored in our database. We are now done with the routes for our destinations, we will create the routes for users when we do the authentification.

Now let's focus on creating queries for our destinations.

```js
const findDestinations = {
    path: '/destinations', 
    method: 'GET',
    handler: showDestinations
}
```

We also need to combine our route helpers into an array and export them.

```js
module.exports = routeCreator(createDestination, findDestination, editDestination, findDestinations, deleteDestination)
```

We got to create an index file to export all our routes so that they can be used elsewhere.

```
$ touch index.js
```

Then add the following code to the file

```js
const destinations = require('./routes/destinations')

module.exports = [...destinations]
```

## **4. Plugins.**

We are going to register a couple of packages as Plugins so that we will be able to handle logs for our server on the console. We are going to use a package called good, we can define options for it below.

We now have move the server start function and calling it after registering all the packages to our server. We are going to log the port uri after we start the server. I want to log the errors, response and requests so that I can immediately pick up if there is any errors on our api server.

Let's install some of the packages that we want to add as Plugins to our server.

```
$ yarn add good good-console good-squeeze
```

We are going to add our Plugins and edit the code to start the server once the registration is complete.

```js
const server = require('../server');
const mongoose = require('mongoose');
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

### S**ummary:**

1. How to setup a hapi server.
2. Setting up routes for our server.
3. How to handle request sent to our server.
4. How to send response from our server.
5. Extending our server with Plugins.



