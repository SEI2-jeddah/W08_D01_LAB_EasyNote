# EasyNotes

## RESTful CRUD (Create, Retrieve, Update, Delete) API with Node.js, Express and MongoDB/Mongoose
*This lab is based on [callicoder](https://www.callicoder.com/)*

__________
1. We will be building a simple Note-Taking application with Rest APIs for creating, listing, editing and deleting a Note.

1. We’ll start by building a simple web server and then move on to configuring the database, building the Note model and different routes for handling all the CRUD operations.

1. Finally, we’ll test our REST APIs using Postman.

<br> 

### Creating the Application
#### 1. Fire up your terminal and create a new folder for the application.

```bash
$ mkdir node-easy-notes-app
```

#### 2. Initialize the application with a package.json file

Go to the root folder of your application and type npm init to initialize your app with a package.json file.

```bash
$ cd node-easy-notes-app
$ npm init
```
<br> 

```bash
name: (node-easy-notes-app) 
version: (1.0.0) 
description: Never miss a thing in Life. Take notes quickly. Organize and keep track of all your notes.
entry point: (index.js) server.js
test command: 
git repository: 
keywords: Express RestAPI MongoDB Mongoose Notes
author: callicoder
license: (ISC) MIT
About to write to /Users/rajeevkumarsingh/node-easy-notes-app/package.json:

{
  "name": "node-easy-notes-app",
  "version": "1.0.0",
  "description": "Never miss a thing in Life. Take notes quickly. Organize and keep track of all your notes.",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "Express",
    "RestAPI",
    "MongoDB",
    "Mongoose",
    "Notes"
  ],
  "author": "callicoder",
  "license": "MIT"
}

Is this ok? (yes) yes

```
Note that I’ve specified a file named server.js as the entry point of our application. We’ll create server.js file in the next section.
<br> 
<br> 

#### 3. Install dependencies

We will need express, mongoose modules in our application. Let’s install them by typing the following command -
```bash
$ npm install express mongoose --save
```

I’ve used --save option to save all the dependencies in the package.json file. The final package.json file looks like this -

```bash
{
  "name": "node-easy-notes-app",
  "version": "1.0.0",
  "description": "Never miss a thing in Life. Take notes quickly. Organize and keep track of all your notes.",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "Express",
    "RestAPI",
    "MongoDB",
    "Mongoose",
    "Notes"
  ],
  "author": "callicoder",
  "license": "MIT",
  "dependencies": {
    "body-parser": "^1.18.3",
    "express": "^4.16.3",
    "mongoose": "^5.2.8"
  }
}
```

Our application folder now has a package.json file and a node_modules folder -
```bash
node-easy-notes-app
    └── node_modules/
    └── package.json
 ```
 <br> 

### Setting up the web server
<br> 

Let’s now create the main entry point of our application. Create a new file named server.js in the root folder of the application with the following contents -

 ```bash
 const express = require('express');

// create express app
const app = express();

// parse requests of content-type - application/x-www-form-urlencoded
app.use(express.urlencoded({ extended: false }));

// parse requests of content-type - application/json
app.use(express.json())

// define a simple route
app.get('/', (req, res) => {
    res.json({"message": "Welcome to EasyNotes application. Take notes quickly. Organize and keep track of all your notes."});
});

// listen for requests
app.listen(3000, () => {
    console.log("Server is listening on port 3000");
});
```
 <br> 

First, We import express module. [Express](https://www.npmjs.com/package/express), as you know, is a web framework that we’ll be using for building the REST APIs.
Then, We create an express app, and add two parsing middlewares using express’s ```app.use()``` method. A middleware is a function that has access to the request and response objects. It can execute any code, transform the request object, or return a response.

Then, We define a simple GET route which returns a welcome message to the clients.

Finally, We listen on port 3000 for incoming connections.
<br> 
<br> 

#### All right! Let’s now run the server and go to http://localhost:3000 to access the route we just defined.

```bash
$ node server.js 
Server is listening on port 3000
```
![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-api-tutorial.jpg)
<br> 

### Configuring and Connecting to the database
I like to keep all the configurations for the app in a separate folder. Let’s create a new folder config in the root folder of our application for keeping all the configurations -
```bash
$ mkdir config
$ cd config
```
Now, Create a new file database.config.js inside config folder

*note* create youre own collection in MongoDB first and then link it as appropriate with the correct name

Ex.
```bash
module.exports = {
    url: 'mongodb://localhost:27017/easy-notes'
}
```
We’ll now import the above database configuration in server.js and connect to the database using mongoose.

Add the following code to the server.js file after app.use(express.json()) line -
```bash
// Configuring the database
const dbConfig = require('./config/database.config.js');
const mongoose = require('mongoose');

mongoose.Promise = global.Promise;

// Connecting to the database
mongoose.connect(dbConfig.url, {
    useNewUrlParser: true
}).then(() => {
    console.log("Successfully connected to the database");    
}).catch(err => {
    console.log('Could not connect to the database. Exiting now...', err);
    process.exit();
});
```
Please run the server and make sure that you’re able to connect to the database -
```bash
$ node server.js 
Server is listening on port 3000
Successfully connected to the database
```

____________________

### Defining the Note model in Mongoose

next, We will define the Note model. Create a new folder called app inside the root folder of the application, then create another folder called models inside the app folder -
```bash
$ mkdir -p app/models
$ cd app/models
```
Now, create a file called note.model.js inside app/models folder with the following contents -
```bash
const mongoose = require('mongoose');

const NoteSchema = mongoose.Schema({
    title: String,
    content: String
}, {
    timestamps: true
});
```
The Note model is very simple. It contains a title and a content field. I have also added a timestamps option to the schema.

Mongoose uses this option to automatically add two new fields - createdAt and updatedAt to the schema.

<br>

*Before we get started , check out the documentation of all the methods you can use on Mongoose’s official documentation*

- [Mongoose save()](https://mongoosejs.com/docs/api.html#document_Document-save)
- [Mongoose find()](https://mongoosejs.com/docs/api.html#find_find)
- [Mongoose findById()](https://mongoosejs.com/docs/api.html#findbyid_findById)
- [Mongoose findByIdAndUpdate()](https://mongoosejs.com/docs/api.html#findbyidandupdate_findByIdAndUpdate)
- [Mongoose findByIdAndRemove()](https://mongoosejs.com/docs/api.html#findbyidandremove_findByIdAndRemove)

<br>

### Defining Routes using Express

Next up is the routes for the Notes APIs. Create a new folder called routes inside the app folder.
```bash
$ mkdir app/routes
$ cd app/routes

module.exports = mongoose.model('Note', NoteSchema);
```
Now, create a new file called note.routes.js inside app/routes folder with the following contents 

```bash
module.exports = (app) => {
    const notes = require('../controllers/note.controller.js');

    // Create a new Note
    app.post('/notes', notes.create);

    // Retrieve all Notes
    app.get('/notes', notes.findAll);

    // Retrieve a single Note with noteId
    app.get('/notes/:noteId', notes.findOne);

    // Update a Note with noteId
    app.put('/notes/:noteId', notes.update);

    // Delete a Note with noteId
    app.delete('/notes/:noteId', notes.delete);
}
```
Note that We have added a require statement for note.controller.js file. We’ll define the controller file in the next section. The controller will contain methods for handling all the CRUD operations.

Before defining the controller, let’s first include the routes in server.js. Add the following require statement before app.listen() line inside server.js file.
```bash
// ........

// Require Notes routes
require('./app/routes/note.routes.js')(app);

// ........
```

If you run the server now, you’ll get the following error -
```bash
$ node server.js
module.js:472
    throw err;
    ^

Error: Cannot find module '../controllers/note.controller.js'
```
This is because we haven’t defined the controller yet. Let’s do that now.

### Writing the Controller functions
Create a new folder called controllers inside the app folder, then create a new file called note.controller.js inside app/controllers folder with the following contents -

```bash
const Note = require('../models/note.model.js');

// Create and Save a new Note
exports.create = (req, res) => {

};

// Retrieve and return all notes from the database.
exports.findAll = (req, res) => {

};

// Find a single note with a noteId
exports.findOne = (req, res) => {

};

// Update a note identified by the noteId in the request
exports.update = (req, res) => {

};

// Delete a note with the specified noteId in the request
exports.delete = (req, res) => {

};
```


Let’s now look at the implementation of the above controller functions one by one -
#### Creating a new Note
##### Task
1. create a new note
1. Save note in the database 
1. Handle erorrs of wrong note creation requests 
<details>
  <summary>Hint</summary>

```bash
/ Create and Save a new Note
exports.create = (req, res) => {
    // Validate request
    if(!req.body.content) {
        return res.status(400).send({
            message: "Note content can not be empty"
        });
    }

    // Create a Note
    const note = new Note({
        title: req.body.title || "Untitled Note", 
        content: req.body.content
    });

    // Save Note in the database
    note.save()
    .then(data => {
        res.send(data);
    }).catch(err => {
        res.status(500).send({
            message: err.message || "Some error occurred while creating the Note."
        });
    });
};
```
</details>


##### Creating a new Note using POST /notes API
Testing our APIs using postman
![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-crud-api-create-a-note.jpg)
<br>

#### Retrieving all Notes
##### Task 
1. Retrieve and return all notes from the database 
1. send an error response when notes are not found 
<details>
  <summary>Hint</summary>
  
```bash
// Retrieve and return all notes from the database.
exports.findAll = (req, res) => {
    Note.find()
    .then(notes => {
        res.send(notes);
    }).catch(err => {
        res.status(500).send({
            message: err.message || "Some error occurred while retrieving notes."
        });
    });
```
</details>


##### Retrieving all Notes using GET /notes API

![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-crud-api-retrieve-all-notes.jpg)
<br>

#### Retrieving a single Note
##### Task 
1. Retrieve and return a single note from the database 
1. send an error response when note is not found 

<details>
  <summary>Hint</summary>
  
  ```bash

// Find a single note with a noteId
exports.findOne = (req, res) => {
    Note.findById(req.params.noteId)
    .then(note => {
        if(!note) {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });            
        }
        res.send(note);
    }).catch(err => {
        if(err.kind === 'ObjectId') {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });                
        }
        return res.status(500).send({
            message: "Error retrieving note with id " + req.params.noteId
        });
    });
};
```
</details> 


##### Retrieving a single Note using GET /notes/:noteId API
![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-crud-api-retrieve-a-single-note.jpg)

<br>

#### Updating a Note
##### Task 
1. Find note and update it with the request body
1. Valdiate note update request 
1. send an error response when requested note is not found
1. send an error response when updating a note has failed

<details>
  <summary>Hint</summary>
  
```bash
// Update a note identified by the noteId in the request
exports.update = (req, res) => {
    // Validate Request
    if(!req.body.content) {
        return res.status(400).send({
            message: "Note content can not be empty"
        });
    }

    // Find note and update it with the request body
    Note.findByIdAndUpdate(req.params.noteId, {
        title: req.body.title || "Untitled Note",
        content: req.body.content
    }, {new: true})
    .then(note => {
        if(!note) {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });
        }
        res.send(note);
    }).catch(err => {
        if(err.kind === 'ObjectId') {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });                
        }
        return res.status(500).send({
            message: "Error updating note with id " + req.params.noteId
        });
    });
};
```
The {new: true} option in the findByIdAndUpdate() method is used to return the modified document to the then() function instead of the original.
</details>

##### Updating a Note using PUT /notes/:noteId API
![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-crud-api-update-a-note.jpg)

<br>

#### Deleting a Note
##### Task 
1. Delete a note with the specified noteId in the request 
1. send an error response when requested note is not found
1. send sucess response when note has been deleted successfully
1. send an error response when deleting a note has failed


<details>
  <summary>Hint</summary>

```bash
// Delete a note with the specified noteId in the request
exports.delete = (req, res) => {
    Note.findByIdAndRemove(req.params.noteId)
    .then(note => {
        if(!note) {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });
        }
        res.send({message: "Note deleted successfully!"});
    }).catch(err => {
        if(err.kind === 'ObjectId' || err.name === 'NotFound') {
            return res.status(404).send({
                message: "Note not found with id " + req.params.noteId
            });                
        }
        return res.status(500).send({
            message: "Could not delete note with id " + req.params.noteId
        });
    });
    };
```
</details>


##### Deleting a Note using DELETE /notes/:noteId API
![](https://www.callicoder.com/assets/images/post/large/node-express-mongoose-rest-crud-api-delete-a-note.jpg)




