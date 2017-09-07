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
