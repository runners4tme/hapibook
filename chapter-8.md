# Chapter 8

In this chapter, we are going to cover the following topics:

* Introduction.
* Mocha, sinon and code.
* How to write unit tests.
* How to integration tests.
* How to structure assertions. 

## Introduction

We are going to create a folder that will hosts all our tests.

```
$ mkdir test
```

Now let's create two more files, one file will be for unit tests and the other one will be for integration tests.

```
$ touch unitTests.js
$ touch integrationTests.js
```

We need to add to packages for managing our tests. We install them as development dependencies since we don't want to install these packages when we have our application in production.

```
$ yarn add mocha sinon code --dev
```

### Mocha

This is a test runner.

### Sinon

This is a function for double tests.

### Code

This package is for assertions.

## Unit tests

Units tests are for testing a single unit of the application and check if it works in isolation. Let's start writing some unit tests for our functions.

```js
const { getSalt, getSaltedPassword, passwordIsInvalid, createToken } = require('../api/helpers/userService')
const { createUser } = require('../api/controllers/handlers/user')
const { authStatus } = require('../api/helpers/scheme')
const { expect } = require('code')
const { describe, it } = global
const sinon = require('sinon')
```

We are ready to write some tests now, let's add them to test all the units of our application. Let's start with the token validator function that we use to validate the token. We can easily tests what this function will return each time it is used on it's own.

```js
describe('authStatus', () => {
  describe('status with valid token', () => {
    it('should validate the token', async () => {
      const validToken = 'dasvcjaegv*&^@je.bvaa@#&^*&#hjdakj.fcthh@#%^@%wdfhqcv'
      const result = authStatus(validToken)
      expect(result.authTokenNotProvided).to.be.falsy()
      expect(result.authTokenLengthIsInvalid).to.be.falsy()
      expect(result.authTypeIsInvalid).to.be.falsy()
    })
  })
  describe('status with invalid token', () => {
    it('should invalidate the token', async () => {
      const inValidToken = 'hgdsaugkdwgg@&^36#7VGSDHwgqgdwyka'
      const result = authStatus(inValidToken)
      expect(result.authTokenNotProvided).to.be.truthy()
      expect(result.authTokenLengthIsInvalid).to.be.truthy()
      expect(result.authTypeIsInvalid).to.be.truthy()
    })
  })
})
```

Nice, now that we have some comfort on the token validator, we can move over to test the user services.

```js
describe('userService', () => {
  describe('getSalt', () => {
    it('should get salt', async () => {
      const password = 'password'
      const result = getSalt(password)
      expect(result).to.be.typeof('string')
      expect(result.length).to.be.equal(16)
    })
  })
  describe('getSaltedPassword', () => {
    it('should salted password', async () => {
      const salt = '43f716b253be173f9fa75013ea27fa42'
      const result = getSaltedPassword(password, salt)
      expect(result).to.be.typeof('string')
      expect(result.length).to.be.equal(64)
    })
  })
  describe('passwordIsInvalid', () => {
    it('should validate the password', async () => {
      const password = 'password'
      const saltedPassword = 'e876483449e05e9b2754fad90717e92ef4195ad3f7f49f613783613acb9c9844395de4f8f94381cc56f37786d0d70f74077564268871c67caddc8683b206b057'
      const isPasswordInvalid = passwordIsInvalid(password, saltedPassword, salt)
      expect(result).to.be.typeof('boolean')
      expect(result).to.be.truthy()
    })
  })
  describe('createToken', () => {
    it('should create a token', async () => {
      const user = { id: '12345', username: 'les', email: 'les@gmail.com' }
      const result = createToken(user)
      expect(result).to.be.typeof('string')
      expect(result.split('.').length).to.be.equal(3)
    })
  })
})
```

We need to do one more unit test for our function that we use to create a user.

```js
describe('Users', () => {
  describe('createUser', () => {
    it('should create a user', async () => {
      const expectedData = {
        username: 'latani',
        password: 'rockstar123',
        email: 'latani@gmail.com'
      }
      const stub = sinon.stub(User, 'create').resolves({ _id: 1 })
      const result = await createUser(expectedData)
      expect(result._id).to.be.equal(1)
      stub.restore()
    })
  })
})
```

