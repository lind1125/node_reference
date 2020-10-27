# Creating a Node/Express App

## Initial setup

* Create a new directory
* cd into the directory and input "npm init" to initialize the node project (NOTE: add -y flag to set default values). To set values manually, cycle through using the return key.
```text
npm init -y
```
*  touch entry point (default: index.js)
```text
touch index.js
```

## Node modules

Using modules in Node allows you to write code in one file that can be exported to be used in other files (i.e. imported).

### Creating/exporting a module

* Use `module.exports` in a .js file to create an object to hold any code you would like to be exported. In the example below, dot notation is used to create the code to be exported with this objecxt. In this example, the key-value pair is an anonymous function with a key of `beBasic`.

*EXAMPLE*
```javascript
module.exports.beBasic = () => "That's so fetch!"
```

### Importing a module

* Use the `require()` function to import a module. `require()` takes as an argument the path to the file where you have written the module you wish to export. In this example, the module was written in a file called "myModule.js". You then use dot notation to acess the properties of the module object.

*EXAMPLE*
```javascript
const myModule = require('./myModule.js');

console.log(myModule.beBasic());
```
## Node Packages

Node packages allow for pre-written Node modules to be installed and incorporated into your Node app using __Node Package Manager (NPM)__, an open-source software registry of node packages.

### Installing node packages with NPM
* Node packages can be installed in the terminal using `npm i` or `npm install` followed by the name of the node package. To install a node package globally, use the `-g` flag when running the install command.

