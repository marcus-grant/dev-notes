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
-
