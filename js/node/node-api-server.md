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
`./routes/note_routes.js`
```js
// routes/note_routes.js
module.exports = function(app, db) {
// route actions
}
```
- Then export the function through the project's `index.js`, `bundle.js` or whatever JS entry point exists for the project
``` routes
```

## References
[1]: https://medium.freecodecamp.org/building-a-simple-node-js-api-in-under-30-minutes-a07ea9e390d2 "FreeCodeCamp: Basic NodeJS API"
[2]: https://daveceddia.com/create-react-app-express-backend/ "Create-React-App with Express Backend"

1. [Free Code Camp: Quick Tutorial on creating a basic node-based API server][1]
2. [Tutorial to add Express Backend to Create React App][2]
