# Chapter 5

In this chapter, we are going to cover the following topics:

* Understand what json web tokens.
* Learn about hapi schemes and strategies.
* Implement user signup and login.
* Apply auth to all the routes.

## Introduction

We are going to create functions that are going to handle users registration and logging in and out of the application. We will be using JSON web tokens to handle all the interactions with our server.

## JSON web tokens

These tokens contains three parts that might not be visible when you look at the token. The three parts are:

1. Header - This is an encoded JSON object which contains the the type of the token and the hashing algorithm used. This is the sha512 that we are going to use to create our token.
2. Payload - This is an encoded JSON object which contains the encrypted data. This will be comprised of the id, username and email address of the user and the expiry date of the token.
3. Signature - This is an encrypted hash of the header and and payload using the key that will be provided. This key is only known by the originating server that contains it, that is why it must not be hard coded in your code. It must be kept as an environmental variable.

We are certainly going to need a couple of functions that will handle the creation of the token for the user, the validation of the token and also the encoding and decoding of the token.We need to create a functions that will handle salting and hashing and also create JSON web tokens. Let's add the jsonwebtoken package used for encoding and decoding web tokens. 

```
$ yarn add jsonwebtoken
```

Now create a new folder called helpers inside the api folder and inside it create a file called userService.js

```
$ cd ..
$ mkdir helpers
$ touch authService.js
```

Let's import the need packages to get this task started. Crypto is a built-in package for creating password hashes.

```js
const Crypto = require('crypto')
const webtoken = require('jsonwebtoken')
```

We are going to create the following pure function to handle the creation of password salts, hashes and tokens.

* **getSalt** - for creating a random salt for our password.
* **getSaltedPassword** - for creating a salted password.
* **passwordIsInvalid** - for checking if the user password is valid or not.
* **createToken** - for creating a token.

```js
module.exports.getSalt = password => {
  return Crypto.randomBytes(16).toString('hex')
}

module.exports.getSaltedPassword = (password, salt) => {
  return Crypto.pbkdf2Sync(password, salt, 1000, 64, 'sha512').toString('hex')
}

module.exports.passwordIsInvalid = (rawPassword, encryptedPassword, salt) => {
  return encryptedPassword !== Crypto.pbkdf2Sync(rawPassword, salt, 1000, 64, 'sha512').toString('hex')
}

module.exports.createToken = (id, username, email) => {
  let expiryDate = new Date()
  expiryDate.setDate(expiryDate.getDate() + 7)
  return webtoken.sign({
    _id: id,
    username: username,
    email: email,
    exp: parseInt(expiryDate.getTime() / 1000)
  }, 'Hellofromtheotherside')
}
```

## User registration and login sessions

Let's start by creating a handler for signing up. So we need the file that will host all the interaction with our users. We import all the modules that are need for our authentication strategy. Now let's head to the handlers folder and create a file called user.js

```
$ cd ..
$ cd controllers
$ cd handlers
$ touch user.js
```

Now let's import our user service and user model with boom to handler our errors.

```js
const Boom = require('boom')
const User = require('../../models/User')
const { 
  getSalt, 
  getSaltedPassword, 
  passwordIsInvalid, 
  createToken 
} = require('../../helpers/userService')
```

We have to add a function to create the users.

```js
const createUser = async (username, password, email) => {
  try {
    const salt = getSalt(password)
    const enctryptedPassword = getSaltedPassword(password, salt)
    const newUser = { username, salt, password: enctryptedPassword }
    const user = await User.create(newUser)
    return user
  } catch(error) {
    throw error
  }
}
```

We start off by creating an async function called createUser, this is a function that we will use to create a new user. it will take a username, password and email as parameters. Inside it, we create a salt and encryptedPassword using our getSalt and getSaltedPassword functions. We create and save the user in our database. We lastly return the id of this user.

Now let's add a handler for signing up for our users.

```js
module.exports.signUpUser = async (request, helper) => {
  try {
    const { payload: { username, password, email }} = request
    const salt = getSalt(password)
    const enctryptedPassword = getSaltedPassword(password, salt)
    const newUser = { username, salt, password: enctryptedPassword }
    const user = await User.create(newUser)
    const token = createToken(user._id, username, email)
    return helper.response({token}).code(201)
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

We create an async function called signUpUser, inside it, we destructure the payload from the request to get all the parameters we want which is the username, password and email. After this we create a salt and hashed password. We then create a newUser object containing the salted and hashed password, we then create and save this use inside our database. After saving it to the database. We use createToken to create a new token with the user's id, username and email. We generate a response with the token to login the user and set the status code. If something goes wrong we respond with the error.

Now let's create another one for signing in the users.

```js
module.exports.signInUser = async (request, helper) => {
  try {
    const { payload: { username, password }} = request
    const user = await User.findOne({ username: username })
    if (!user) {
      return Boom.unauthorized('invalid username')
    } else if (passwordIsInvalid(password, user.password, user.salt)) {
      return Boom.unauthorized('incorrect password')
    } else {
      const token = createToken(user._id, username, user.email)
      return helper.response({token}).code(200)
    }
  } catch (error) {
    return Boom.badRequest(error)
  }
}
```

Let's create the handlers to make it possible to sign up and login into the application.

Start by registering the user.

```js
const { signUpUser, signInUser } = require('../handlers/user')
const { userSchema } = require('../../validators/users')

const signUp = {
  path: '/users/signup',
  method: 'POST',
  handler: signUpUser
}
```

We need to sign up users as well.

```js
const signIn = {
  path: '/users/signin',
  method: 'POST',
  handler: signInUser
}
```

Now let's make all the function available to other parts of our application.

```js
module.exports = [ signUp, signIn ]
```

## Anthentification Schemes and Strategies

We need to create a custom auth decorator that will wrap all our requests to all the endpoints that requires the user to be logged in to access the resources that exist in that location. Let's add it to our project. Firstly let's import the packages that are need for this task.

```js
const Boom = require('boom')
const jwt = require('jsonwebtoken')
```

Then we need to create a pure function that will process our token from the client.

```js
const authStatus = token => {
  return {
    authTokenNotProvided: !!token,
    authTokenLengthIsInvalid: token.split('.').length !== 3,
    authTypeIsInvalid: token.split('.')[0].split(' ')[0] !== 'Bearer'
  }
}
```

Lastly we need a scheme that will handle all the results of the status of the token that we just process.

```js
const scheme = (server, options) => {
  return {
    authenticate: (request, helper) => {
      try {
        const { authorization } = request.headers
        const { authTokenNotProvided, authTokenLengthIsInvalid, authTypeIsInvalid } = authStatus(authorization)
        if (authTokenNotProvided) {
          return Boom.unauthorized('missing token', 'Bearer')
        } else {
          if (authTokenLengthIsInvalid) {
            return Boom.unauthorized('invalid token', 'Bearer')
          } else if (authTypeIsInvalid) {
            return Boom.unauthorized('invalid token type', 'Bearer')
          } else {
            const token = authorization.split(' ')[1]
            const payload = jwt.verify(token, 'Hellofromtheotherside')
            return helper.authenticated({ credentials: payload })
          }
        }
      } catch (error) {
        return Boom.unauthorized(error)
      }
    }
  }
}

module.exports.authStatus = authStatus
module.exports.scheme = scheme
```

### S**ummary:**

We have learnt the following aspects from the chapter

1. Understand what json web tokens.
2. Learn about hapi schemes and strategies.
3. Implement user signup and login.
4. ~~Apply auth to all the routes.~~