## Integration tests

Integration tests are for testing that the whole application works well together. Now let's add some tests. We need to declare all the packages that we are going to use to write our tests.

```js
const server = require('../server')
const Destination = require('../api/models/Destination')
const sinon = require('sinon')
const { expect } = require('code')
const { describe, it } = global
```

We are going to add all the tests inside this block of code, this will hold all the tests for destinations.

```js
describe('Destinations', () => {

})
```

Let's create the test for creating the destinations.

```js
describe('POST /destinations', () => {
    it('should create a destination', async () => {
      const expectedData = {
        currency: 'ZAR',
        url: 'www.musanda.co.za',
        capitalCity: 'lukau',
        population: 30000,
        languages: ['tshivenda'],
        activities: ['rulling']
      }
      const options = {
        method: 'POST',
        url: '/destinations',
        payload: expectedData
      }
      const stub = sinon.stub(Destination, 'create').resolves({ _id: 1 })
      const response = await server.inject(options)
      expect(response.statusCode).to.equal(201)
      expect(response.result.id).to.equal(1)
      stub.restore()
    })
  })
```

Let's create test for getting a destination.

```js
describe('GET /destination', () => {
    it('should get a destination', async () => {
      const expectedData = {
        currency: 'ZAR',
        url: 'www.musanda.co.za',
        capitalCity: 'lukau',
        population: 30000,
        languages: ['tshivenda'],
        activities: ['rulling']
      }
      const options = {
        method: 'GET',
        url: '/destinations/5a173a2336a04f1a048e8589'
      }
      const stub = sinon.stub(Destination, 'findOne').resolves(expectedData)
      const response = await server.inject(options)
      expect(response.statusCode).to.equal(200)
      expect(response.result).to.equal(expectedData)
      stub.restore()
    })
  })
```

Let's create a test for testing destinations.

```js
describe('GET /destinations', () => {
    it('should get all destinations', async () => {
      const expectedData = [{
        id: 1,
        currency: 'ZAR',
        url: 'www.musanda.co.za',
        capitalCity: 'lukau',
        population: 30000,
        languages: ['tshivenda'],
        activities: ['rulling']
      }]
      const options = {
        method: 'GET',
        url: '/destinations'
      }
      const stub = sinon.stub(Destination, 'find').resolves(expectedData)
      const response = await server.inject(options)
      expect(response.statusCode).to.equal(200)
      expect(response.result.length).equal(1)
      stub.restore()
    })
  })
```

Lets create a test to update a destination

```js
describe('UPDATE /destination', () => {
    it('should update a destination', async () => {
      const options = {
        method: 'PUT',
        url: '/destinations/5a173a2336a04f1a048e8589',
        payload: {
          currency: 'USD',
          url: 'www.coldbeach.co.za',
          capitalCity: 'chicago',
          population: 40000
        }
      }
      const expectedData = {
        currency: 'USD',
        url: 'www.coldbeach.co.za',
        capitalCity: 'chicago',
        population: 40000,
        languages: ['tshivenda'],
        activities: ['rulling']
      }
      const stub = sinon.stub(Destination, 'findOneAndUpdate').resolves(expectedData)
      const response = await server.inject(options)
      expect(response.statusCode).to.equal(200)
      expect(response.result).to.equal(expectedData)
      stub.restore()
    })
  })
```

Let's create a test to test deleting a destination.

```js
describe('DELETE /destination', () => {
    it('should delete a destination', async () => {
      const options = {
        method: 'DELETE',
        url: '/destinations/5a173a2336a04f1a048e8589'
      }
      const stub = sinon.stub(Destination, 'findOneAndRemove').resolves(null)
      const response = await server.inject(options)
      expect(response.statusCode).to.equal(204)
      expect(response.result).to.equal(null)
      stub.restore()
    })
  })
```

### S**ummary:**

1. How to write unit tests.
2. Mocha, sinon and code.
3. How to write unit tests.
4. How to write integration tests.
5. How to structure assertions.





