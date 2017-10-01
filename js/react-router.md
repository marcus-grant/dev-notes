# React Router
- Client side routing makes the user experience so much better for web apps
- Routing allows for the different views within an app to be individually addressable with a 
- This article will cover part of creating a Web app that [Andrew Mead][2] has detailed in his [Udemy Course][1], which I used to get caught up with meteor and React
  - I needed to learn how to use Meteor with my Node & React stack
  - I found his course to be very well organized, presented, and taught for either experienced developers or for people new to any Node & React based web stacks
  - It is my hope that more people will support Andrew and others like hime by buying his course
  - People like him are what makes online learning such a good experience
- The App in question, is going to be a [URL Shortening][3] app that lets users login and...
  - add URLs to websites that they want to have a shorter link to
  - View previously added links
  - Remove existing links
  - Provide a shorter URL that will go to the place intended when entered into the browser

## Setting Up React Router
- Go to the directory on the computer where this project will be stored
- enter `meteor create short-lnk --release 1.4.2.3`
  - this has meteor create a project directory `short-lnk` which will house the entire project
    - this includes meteor, node, & MongoDB
    - anything inside this directory will now 
  - the `--release` option is there to use a specific version of meteor which has since been updated
  - when using `meteor run` to start the server, include `meteor run --release 1.4.2.3` to make sure meteor uses that version and not a newer one
  - If inside `.meteor/release`, `METEOR@1.4.2.3` is shown, then it won't be necessary to include the `--release` option detailed above
  - Using this version ensures that no compatibility or dependency issues arise when trying out this app
- Now it's possible to go into this meteor dev environment by changing directories to `short-lnk`
- Blaze is the default javascript library for creating front-ends in meteor, but React is in my view much better, so let's strip the generated meteor app of all blaze stuff
  - inside `client/main.html` remove all blaze stuff so the file likes like this:

`client/main.html`

```html
<head>
  <title>Short Lnk</title>
</head>

<body>
  <div id="app">
  </div>
</body>
```

  - Before continuing, some modules will need to be installed
    - react
    - react-dom
    - react-router
  - Install these with: `meteor npm install react@15.4.1 react-dom@15.4.1 react-router@3.0.0 --save`
  - **TODO** include babel runtime install
  - Now let's strip `client/main.js` of all Blaze references, below is what it should look like

`client/main.js`
```js
import { Meteor } from 'meteor/meteor';
import React from 'react';
import ReactDOM from 'react-dom';

class Signup extends React.Component {
  render() {
    return <p>Signup Component Here</p>;
  }
}

Meteor.startup(() => {
  ReactDOM.render(<Signup/>, document.getElementById('app'));
});
```

  - Meteor's startup method is needed to make the client work with the meteor API
  - Import React & ReactDOM to get started rendering React to the client
  - Includes a basic `<p>` tag as a placeholder to the `Signup` component
  - The `<Signup/>` component is going to be the default route when this page is visited so that's the first component to be created
  - But let's move that component into its own file in `imports/ui/Signup.js`

`client/main.js`
```jsx
import { Meteor } from 'meteor/meteor';
import React from 'react';
import ReactDOM from 'react-dom';
import Signup from '../imports/ui/Signup';

Meteor.startup(() => {
  ReactDOM.render(<Signup/>, document.getElementById('app'));
});
```

`imports/ui/Signup.js`
```jsx
import React from 'react';

export default class Signup extends React.Component {
  render() {
    return <p>Signup Component Here</p>;
  }
}
```
  - And finally, to get started, create an empty `Link` component that will represent a clickable link for the react router to respond to, to change the currently displayed view

`imports/ui/Link.js`
```jsx
import React from 'react';

export default class Link extends React.Component {
  render() {
    return <p>Link component here</p>;
  }
}
```

## Relationship Between Link Shortener App, Components, & Meteor API
![Diagram of Relation of Routes to the App][a]
- `/` the root link, or the route that gets shown without any other additions in the URL
  - This will lead to the React component `Login`
  - This component concerns itself with getting the user to either login or register with app
  - It will communicate with the Meteor API to...
    - *Login* the user, and loading all the data specific to that user
    - *Register* the user so that they can use the app and have stored data specific to them 
- `/signup`
  - The Signup component gets referenced by the `Login` component as well
  - If this route is typed into the browser instead, it goes straight to the signin component
- `/links`
  - This will lead to the more abstract `App` component which contains the App itself
  - Within the `App` it will be possible to show different components that show different views  - These views include `PrivateHeader`, `AddLink` & `LinkList`
  - `PrivateHeader` is the deafault component if simply the route `/links` is used
  - `/link` won't interract with the API in any way, it will simply bring up the `App`
    - `AddLink` if that component is selected inside the `App`, will use the API to add a new link that has been entered by the user into the application
    - `LinkList` will ask the API for a list of links that have been stored
- 404 - Not found
  - If an unrecognized route is entered into the browser, it will route to a `NotFound` component indicating to the user that the route is invalid
- `_blahblah`
  - any link that is underscored will take the user to the shortened link they've created with the app previously
  - instead of `_blahblah` the link will be a generated hash that is unique to that URL
  - Because no rendering is needed here, the API is just used to reroute the browser to the original link that was entered

## Set Routes
- Let's create the first route to show the basics of the react-router
- This will just create routes and the `<Link>` component as the return to show that react-router is routing by displaying two different very basic mockups of the app
- To do this, first the components, `Router`, `Route` & `browserHistory` are needed from `'react-router'` inside of `client/main.js`

