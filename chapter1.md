# Chapter 1

In this chapter, we are going to cover the following topics:

* Introduction.
* NodeJs.
* Package Managers.
* Node Modules.
* Project Structure.
* Summary.

## **Introduction**

This chapter covers all the fundemental about NodeJS ecosystem and all the necessary setup that will come in handy when we start building our api in the later chapters. You need to understand a couple of core concepts in general about writing code and also how  We need to install a couple of applications and also set it to be able to follow up with the book and examples inside this book.

## NodeJs

NodeJS runs on v8 engine by chrome, it allows us to write javascript code that can run outside the browser. This means that you can write servers, scripts and other things using JavaScript. As the saying goes, whatever can be written in JavaScript, will end up written in Javascript.NodeJs engine uses a single threaded architecture, this mean that it can only run a single process at a time. It has event loop that queues' all the events so that they can be processed one by one. The advantage of this is that you can continue interacting with your application since these events are non-blocking as the program. This means that your most of your I/O, database operations and network requests, you will need to provide a callback that will handle the result when the application finally returns the result from these operations.

You also need to download NodeJs platform so that you will be able to run your javascript code locally on your machine. You can download the latest stable version of NodeJS at [https://www.nodejs.org/](https://www.nodejs.org/) for your respective operating system.

To check if you have downloaded node successfully, You need to run the following command, if the installation was successful, the console should would have printed the version of Node JS installed in your machine:

```command-line
$ node -v
$ 8.9.1
```

The non-blocking archicture back in the days gave NodeJS a bad rep bacause it would always lead to call back hell when you are handling asynchronous code, this has was improved by the introduction of promises which made it easy to write readable code that can be chained together. A promise can resolve successfully or reject if something doesn't go well, it can also be pending if it hasn't returned the results yet. You could easily call a promise and use then to handle the results and catch to handle the errors that might occur when calling the promise.

## Package Managers

**Yarn** is a fast, safe and reliable dependency manager as stated on it's website. What makes yarn fast is the fact that it caches all the packages that it downloads so that it does not have to download it again. It also download the packages in parallel which makes installation faster for packages. It is also very reliable since it uses check sum to validate the integrity of every package installed. And lastly, it makes use of a lock file which makes sure that the same version of the packages used are consistent over different machines, avoiding awkward cases of "it works on my machine". This is why I would recommend that you use Yarn over NPM which comes with NodeJS.

**Let's go through some YARN basic commands that are useful for managing packages in a project.**

Starting a project.

```
$ yarn init
```

Adding a dependency.

```
$ yarn add [package]
```

Adding a dependency to a different category.

```
$ yarn add [package] --dev
```

Upgrading a dependency.

```
$ yarn upgrade [package] 
```

Removing a dependency.

```
$ yarn remove [package]
```

Installing all the dependencies.

```
$ yarn install
```

These are some of the commands that you will get accustomed to as we are going to be using them through out the book.

## Node Modules

Behind NodeJS rise to prominence, there is the biggest open source community that creates millions of packages on a daily basis. These packages are the core of building web applications and NodeJS ships with a package manager called NPM. Node Package Manager is very popular in the community however Yarn is now gaining some momentum since it has better security features and also much more faster than NPM. This is the package manager that we are going to use in our book.

## **Project structure**

Setting up a proper structure when developing applications is very important. This is because it will help you organise your code better and make it much more easier for other developers to navigate. We will create the structure of the as we go along with the book. We need to create a file called package.json, this file is used to manage all the packages that we will use in our project, we are going to use a package manager called **Yarn** which is very easy to use. Firstly we need to map out how we are going to organize the structure of our projects. This will help us in terms of navigating the project and adding content to our files since we have a basic understanding of how the projected is organized in this manner.

We are going to create a folder called api, this folder will hold most of the files that relates to the resources for our api's. inside this folder, we will create controllers, this will contain the handlers, which are functions that are called when we hit certain unique resource locators, and routes will contain all the configuration for routes. The helpers will hold all sort of helpers that might be useful while building our api's. The models contains the structure of how our resources should look like. Validators will govern how we will interact with the resource, this will make sure that we only interact with data that we can trust from the client.

The bin folder will basically contain all the files that are used to start and stop the server and other server configurations that we might need. The test folder is going to contain all the files for unit and integration tests. The config file will be used to set all the configuration for the whole project, thus this is where we are going to declare all the variables that we will store inside the environmental variables. The server.js file will be used for creating an instance of the server and authentication settings. The package.json file will be generated by yarn together with the lock file when we add dependencies to the project.

The following skeleton is an overview of how the project is going to be structured.

```
-api
--controllers
---routes
---handlers
--helpers
--models
--validators
-bin
-test
-config.js
-server.js
-yarn.lock
-package.json
```

Open your console and change directory to where you want to keep the code for all your node projects.

Firstly you have to create a folder called LiteCamp by running the following command.

```
$ mkdir  LiteCamp
```

Change directory into the root of the folder that you have just created.

```
$ cd LiteCamp
```

We have to install yarn and use it to initialize a new package.json file. The -g is options which allows us to install packages globally and allows us to use the package when we are anywhere in our machine. Now we are going to install Yarn using NPM which is the default package manager for NodeJS. From thereafter, we will be managing all the external modules using Yarn.

```
$ npm install yarn -g
```

Now  that we have installed Yarn to our machine, we can use it to create our package.json file. Run the following command to initiate the create of the package.json file for our project.

```
$ yarn init
```

Yarn is going to ask you a couple of questions regarding the set up of your package.json file on the command line interface. Please provide the provide the following answers to make sure that we have the same package.json file.

* Name = litecamp.
* Version = 1.0.0.
* Description = A small api about holiday destinations.
* Entry point = server.js.
* Repository url = You can leave this blank for now.
* Author = &lt; Insert your name here &gt;
* License = MIT.

After you have completed the above questions, Press **ENTER **to generate the package.json file.

If you open your package.json file, it should look like this:

```json
{
  "name": "HolidayApp",
  "version": "1.0.0",
  "description": "a small api about holiday destinations",
  "main": "server.js",
  "author": "latani masithi",
  "license": "MIT"
}
```

You have successfully created a package.json file. This all our development and production dependencies, after you install a package to the project, yarn will automatically create a lock file called yarn.lock, this file will be used to maintain the integrety of all the packages that will be used in our project.

### **Summary:**

1. Introduction.
2. NodeJS.
3. Node Modules.
4. Package Managers.
5. Project Structure.
6. Summary.



