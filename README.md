# Creating a node project

* Create a new directory
* cd into the directory and input "npm init" to initialize the node project
*  touch entry point (default: index.js)


## Adding an express app
* input "npm i express" to install the express module into your project
* inside your entry point (default: index.js) import the express module and create an instance of an express app:

```
const express = require('express')
const app = express()
```

## Creating a .gitignore file

* touch a .gitignore file in your project directory and write in the "node_modules" directory that express creates (as well as any other files you do not want pushed to your repo)

## Writing a a home hello world route using express

* invoke the app.listen() function and input the port you would like to listen to.

*example*
```
app.listen(8000, ()=>{
    console.log('You\'re listening to the smooth sounds of port 8000')
})
```

* invoke the app.get() function and send Hello World to the front end to ensure the app is listening correctly.

*example*
```
app.get('/', (req, res)=>{
    res.send('Hello, World!')
})
```