`client/main.js`
```jsx
import { Meteor } from 'meteor/meteor';
import React from 'react';
import ReactDOM from 'react-dom';
import { Router, Route, browserHistory } from 'react-router';

import Signup from '../imports/ui/Signup';
import Link from '../imports/ui/Link';

const routes = (
  <Router history={browserHistory}>
    <Route path="/signup" component={Signup} />
  </Router>
);


Meteor.startup(() => {
  ReactDOM.render(routes, document.getElementById('app'));
});
```

- With these changes, now the all possible `Routes` are defined withing the `routes` reference and they get rendered by `ReactDOM.render(...)`
- A `<Router>` becomes the root component, which is important because this is the component that will determine what components will show up on the app based on the current `<Route>`
- A `<Route>` only needs two things:
  - a `path` which is just a string that will get entered into the end of the app's URL where if entered into the browser the `<Router>` will display an associated `component`
  - a `component` this is the component that the `Router` will display if above `path` is given into the app's URL
- The `Router` will also change the current URL of the browser to fit the current `path` when that `Route`'s component is the one being rendered
- Now if the URL `localhost:3000/` is entered, there should be nothing there as there is no route defined for the path `/`
  - If the developers tool is used in Chrome there should be a warning that there is no route for `/` given, which is true
- However, if the path `localhost:3000/signup` is entered, the placeholder text for the `Signup` component is displayed, showing that the `Router` is routing the it was intended
- To create another route to the `Link` component, instead, to show how to create seperate routes to different components, add a new line to the `routes` definition like below:

`client/main.js`
```jsx
// ...
const routes = (
  <Router history={browserHistory}>
    <Route path="/signup" component={Signup} />
    <Route path="/links" component={Link} />
  </Router>
);
// ...
```

- Now when the path `localhost:3000/links` is visited instead of `/signup` the `Link` component's placeholder text gets rendered instead of the `Signup` one
- Then let's create the root route, *i.e. the one that will be shown if just the app url, in this case http://localhost:3000 is entered*
- As discussed before, the `Login` component is going to be the first entry point for the app
- Create a component as `imports/ui/Login.js` and copy-paste the `Link` component and replace the class name and placholder text:

`imports/ui/Login.js`
```jsx
import React from 'react';

export default class Login extends React.Component {
  render() {
    return <p>Login component here</p>;
  }
}
```

- Then add the `Login` component just created into `client/main.js` and add a new `<Route>` to `routes` with the `path` as `'/'` and the `component` as `{Login}`

`client/main.js`
```jsx
// add to the imports the 'Login' component
import Login from '../imports/ui/Login.js';

// and add to 'routes' this <Route>
// inside <Router>
    <Route path="*" component={NotFound} />
```

- Now when just the app URL is entered, in this case `http://localhost:3000`, the `Login` component's placeholder text `Login component here` is rendered


## 404 Not Found
- When creating web apps it's important to have a route in place that prompts of an invalid route when they've entered one into their browser
- Technically this won't be real 404 response code since it is a route that's being caught by the router, but it is an invalid link and the behavior of the app should be the same as if it was a 404 event
- Start by creating a `NotFound` component as `imports/ui/NotFound.js`

`imports/ui/NotFound.js`
```jsx
import React from 'react';

export default class NotFound extends React.Component {
  render() {
    return <p>404 - Not Found</p>;
  }
}
```

- For now it's just a copy and paste of the `Link` component in a different file, with a different class name and a different `<p>` tag content to differentiate it from the rest
- Then inside of `client/main.js` the route will need to be added
  - This route will have to follow different rules from just about any other route that will be defined
  - Because the path needs to be match anything that could be added to the app's URL, in this case `localhost:3000` there needs to be a final route that hits for anything that is the app's route but doesn't match all other results
  - For this reason the `NotFound` route needs to be defined a wildcard `*` in the path **and crucially** needs to be the final route that gets defined because it needs to be the route that is still a match for the app URL but fits no other defined path

`client/main.js`
```jsx
// add to the imports the NotFound component
import NotFound from '../imports/ui/NotFound.js';

// and add to 'routes' this <Route>
// inside <Router>
    <Route path="*" component={NotFound} />
```

- Now if some random text is entered into the browser after the app URL, `http://localhost:3000/foobar`, now the `404 - Not Found` text will come up, and the same will be true if any other non-matching route is entered
- There should now be 4 seperate renderings available based on what is entered into the URL bar of the browser 
- **Summary**
  - import `Router`, `Route` and `browserHistory` to start making use of the react-router
  - create a `<Router>` component inside the `main/client` that defines the possible routes
  - Add `<Route>` components nested inside of `<Router>` to define...
    - `path` as something that can be entered as a suffix to the URL for that `Route`
    - `component` as the component that is rendered when the route is entered, and that will trigger the `Router` to change the current browser URL to `path` when inside the app that `component` is rendered
  - A catch-all route can be create as the **final route** to return a component indicating to the user that an invalid link was used


## Navigating Between Routes
- Previously it was shown how to associate routes entered into the browser's URL bar to components that will get rendered
- Now for the reverse, how to create components, that when clicked or invoked in some way by the user or the client, will go take the app to another route, and the browser's current URL reflecting that change
- 


## References
[1]: http://bit.ly/2xOadaU "Udemy Course on Full Stack Web Apps with Meteor & React"
[2]: http://www.mead.io/ "Andrew Mead's Homepage"
[3]: http://bit.ly/2x5Dr1j "Wikipedia: URL Shortening Article"

[a]: ./images/router-relations.png "Diagram of Relationships Between React Components, Routes & API"

1. [Udemy Course Link to Full Stack React with Meteor & React by Andrew Mead][1]
2. [Andrew Mead's Homepage][2]
3. [Wikipedia Entry on URL Shortening][3]
