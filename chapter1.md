# Chapter 1

In this chapter, we are going to cover the following topics:

* Installation.
* Introduction command line.
* Introduction to NodeJs.
* Introduction to MongoDB.

## **Introduction**

This chapter covers all the fundemental about NodeJS ecosystem and all the necessary setup that will come in handy when we start building our api in the later chapters. You need to understand a couple of core concepts in general about writing code and also how  We need to install a couple of applications and also set it to be able to follow up with the book and examples inside this book.

### Installation

**Code editors**

You need to download a code editor to edit your code. I recommend that you download Atom or Code VS which are two of my favorite editors. You can download them from the following links depending on your operating system.

1.1 Linux -&gt; [https://code.visualstudio.com/](https://code.visualstudio.com/) and [https://atom.io/](https://atom.io/)

1.2 Windows -&gt; [https://code.visualstudio.com/](https://code.visualstudio.com/) and [https://atom.io/](https://atom.io/)

1.3 Mac OS -&gt; [https://code.visualstudio.com/](https://code.visualstudio.com/) and [https://atom.io/](https://atom.io/)

**NodeJS**

You also need to download NodeJs platform so that you will be able to run your javascript code locally on your machine. You can go to the links that are provided next to the distribution of your operating system.

2.1 Linux -&gt; [https://www.nodejs.org/](https://www.nodejs.org/)

2.2 Windows -&gt; [https://www.nodejs.org/](https://www.nodejs.org/)

2.3 Mac OS -&gt; [https://www.nodejs.org/](https://www.nodejs.org/)

To check if you have download node successfully, You need to run the following command, if the installation was successful, the console should would have printed the version of Node JS installed in your machine:

```bash
$ node -v

v 8.9.1
```

**MongoDB**

We are going to use a NOSQL database when creating our api, so you also need to have it install in your machine. You can do so in the links provided. Head over to the MongoDB website and download that is applicable for your operating system and download it.

3.1 Linux -&gt; [https://www.mongo.com/](https://www.mongo.com/)

3.2 Windows -&gt; [https://www.mongo.com/](https://www.mongo.com/)

3.3 Mac OS -&gt; [https://www.mongo.com/](https://www.mongo.com/)

You can run the following command to see if it the database was successfully downloaded to your machine. It should print the version of your MongoDB. Finally you are now all setup to get started with web development using javascript in both the client and the server.

```
$ mongo -v

v 3.4
```

## **Introduction to NodeJS**

NodeJS runs on v8 engine by chrome, it allows us to write javascript code that can run outside the browser. This means that you can write servers, scripts and other things using JavaScript. As the saying goes, whatever can be written in JavaScript, will end up written in Javascript.NodeJS engine uses a single threaded architecture, this mean that it can only run a single process at a time. It has event loop that queues' all the events so that they can be processed one by one. The advantage of this is that you can continue interacting with your application since these events are non-blocking as the program. This means that your most of your I/O, database operations and network requests, you will need to provide a callback that will handle the result when the application finally returns the result from these operations.

The non-blocking archicture back in the days gave NodeJS a bad rep bacause it would always lead to call back hell when you are handling asynchronous code, this has was improved by the introduction of promises which made it easy to write readable code that can be chained together. A promise can resolve successfully or reject if something doesn't go well, it can also be pending if it hasn't returned the results yet. You could easily call a promise and use then to handle the results and catch to handle the errors that might occur when calling the promise.

Behind NodeJS rise to prominence, there is the biggest open source community that creates millions of packages on a daily basis. These packages are the core of building web applications and NodeJS ships with a package manager called NPM. Node Package Manager is very popular in the community however Yarn is now gaining some momentum since it has better security features and also much more faster than NPM. This is the package manager that we are going to use in our book.

## Introduction to command line

We are going to be using the command line a lot when developing our api, so you need to at least know some basics of how to use the it to do some basic commands to create folders, files and installing packages, etc.

### **Project structure**

Setting up a proper structure when developing applications is very important. This is because it will help you organise your code better and make it much more easier for other developers to navigate. We will create the structure of the as we go along with the book. We need to create a file called package.json, this file is used to manage all the packages that we will use in our project, we are going to use a package manager called **Yarn** which is very easy to use and realiable.

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

## **Introduction to MongoDB**

MongoDB is a NOSQL database that is very popular in the ecosystem. This database makes it very easy to store information in it, it is also schema less, which means that you have to becare when it comes to the data that is sent to the database. Validation all the data that comes from the client is quite critical and there are a lot of package that we can use to handle such tasks. Let's get started by creating the structure of our project.

### **Summary:**

1. Installation.
2. ~~Introduction command line.~~
3. Introduction to NodeJs.
4. Introduction to MongoDB.



