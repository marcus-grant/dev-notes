# Using Meteor in React App

## Foreward
- These are unorganized notes, and they should remain as such
- Edit notes for publishing later

## Install
- Go to [meteor site](https://www.meteor.com/install)
- For Unix based OS, run in terminal `curl https://install.meteor.com/ | sh`
- Figure out better way to store `~/.meteor`

## What is Meteor & Why Use It?
- First off, the best resource for meteor info is their [guide](https://guide.meteor.com/)
- From there:
> Meteor is a full-stack JavaScript platform for developing modern web and mobile applications. Meteor includes a key set of technologies for building connected-client reactive applications, a build tool, and a curated set of packages from the Node.js and general JavaScript community.

- So fullstack implies that both the client and server are handled
- Node.JS is the backend
- Meteor actually handles node itself and **all its dependencies**
- Meteor can be used in any platform that has a server-client platform, including web, desktop, and mobile
- Handles 2-way communication
- Provides an integrated build tool
- Gives 3rd party libraries that are most useful that are curated from npm
  - Any 3rd party package can be browsed on [npm](https://www.npmjs.com)
- [Atmosphere](https://atmospherejs.com) is another great place to find **meteor** specific packages
  - **Please prioritize npm packages**
- [MongoDB](https://www.mongodb.com) is the database most often used in this stack
- **Web Sockets** connect real time data to/from server/client
- Any JS based render can be used, here React is used

## Starting A Meteor Project
- `meteor create hello-meteor` typed in the folder that is to contain the project folder of the new meteor project, starts the script that generates a meteor project
  - It creates a folder of name `PROJECT_NAME` and puts a lot of the boilerplate npm & webpack babel stuff into that new folder
- `meteor npm install` runs `npm install` from within the meteor environment
  - It installs all packages that are listed in `packages.json` into `/node_modules/`
- `meteor run` is the default run script that starts the npm server within the meteor environment
  - Behind the scenes, it's just `npm start`, but it's handled through meteor, so the default script within `packages.json` is: `"start": "meteor run"`
  - Going to [http://localhost:3000/](http://localhost:3000/) will show the default web-page provided by meteor

### The Meteor Directory Structure
- Every meteor project has a `.meteor` folder which meteor uses to track the project and its meta data like npm version, etc.
- `client` folder, stores all the assets required to transpile a site
  - `main.css` is generated but empty, and it contains the main entry point to handle CSS styles for the entire app, but other files can be referred to by it
  - `main.html` is the main entrypoing for html being used by meteor, this is where everything gets framed as a webpage before it is transpiled, minimized and served by meteor
  - `main.js` stores a lot of blaze related stuff, which should be removed if another client side javascript library like React is to be used, then this needs to be deleted
- Meteor also runs a node server log that can be used after `meteor run` to debug app

## Modifying the Default Meteor App
- In ~/code/web/score-keep is the first meteor app
- Remember the steps to create a, and run a meteor app:
  1. `meteor create PROJECT_NAME`
    -  For the sake of compatibility for this project's time, use 1.4.2.1:
      - `meteor create --release 1.4.2 score-keep`
  2. `cd PROJECT_NAME`
  3. `meteor npm install`
  4. `meteor run`
- Now it should be possible to start tearing down the meteor project Directory
  - **NEVER** delete or modify directly, the `./.meteor` directory
- The 4 boilerplate website files that meteor generates, are the ones that should be modified (removed?) to change the behavior of the app
  - `./client/main.html`
  - `./client/main.css`
  - `./client/main.js`
  - `./server/main.js`

*./server/main.js *
- For this project, delete its contents completely
- To track the order and lifetime of file runs, use `console.log()` as the first line in this new file in `/server/main.js`
- When saved, meteor detects the file change, and will rerun the server with that logging command in place

* ./client/main.js *
- Do the same delete and log command for the `client/main.js` file js file
  - **However** because this `main.js` file is in the `client` folder, these changes will only take place in the browser, so no prompt will be visible in the node server prompt
  - Go into the developer console in the browser to see this log

- Meteor loads in functionality using either **eager loading** or **lazy loading**
  - Lazy loading is the primary method

- More on loading later, but for now, it's necessary to know that it is **not recommended** to add files to the client and server folders for new functionality
- Instead, for now, add a `app.js` file at the root of the project and add a unique `console.log()` statement inside `app.js` inorder to see how the order of thinigs play out
  - Both the client and the server side will log the event
  - Both the client and server, will also call out the `app.js` log event before the others
- This timing issue where files outside these folders are used are an antipattern for this reason, it's hard to handle the dependencies and race conditions that arise
- So the project directory structure then becomes important to maintain
  - `client` only for client side files
  - `server` only for the server
- Delete the `app.js` file it's only a demonstration of what **NOT** to do
- There are other directory names that can optionally be used that have important meanings for the project
- One of these is `./imports`
  - **Lazy Loading** requires that you be able to manually import certain files
  - These files go inside `imports`
  - Create an `import` folder at the root of the project, and include the same `app.js` that was just deleted with a console prompt.
  - These lazy loaded imports aren't then automatically loaded into the application either on the client or server side
- Next is the `./public` folder
  - Anything that is placed here will be a publically available asset that is available to either the server or client
  - Make a `./public/help.html` file with a simple message in it
  - Because that asset is never referenced in any of the other files, it isn't visible anywhere after meteor restarts the server, however, going to [http://localhost:3000/help.html](http://localhost:3000/help.html) will reveal the help page since it's publically available
  - `./public` will typically get used for static web pages, images, fonts, svg's etc
- Client will never have any extra files, it is the location of the client side application
  - Where then?
  - The `./imports/` directory will hold the main brunt of javascript assets
- If the inspector is used on any of the current meteor pages, there will be a head link for a css that is automaitcally generated by meteor
- The same is done behind the scenes for the javascript that gets used
- `main.html` shold be noted, doesn't have any script tags like most js based webpages have
  - this is because meteor handles all this stuff behind the scenes before serving up the final page, allowing the dev to focus on the real part of the work
- For the purposes of this app, let's strip the `main.html` file of everything except for this below:
```html
<head>
  <title>Score Kepp</title>
</head>

<body>
  <h1>Score Keep App</h1>
</body>
```
- This provides a good frame to build the rest of the app later

## Imports
- **Eager Loading** is an anti-patter, so it isn't going to be used, instead **lazy loading** is going to be used
- This means that when big new files for the javascript portion of this app, it will be placed in the `imports` part of the project Structure
- Without creating a really massive set of the 4 `main` files, these imports will be needed to add functionality without messing with the `server` & `client` directory Structure
- The new files, like react components for example, will **export** assets for the main files to later **import** as part of their core functionality
- There will be two functions that will be exported to the `server/main.js`:
  - `greetUser`
  - `getFirstName`
  - These will be imported from from `imports/utils.js`
- Now, delete the `imports/app.js`
- Add `imports/utils.js`
  - For now, just create the log to console statement with indicator for which file called it
  - Again, on save, meteor reloads, but nothing new happens
  - the `main`s need to import this file first
- ontop of `server/main.js` add, `import '../imports/utils.js'
  - this will look back one directory, then inside imports, and include utils.js
  - Now, on save, there will be an output log for `import/utils.js`
  - This is now **lazy loading** because the app is specifically being told when to include other parts of the codebase
- Do the same import statement for `client/main.js`
  - First line of `client/main.js`: `import '../imports/utils';`
  - '..' signifies a path behind the current folder
  - imports should be at the top\
  - imports don't need the file extensions in meteor
- Now with these imports in place, it's possible to see the console log from the utils file
- From the `imports/utils.js` file, if specific parts of a file needs to be accessed, say a function: `getUser`, the **export** keyword will be needed to specify to the javascript interpreter that something is exportable
- Any export needs to have an import in another file in order to make use of it
- So in, `imports/utils.js`, add:
```js
export let greetUser = function () {
  return 'Hello Guy'
}
```
- This will then make the function `greetUser` available to any file that imports this file, and specifies either the entire file or just this one export within it
- To show how specific parts of an import should be accessed, in `server/main.js`:
```js
import {greetUser} from './../imports/utils'
```
  - replace the previous import with the line above, then add below:
```js
console.log(greetUser());
```
  - This will now make `greetUser()` available in the `server/main` file and the server should now log out the message 'Hello Guy'
  - The `{greetUser}` specifies that **only** that part of `imports/utils` should be imported
  - Other exports can be imported, so long as within the curly braces they are included in the import statement, seperated by commas
- There is a 2nd type of export, **default**
- **default** as a keyword describes an export that can be named during the import statement wherever it is imported
    - placing this into `utils`
    `export default 'Default val';`
    - then this into `cllient/main`
```js
import someDefault,{greetUser, name} from './../imports/utils';
//...
export default 'Default val';
//...
`console.log(someDefault)`
```
  - This will import a default variable as `someDefault`
  - It will also log out the default statement *Default val*
- **default** s are primarily used to specify the intended most likely import from something to simplify import statements wherever the js file is intended
- The default only needs the `export default` statement, and what it equates to, no name is necessary or `var` or `let` statements

## React
### Installing React & Using JSX
- Here's a good time to try using [npmjs](https://www.npmjs.com/package/react) to test out hunting down npm packages
  - [React entry](https://www.npmjs.com/package/react) on nmpjs.com
- `react` & `react-dom` are required in order to render react to a browser
  - `react` handles the actual react libraries
  - `react-dom` is to render these things to a virtual DOM on a browser
- running `npm install` by itself won't work because vanilla npm hasn't been installed when creating the meteor environment
- Instead, `meteor npm` gets used
- To install, `meteor npm install react@15.3.2 react-dom@15.3.2` will install these packages at the specified version numbers
  - This now installs these packages to the `node_modules` folder
  - However, this doesn't modify `packages.json` so installing on another machine or environment won't be able to suss out which packages to install
- To specify that these dependencies should be persisted two methods are available
  - Specify the dependencies manually within the `packages.json`
  - use `--save` with `meteor npm install` and it will automatically handle the above
- Wipe `client/main.js` & `client/main.html`
- Enter this into `client/main.html`
```html
<head>
  <title>Score Keep</title>
</head>

<body>
  <div id="react-root"></div>
</body>
```
  - This is the standard minimal boiler plate for a react app
  - There must be a single `<div id="some-id-for-react-to-grab">`
  - The id is something unique that `react-dom` will look for to render
- In `client/main.js`...
```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import {Meteor} from 'meteor/meteor';

Meteor.startup(function () {
    // Render JSX stuff here...
    let jsx = <p>wuba-luba-dub-dub</p>;
    React.DOM.render(jsx, document.getElementById('react-router'));
});

```
  - Meteor, treats `client/main.js` as the **main** entry point for clientside javascript,
  so this is where meteor will start execution, and it just needs `Meteor.startup( func())`
  to know how to have React start rendering the DOM
  - Then inside `startup` is where the first statements in React should begin
  - React requires at least one `ReactDOM.render()` statement with at least one identifier for an html tag to render inside of
