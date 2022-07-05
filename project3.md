# MERN STACK IMPLEMENTATION

## SIMPLE TO-DO APPLICATION ON MERN WEB STACK

===============

In this project, I implemented a web solution based on MERN stack in AWS Cloud.

MERN Web stack consists of following components:

1. **MongoDB:** A document-based, No-SQL database used to store application data in a form of documents.
2. **ExpressJS:** A server side Web Application framework for Node.js.
3. **ReactJS:** A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
4. **Node.js:** A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

## STEP 1: BACKEND CONFIGURATION

===============

Update ubuntu

```
sudo apt update
```

Upgrade ubuntu

```
sudo apt upgrade
```

Get the location of Node.js software from Ubuntu repositories.

```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

![Bash](./media/sudobash.png)


Install Node.js with the command below:

```
sudo apt-get install -y nodejs
```

![Node installation](./media/nodejsint.png)


The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

Verify the node installation with the command below:

```
node -v 
```

Verify the node installation with the command below:

```
npm -v 
```

Create a new directory for your To-Do project

```
mkdir Todo
```

![Make dir](./media/tododir.png)


Change your current directory to the newly created one:

```
cd Todo
```

Next, you will use the command `npm init` to initialise your project, so that a new file named `package.json` will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the `package.json` file by typing `yes`.

```
npm init
```

Run the command ls to confirm that you have `package.json` file created.

Next, we will Install `ExpressJs` and create the `Routes` directory.

## STEP 1.1: INSTALL EXPRESSJS

===============


Remember that Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore it simplifies development, and abstracts a lot of low level details. For example, Express helps to define routes of your application based on HTTP methods and URLs.

To use express, install it using npm:

```
npm install express
```

Now create a file `index.js` with the command below:

```
touch index.js
```

Run ls to confirm that your index.js file is successfully created

Install the dotenv module:

```
npm install dotenv
```

Open the index.js file with the command below:

```
vim index.js
```

Type the code below into it and save. 

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

We had specified to use port 5000 in the code. This will be required later when we go on the browser.

Create an inbound rule to open TCP port 5000: 

![Inbound port](./media/inboundsg.png)


Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:

```
node index.js
```

![Node up](./media/nodeserv.png)


If every thing goes well, you should see Server running on port 5000 in your terminal.

![Express up](./media/expresswel.png)


### CREATE ROUTES


There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task


Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes:

```
mkdir routes
```

Change directory to routes folder:

```
cd routes
```

Now, create a file `api.js` with the command below:

```
touch api.js
```

Open the file with the command below:

```
vim api.js
```

Copy below code into the file:

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

Next we create `Models` directory.

## STEP 1.2: MODELS

===============

Now comes the interesting part, since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.

We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document. (Seems like a lot of information, but not to worry, everything will become clear to you over time. I promise!!!)

In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back `Todo` folder and install Mongoose:

```
npm install mongoose
```

Create a new folder `models`:

```
mkdir models
```

Change directory into the newly created ‘models’ folder with:

```
cd models
```

Inside the models folder, create a file and name it todo.js:

```
touch todo.js
```

Open the file created folder then paste the code below in the file:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

Now we need to update our routes from the file `api.js` in `routes` directory to make use of the new model:

```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```

## STEP 1.3: MONGODB DATABASE

===============

We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case.


![MLab Dashboard](./media/mlabdash.png)


Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing).

**IMPORTANT NOTE**

In the image below, make sure you change the time of deleting the entry from 6 Hours to 1 Week.

![MongoDB access](./media/mongoaccess.png)

Create a MongoDB database and collection inside mLab.

![MongoDB create](./media/mongocreate2.png)


In the `index.js` file, we specified `process.env` to access environment variables, but we have not yet created this file. So we need to do that now.

Create a file in your Todo directory and name it `.env`.

```
touch .env
vi .env
```

Add the connection string to access the database in it, just as below:

```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```

Ensure to update `<username>`, `<password>`, `<network-address>` and `<database>` according to your setup.

Here is how to get your connection string:

![MongoDB connect](./media/mongoconn.png)

![MongoDB connect](./media/mongoconn2.png)


Now we need to update the `index.js` to reflect the use of `.env` so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below:

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the `index.js` application file.


Start your server using the command:

```
node index.js
```

You shall see a message ‘Database connected successfully’, if so – we have our backend configured. Now we are going to test it.


![MongoDB up](./media/nodeup.png)


