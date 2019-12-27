Node Development Environment Notes
==================================

Install Packages
----------------

First since newer javascript features are nice to work with and so is compatibility, let's get some babel packages.

```sh
npm install  npm install --save-dev @babel/core @babel/cli @babel/preset-env @babel/node
```

And since babel will be constantly transpiling newer javascript with older versions, it's nice to have a utility `nodemon` that will restart the node server being used during development with the updated code. So install `nodemon` as a developer dependency.

```sh
npm install --save-dev nodemon
```

Next create a `.babelrc` file in the project root to configure how babel will transpile javascript. Later it might be desirable to use more specific features of babel, but their `preset-env` that was downloaded is plenty good enough to start with. Specify the below JSON into `.babelrc`.

```json
{
  "presets": [
    "@babel/preset-env"
  ]
}
```

Package.json Scripts
--------------------

Now to make life a little easier setup some `package.json` scripts so the different actions needed to develop, build and test the project are just a single npm command away.

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "nodemon --exec babel-node index.js ",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git"
  },
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/sccofield/my-app/issues"
  },
  "dependencies": {
  },
  "devDependencies": {
    "@babel/cli": "^7.1.5",
    "@babel/core": "^7.1.6",
    "@babel/node": "^7.0.0",
    "@babel/preset-env": "^7.1.6",
    "nodemon": "^1.18.6"
  }
}
```

Namely the `start` script is needed to be added like above. All it is, is specifying where `babel-node` should run the babel-compatible version of node will run its entrypoint file `index.js` 

Now all that's needed to start development is to type `npm run start`.

Setup Jest to Test
------------------

Now if test driven development is important to you, this isn't enough to just get started, a test runner like jest is necessary. **REWORD & LINK TO JEST** Start by installing jest through npm.

```sh
npm install --save-dev jest
```

Just like before, add a script named `test` in this case, to replace the placeholder one from before of the same name in `package.json`.

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

This will then use jest to look for all tests within the project root, typically with the default rule anything with `test` in the filename and has extension `js`.
