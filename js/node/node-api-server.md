# Notes on Creating Node-Based API Servers
- Taken largely from Free Code Camp's [tutorial][1] on building a basic API Server on Node

## Setting Up Node Installs
- Make sure that `express`, `mongodb` & `body-parser` are installed as modules
  - `npm install --save express mongodb body-parser`
  - or with yarn...
  - `yarn add express mongodb body-parser`
- Also, a nice extra is to have `nodemon` as a dev-devepndency
  - It's a simple module that automatically restarts the servers on file changes
  - **Not** necessary if already using a dev server of some kind
  - If you do need it, simply install with...
    - `npm install --save nodemon`
    - or with yarn... `yarn add --dev nodemon`
    - Then add a script to `package.json` like below...
`package.json`
```json
"scripts": {
  "dev": "nodemon server.js"
}
```
- **NOTE**: need to figure out if the `src` folder is the correct place to put this
- Next will be to populate `./server.js` with the correct dependencies    - express
    - MongoClient
    - bodyParser
`./server.js`
```js
const express = require('express');
const MongoClient = require('mongodb').MongoClient;
const bodyParser = require('body-parser');

const app = express();
```
- Then to quickly test that everything just done works, enter the below snippet of code into `./server.js`
`./server.js`
```js
const port = 8000;

app.listen(port, () => {
  console.log('API Server listening to port ', port);
});
```
- Now the terminal should end the starting of the server with a quick console message about listening on port 8000


## The First CRUD Route
- Start by creating the first part of **CRUD** **C**reate
- In express routes to the API server are created as a function that takes the express instance from before and a database as arguments
`routes/note_routes.js`
```js
// routes/note_routes.js
module.exports = function(app, db) {
// route actions
}
```
- Then create the `index.js` route file to export `note_routes` altogether through the server

`routes/index.js`
``` routes
const noteRoutes = require('./note_routes');

module.exports = function(app, db) {
  noteRoutes(app, db);
  // Other route groups can go here
};
```
- Then import it for use in...
`server.js`
```js
const express = require('express');
const MongoClient = require('mongodb').MongoClient;
const bodyParser = require('body-parser');

const app = express();

const port = 8000;

require('./src/routes')(app, {});

app.listen(port, () => {
  console.log('API Server listening to port ', port);
});
```
- Since the database isn't set up yet, only empty objects are being passed around
- To create, the **create** route, just use this syntax:
`routes/note_routes.js`
```js
// note_routes.js
module.exports = function(app, db) {
  app.post('/notes', (req, res) => {
    // You'll create your note here.
    res.send('Hello')
  });
};
```
- Now whenever the server receives a *post* request to the `/notes` path, it will execute the code inside the callback-passing in a request object
- Then a response is given, which in this case is just a *hello*
- Test out the functionality with your favorite http utility
  - in **HTTPie** just use: `http POST localhost:8000/notes`

## Request Parameters
- In most HTTP request utilities, there exists an option to add key-value pairs to the request
- This will enable easy testing of the API server by letting tests be run with different key value pairs for things like a notes **title** and **body** as keys
- In `note_routes`, log out the body that gets sent in a request
`note_routes.js`
```js
module.exports = function(app, db) {
  app.post('/notes', (req, res) => {
    app.post('/notes', (req, res) => {
      console.log(req.body)
      res.send('Hello')
    });
  };
}
```
- For example, using **HTTPie**
  - `http -f POST localhost:8000/notes body=hello`
- This will initially give an `undefined` response on the node server
- Express can't process URL encoded forms on its own, that's why `body-parser` was installed
- In `server.js` add this line:
  - `app.use(bodyParser.urlencoded({ extended: true }));`
- Now when sending test requests that include form entries, should appear in the terminal of the node server
- 

#References
[1]: https://medium.freecodecamp.org/building-a-simple-node-js-api-in-under-30-minutes-a07ea9e390d2 "FreeCodeCamp: Basic NodeJS API"
[2]: https://daveceddia.com/create-react-app-express-backend/ "Create-React-App with Express Backend"
[3]: https://httpie.org/doc#examples "HTTPie: Documentation Examples"

1. [Free Code Camp: Quick Tutorial on creating a basic node-based API server][1]
2. [Tutorial to add Express Backend to Create React App][2]
3. [HTTPie Docs: Exmamples][3]