*EXAMPLE*
```text
npm i -g nodemon
```
__NOTE: Node packages can be quite large, so you should always add the `node_modules` directory to your [.gitignore file](#creating-a-.gitignore-file)__

---

## Frequently Used Node Packages:
* [nodemon](#nodemon)
* [express](#express)
* [ejs](#ejs)
* [express-ejs-layouts](#ejs-layouts)
* [method-override](#method-override)
* [dotenv](#dotenv)
* [sequelize](#sequelize)

---
## Nodemon
* Nodemon is a package that, when running, will restart the application everytime you save changes to your code. Because of its frequent usefulness, it's best to install globally, as seen in the example above. It is initialized by typing `nodemon` into the terminal. Close an instance of nodemon with `^C`.

## Express
### Setup
* Install the express module into your project from the terminal:
```text
npm i express
```

* inside your entry point (default: index.js) import the express module and create an instance of an express app:

```javascript
const express = require('express')
const app = express()
```

* invoke the app.listen() function and input the port you would like to listen to.

*EXAMPLE*
```javascript
app.listen(8000, ()=>{
    console.log('You\'re listening to the smooth sounds of port 8000')
})
```

### Express routes

A route is a combination of a __URL pattern__ (the string that occurs after the base URL) and __HTTP Verb__. An HTTP verb represents a method for the request to the server. The four main HTTP verbs are:

| VERB   | CRUD    | Example                     |
|--------|---------|-----------------------------|
| GET    | READ    | Display information         |
| POST   | CREATE  | Add data to a database      |
| PUT    | UPDATE  | Edit data in a database     |
| DELETE | DESTROY | Remove data from a database |


---
In the example below, the GET method is invoked for the home route (essentially, a blank URL pattern), and the response object sends 'Hello World' to the front end to ensure that the app is listening correctly.

*EXAMPLE*
```javascript
app.get('/', (req, res)=>{ // see below regarding (req, res)
    res.send('Hello, World!')
})
```

### The Request and Response Object
The Express methods listed pass two arguments into their callback function, the __request object__ and the __response object__. These are conventionally written as `req` and `res`, respectively.

* The request object contains all data about the request made to the server. The most commonly used keys for this object are:

  * `req.body` - any submitted form data will be stored here
  * `req.params` - any route variables will be stored here
    *EXAMPLE*
    ```
    app.get('/:input', (req, res) => {
    console.log("req.params: ", req.params)
    res.send("Our parameter is " + req.params.input + ".");
    })
    ```
  * `req.query` - any query string will be stored here
    *EXAMPLE*
    ```javascript
    app.get("/querystring", (req, res) => {
      let printout = '';
      for (let key in req.query) {
        printout += key + ": " + req.query[key] + "<br />";
      }
      res.send("Here's what they sent: <br /><br />" + printout)
    });
    ```

 
* The response object is what sends data back to the request, i.e. the user's browser. Common functions built into the response object are:

  * `res.send()` - sends back a simple string. Similar to `console.log()`
  * `res.sendFile()` - can send an entire static file (e.g. an html file created in the views directory)
  * `res.render()` - renders data into templates using a [template engine](#ejs)
  * `res.json()` - used to send object data back as JSON



## EJS
  EJS is a template engine that allows you to inject values and script logic into HTML files that we have set up as __views__. Views are files located inside a views directory, which are then accessed by passing their file path into the response object. With EJS, we can inject data from the server to be displayed by the browser.

  ### Setup 
  * first install EJS into your application:
  ```text
  npm i ejs
  ```
  * then set the view engine in the file where your routes are written. Must be placed above the routes.
  ```javascript
  app.set('view engine', 'ejs');

  ```
### Using EJS
Rather than HTML files, the code for what is to be rendered by the browser is written in __.ejs__ files. As long as they are nested in a views folder and have .ejs extensions, you can simply pass the filename (no extension, though it wont break it if you include it) into res.render().

*EXAMPLE*
```javascript
app.get('/', (req, res)=>{
  res.render('index.ejs');
});
```
Note that the .ejs is optional, as express knows to look for ejs files.

### Templating with variables

Using templates allows you to pass objects into `res.render()` and access its values in your ejs templates using ejs notation:

* <% Anything enclosed in these tags will be executed %>
* <%= Anthing enclosed with a `=` will be printed to the browser > 

*EXAMPLE*
```javascript
// this route has an object passed into the res.render function
app.get('/movies', (req, res)=>{
    axios.get(`http://www.omdbapi.com/?apikey=${process.env.API_KEY}&s=${req.query.movieTitle}`)
    .then(response =>{
        let results = response.data.Search
        res.render('results.ejs', {results: results})
    })
```
```HTML
 <!-- The template file with ejs notation  -->
<ul>
    <% results.forEach((movie)=>{ %>
        <li> 
            <a href="/movies/<%= movie.imdbID %>">
            <%= movie.Title %>
            </a> 
        </li>
   <% }) %> 
</ul>
```

## EJS Layouts
EJS layouts is a node package that allows us to create a boilerplate template to include any content that we want to be displayed on every page of our application.

### Setup
* first install `express-ejs-layouts` into your application:
  ```text
  npm i express-ejs-layouts
  ```
* Import the module and invoke the `app.use()` function so that any request object is intercepted by EJS layouts before passing through any route. This kind of code is known as "middleware."

*EXAMPLE*
```javascript
var express = require('express');
var app = express();
var ejsLayouts = require('express-ejs-layouts');

app.set('view engine', 'ejs');
app.use(ejsLayouts);
```
* now create a layout.ejs file in your views folder. __NOTE: It must be called layout.ejs__. Any content coded into other views will be injected into this layout using the `<%- body >` tag:

*EXAMPLE*
```html
<!DOCTYPE html>
<html>
<head>
  <title>Example EJS Layout</title>
</head>
<body>
  <%- body %>
</body>
</html>
```

## method-override

`PUT` and `DELETE` routes are not supported HTML 5, and `method-override` is an established node package to work around that. It looks for a `_method=DELETE` or `_method=PUT` query string in the request URL and swaps out the method accordingly.

### Setup
* Install via npm:
```text
npm i method-override
```
* import and configure your middleware:
```javascript
var methodOverride = require('method-override');

// write this code aobve any other middleware that uses the request method
app.use(methodOverride('_method'));
```

`method-override` is only activated using a `POST` method, so elements for `DELETE` and `PUT` routes should be coded as below:

*EXAMPLE*
```html

<ul>
  <% myDinos.forEach(function(dino, index) { %>
  <li><%= dino.name %> is a <%= dino.type %>
      <form method="POST" action="/dinosaurs/<%= index %>/?_method=DELETE">
          <input type="submit" value="Delete">
      </form>
  </li>
  <% }); %>
</ul>
```

## dotenv (environmental variables)

`dotenv` is a node package that allows you to use __environmental variables__. Environmental variables gives you the ability to create variables stored in a .env file which can then be used throughout your application files.

### Setup
* install dotenv using npm:
```
npm i dotenv
```

* require and configure dotenv:
```javascript
require('dotenv').config()
```

* touch a .env file to your application's root directory and add environmental values in the form of NAME=VALUE. These variables can then be used in your code using the `process.env` dot notation.

*EXAMPLE*
```
PORT = 8000
```
```javascript
app.listen(process.env.PORT, ()=>{
    console.log('PORT 8000!')
})
```

__NOTE: Be sure to add your .env file to your [.gitignore](#creating-a-.gitignore-file) in case any of your variables contain sensitive information.__


## Sequelize

Sequelize is an __ORM (Object-Relational-Mapper)__, which allows you to make SQL queries within your Node app.

### Setup

* If not currently installed, install the sequelize-cli tool:

```text
npm install -g sequelize-cli
```

* Add the sequelize depency to your node app (pg is included to access Postgres)

```text
npm install pg sequelize
```
* Initialize your sequelize project

```text
sequelize init
```

* Update your config.json to match the database you are working in. Also update the dialect to postgres. If no password/username is needed, these can be removed.

*EXAMPLE*
```json
{
  "development": {
    "database": "userapp_development",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "test": {
    "database": "userapp_test",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "production": {
    "database": "userapp_production",
    "host": "127.0.0.1",
    "dialect": "postgres"
  }
}
```
### Creating database/models inside Postgres

* Use the `db:create` command:

*EXAMPLE*
```text
sequelize db:create userapp_development
```

* Create a model using the `model:create` command. Specify the name of your model using `--name`. __*Model names should always be singular*__. After `--name` + the name of your model, input the `--attributes` flag followed by the different data types you would like in your model. __*This should be written as `atrribute:data type,attribute:data type` and so on with no spaces between the commas.*__

*EXAMPLE*
```text
sequelize model:create --name user --attributes firstName:string,lastName:string,age:integer,email:string

```
Any changes to your model should be made before the next step: migration.

* To run the migration, input the following command:

```text
sequelize db:migrate
```

* You can undo the last migration with:
```text
 sequelize db:migrate:undo
 ```
## Creating a .gitignore file

A `.gitignore` file allows you to prevent certain files from bing pushed to remote repos on github. When using express, be sure to touch a .gitignore file in your project directory and write "node_modules" to prevent pushing of the (very large) directory that express creates (as well as any other files you do not want pushed to your repo).