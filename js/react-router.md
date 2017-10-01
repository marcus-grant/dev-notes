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
  - Install these with: `meteor npm install react@16.4.1 react-dom@16.4.1 react-router@3.0.0 --save`
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
- To do this a lot of work is going to be done with the `browserHistory` component that was previously imported but not really worked with
- To explore the functionality of the `browserHistory` component, let's modify `client/main.js` to include this line: `window.browserHistory = browserHistory;` after the imports section
- Going into the browser's console, and entering `browserHistory` into it, all the props and information pertaining to the `browserHistory` component should now be visible for the app
  - **Note** the console won't be able to recognize these things if the **React Developer Tools** extion isn't installed
  - A previous [article][4] has been written do address debugging React & Meteor, which will have all the information necessary on using the developer tools for React apps
![Developer Console Screenshot of browserHistory][b]
*the browserHistory object on display within the browser's developer console*
- Using the `browserHistory` object inside of the developer console, it's possible to force the browser to interact with the react router, for example, by using `push('/signup')` to push into the browserHistory the signup route
![Pushing Routes directly into browserHistory from the developer console][c]
*Directly pushing routes into browserHistory from developer console*
- The same thing can now be done with any valid route that's been defined within the `Router`
- To emulate the behavior that would occur if the back button was used on the browser, try using the console to tell `browserHistory` to `browserHistory.goBack()`
- `browserHistory.go(SOME_INTEGER)` tells the browser history to move back or forward by some positive integer to go forward and a back with a negative integer
- More information can be found about this API by checking the [github repository][6] for the history API
- Because React uses the Virtual DOM, anchor tags aren't useful for navigating the app
- Instead, a `Link` component from `react-router` is needed to define something that is interacted with programatically or through user interraction to trigger routing to a different component
- The only prop that a `<Link>` component requires is a `to` component that specifies the route path that that `Link` will trigger within the react-router's `Router` component
- Go to `imports/ui/Login.js` to modify its `return` to instead give an `<h1>` tag and a `<Link>` component that goes `to` `"/signup"`

`imports/ui/Login.js`
```jsx
import React from 'react';
import { Link } from 'react-router';

export default class Login extends React.Component {
  render() {
    return (
      <div>
        <h1>Login to Short Lnk</h1>
        <Link to="/signup">Don't have an account?</Link>
      </div>
    );
  }
}
```
- Now when the page is reloaded on the login, or just the root URL, there should be a new heading and a `Link` that when clicked should go to the `/signup` route
- Do the same for the `Signup` component so that its link goes to the `Login` component

`imports/ui/Signup.js`
```jsx
import React from 'react';
import { Link } from 'react-router';

export default class Signup extends React.Component {
  render() {
    return (
      <div>
        <h1>Signup Here!</h1>
        <p>Already have an account?</p>
        <Link to="/">Login here</Link>
      </div>
    );
  }
}
```
- Now there should be `Links` leading back and forth between the `Signup` & `Login` components that work in the browser
- Now that the `browserHistory` component has been explored a little, remove it from the definition that was given to is in `client/main.js` since it's not going to needed directly from within the console anymore
- To get some insight into programatically altering the `browserHistory` through its functions, go to `imports/ui/Link.js` to create a temporary setup where it renders a heading, and a **Log Out** button that pushes `browserHistory` to the previous page
  - **Note** this isn't the intended final functionality and it would mess with how the app works as its imminently possible that the previous page on the app wasn't the `Link` component

`imports/ui/Link.js`
```jsx
import React from 'react';
import { browserHistory } from 'react-router';

export default class Link extends React.Component {
  onLogOut() {
    browserHistory.push('/');
  }

  render() {
    return (
      <div>
        <h1>Your Links</h1>
        <button onClick={this.onLogOut.bind(this)}>Logout</button>
      </div>
    );
  }
}
```
- Now, go to `http://localhost:3000/links` and there should be a heading and a **Logout** button
- If that **Logout** button is clicked, the event handler should be triggered because of the `onClick` attribute given to the `<button>` tag which calls `onLogout()`
- When that function is called, `browserHistory` is `push`ed to the previous page


## Component State & Signin Form
### React States
![React States Diagram][d]
- Props are the primary way of passing data to components in a React app
- However, props **should always be immutable** and information regarding an app wouldn't be terribly useful if it never changed
- This is where **states** come in
- **States** are data structures that reside within a react component that are allowed to change, and affect the props of subordinate components
- Typically props immediately affect the component's rendering
- But **states** can be used to determine how a component should be rendered as well
- User interaction, for example, can be used to change a component's state, which will then in turn affect how the component, or one of its subordinate components render
- Take a form submission...
  - When the user hits submit on a form, there could be several components that get rendered differently based on the information present
  - That information would pass down in the form of a prop, but it would originate from a parent component's **state** that gets modified by the form submission
- Or consider a button that hides a component if the user doesn't want it visible...
  - The visibility of that component would be stored in a **state** of a parent component that contains both the form and component that should be toggleable from hidden to visible
  - That parent component would then, from clicking that button, update the state, then pass that information down in the form of a prop to the component that should now be either hidden or be made visible

### React States, By Example
- Let's apply some state to the `Signup` component
- State requries that a `constructor()` function is provided by the containing component
- Constructors are common in tons of programming languages and I'll defer to the countless resources online, like [Mozilla's MDN][7], to explain what they are and why they should be used to instantiate a component's state

`imports/ui/Signup.js`
```jsx
import React from 'react';
import { Link } from 'react-router';

export default class Signup extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: this.props.count || 0
    };
  }
  increment() {
    this.setState({
      count: this.state.count + 1
    });
  }
  render() {
    return (
      <div>
        <h1>Signup Here!</h1>
        <p>{this.state.count}</p>
        <button onClick={this.increment.bind(this)}>+1</button>
        <p>Already have an account?</p>
        <Link to="/">Login here</Link>
      </div>
    );
  }
}
```

- The `constructor(props)` function gets called, whenever the component is first created
- `super(props);` when inside `constructor(props)` gathers the props passed into the component and calls React's `Component` function that this class extends inorder to create the component with those props and allowing for accessing them within the constructor
- **Note** that inside the `increment()` function, when the state is modified, it's not simply accessing the state as `this.state.count += 1`, the state needs to be set explicitly using React's `setState()` function
- Inside of this component's use of `setState()`, is object notation that's passing a new object with an updated `count` that is incremented by 1
- Then, when rendering the component, `{this.state.count}` gets accessed, just like a prop normally would in a React component, but now it's instead accessing a changeable state
- For demonstration purposes, this state is displayed by a `<p>` tag as a number representing the `count` part of the state, and it's changed by a `<button>`'s `onClick` attribute
- `onClick` is passed a bound function pointing to this component's `increment()` function
- Now, in the browser, if the URL entered is `http://localhost:3000/signup`, there should be a number representing `count` and it should increment by 1, everytime the `+1` button is clicked.
- If the page is reloaded, that component is now re-rendered, and the default value of 0, which is defined in the `Signup` component's `constructor` is shown instead
- Among other things, the constructor effectively defines the initial state of a component
- If a prop, `count` is passed into this component, this component will then take that value passed down into it as its state, because it is defined conditionally as either `this.props.count` or 0
- Remembering that arrow functions can also be passed into not only components, but JSX-based HTML tags, a decrement button can be created with the same functionality, except now it decreases the value of the counter by 1
  - In return of the render function of the component just add this:
  - `<button onClick={() => this.setState({count: this.state.count - 1})}>-1</button>`
- Now state is being actively used to manage how components are being rendered
- Now let's turn this state into something befitting the purpose of this app, again, inside of `imports/ui/Signup.js`

`imports/ui/Signup.js`
```jsx
import React from 'react';
import { Link } from 'react-router';

export default class Signup extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: '',
    };
  }
  onSubmit(e) {
    e.preventDefault();

    this.setState({
      error: 'Something went wrong?!'
    });
  }
  render() {
    return (
      <div>
        <h1>Signup Here!</h1>
        {this.state.error ? <p>{this.state.error}</p> : undefined }
        <form onSubmit={this.onSubmit.bind(this)}>
          <input type="email" name="email" placeholder="Email"/>
          <input type="password" name="password" placeholder="Password"/>
          <button>Create Account</button>
        </form>
        <p>Already have an account?</p>
        <Link to="/">Login here</Link>
      </div>
    );
  }
}
```
- Now the stuff used to test out how states work within a component has been removed
- Then state that's being kept by the component is changed to replace `count` with `error`
- `error` will be used to track whether the component is in some error state where the string used as the value of `error` will be either empty if no error is present, or a string representing an error message is given
- `onSubmit(e)` is a function that gets used when the newly created `<button>` for a login form is clicked
  - For now it will only give an error to the `error` state when the button is clicked
- Then inside the components `render` function, instead of the previous elements used to test state with a counter, now there's an optional error `<p>` tag that's rendered with an error message, and a login form
  - The login form has an `onSubmit` property that calls the `onSubmit(e)` function defined whenever the form is submitted
  - There's an input field for an email address, of type `email`, named `email`, and with placeholder text, `"Email"`
  - There's an input field for a password
    - the type is `password` so that the input is not shown as it's being typed
    - It's named `password`
    - It's placeholder has the text `Password`
  - The optional error message decides if the component will render the message at all if there's no current error present using a **ternary operator**
    - The **ternary operator** consists of a `?` following a conditional statement
    - Because in javascript a string that is empty evaluates as [Falsey][8] when used in a conditional statement, and true when a string is defined, preceeding `?` with `this.state.error` evaluates as true when there's an error string, and flase when there isn't one
    - Following `?` a ternary needs to have an expression for what to do when the condition of the ternary expression is true
    - The true, action for the ternary is to render a `<p>` tag with the error message stored in `this.state.error`
    - Then following an action for a true condition for the ternary, is `:` which indicates that the next expression will be what happens when the ternary is false
    - In this case, `undefined` is given so that nothing will render when there's no error
- `e.preventDefault` prevents the router refresh that is standard with javascript submits


## User Accounts Using Meteor
- Creating functionality to create user accounts, verify them securely, passing security tokens back and forth between client, managing login state, and to manage user information can requrie a lot of annoying boilerplate using a standard Node stack
- Meteor makes this **much** quicker and easier than vanilla Node normally would
- This is built into meteor, but isn't enabled by default
- To enable some of this functionality right away...
  - stop meteor
  - ` meteor add accounts-password`
    - this will add in the dependencies required to properly handle basic account validation, cryptogoraphy, secure storage, and other useful packages to handle user accounts based on hashed passwords
  - There won't be much interraction with these packages necessary unless more complex use of them is warranted by the app
  - The default use of these packages should be sufficient to make the user management quite effective both in experience and security metrics
- `accounts-password` may have been installed but it will never be directly imported by react
- instead this is abstracted by Meteor's `meteor/accounts-base`
  - this is documented by Meteor's [documentation][9] 
    - [Documentation][10] for the accounts features
    - [Documentation][11] for those accounts with multiple servers
    - [Documentation][12] for the passwords features of meteor

### Implementing Basic User Accounts
`imports/ui/Signup.js`
```jsx
import React from 'react';
import { Link } from 'react-router';
import { Accounts } from 'meteor/accounts-base';

export default class Signup extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: '',
    };
  }
  onSubmit(e) {
    e.preventDefault();

    let email = this.refs.email.value.trim();
    let password = this.refs.password.value.trim();

    Accounts.createUser({email, password}, (err) => {
      console.log('Signup callback', err);
    });

    this.setState({
      error: 'Something went wrong?!'
    });
  }
  render() {
    return (
      <div>
        <h1>Signup Here!</h1>
        {this.state.error ? <p>{this.state.error}</p> : undefined }
        <form onSubmit={this.onSubmit.bind(this)}>
          <input type="email" ref="email" name="email" placeholder="Email"/>
          <input type="password" ref="password" name="password" placeholder="Password"/>
          <button>Create Account</button>
        </form>
        <p>Already have an account?</p>
        <Link to="/">Login here</Link>
      </div>
    );
  }
}
```
- This rewrite of `imports/ui/Signup.js` while using meteor's `Accounts` module implements basic functionality of user management on the app
- `Accounts` imports the accounts feature from meteor itself
- Inside `onSubmit(e)`
  - `Accounts.createUser` uses ES6 object notation to reference the form `refs` defined in `email` & `password` to grab emails and passwords entered by the user
  - `createUser` is an `Accounts` function that adds users with information fed to it, in this case: `email` & `password`
- the `<input>` element for emails is modified to specify a `ref` of value `email` which allows react to grab the `ref` inside `onSubmit` to easily grab the value of the input when submitted
- the password `<input>` does the same with its `ref`, but does it with the value `password`
- Those `refs` then get saved as objects inside `onSubmit` using `this.refs.email.value.trim()` to access the refs by name, then get their values, and finally trim the string to only the relavant values, *ie no leading or trailing spaces*
- `refs` makes form dataflow significantly easier to implement
- Going into the browser's Meteor DevTool, opened to the DDP tab, it's now possible to view the traces that occur when adding a user using meteor
- Creating an account, this should then show up:
![Meteor DDP trace when creating a user][f]

- When opening these traces up all the details are shown, including hashed and salted passwords that result from entering a new password for the user
  - this is important because plaintext passwords are easy to exploit
  - Now whenever passwords are used they will be hashed before being transmitted and evaluated as those hashed values
  - The plaintext password is never stored anywhere
- Now let's see how the client app is using the meteor module to handle user creation
![Console screenshot of app when adding user][g]
- `require('meteor/meteor').Meteor.userId()` shows the hashed userID that meteor has recorded for the user that was created
  - When there is an ID, the user is logged in, otherwise it should return a `null` value
- `require('meteor/meteor').Meteor.user()` returns a bit more information about the user
  - **Note** that the password is not send, for security reasons
- `require('meteor/accounts-base')` returns information about the functions and information about the meteor `accounts-base` module that was used by the app, including the methods that can be used in console to get more information and actions
- `require('meteor/accounts-base').Accounts` returns the accounts objects created
- `require('meteor/accounts-base').Accounts.logout()` is one of the functions that should be listed above, and here it's used to log a user out of the server
- using the same function as above, `require('meteor/meteor').Meteor.user()`, there should be a `null` value returned because the user is now logged out
- the exact same thing can be done with the `user()` method call above, where again, `null` is returned because the user is logged out
- Now, the same thing will be true in the MongoDB user database, as that is how meteor stores user information
- Going into another terminal while meteor is running, enter the command `meteor mongo`
- Inside the mongo terminal, entering `db.users.find()` will return all of the users that have been created for this server, and all of their relevant account information

## References
[1]: http://bit.ly/2xOadaU "Udemy Course on Full Stack Web Apps with Meteor & React"
[2]: http://www.mead.io/ "Andrew Mead's Homepage"
[3]: http://bit.ly/2x5Dr1j "Wikipedia: URL Shortening Article"
[4]: http://bit.ly/2xOljg8 "My previous article on React Debugging Tools"
[5]: http://bit.ly/2yg7clh "React Documentation: Thinking in React (good for state info)"
[6]: http://bit.ly/2x5N3cE "Github Repo for the JavaScript History API"
[7]: https://mzl.la/2yfSoTG "MDN Reference Page on Javascript Constructors"
[8]: https://mzl.la/2x6mNi3 "MDN Reference Page on Javascript Falsiness"
[9]: http://bit.ly/2x6qqoq "Meteor Documentation: Main Page"
[10]: http://bit.ly/2ygCqbU "Meteor Documentation: Accounts"
[11]: http://bit.ly/2ygAwrE "Meteor Documentation: Accounts (multi-serer)"
[12]: http://bit.ly/2x6oDzt "Meteor Documentation: Passwords"

[a]: ./images/router-relations.png "Diagram of Relationships Between React Components, Routes & API"
[b]: ./images/react-router-browserhistory-console.png "Screenshot of Developer Console Displaying browserHistory"
[c]: ./images/browserHistory-push.png "Pushing Routes into browserHistory"
[d]: ./images/react-states-diagram.png "Diagram Showing How React States Work"
[f]: ./images/meteor-login-trace.png "Screenshot of Meteor DevTool trace after a login"
[g]: ./images/meteor-login-console.png "Screenshot of Meteor DevTool Console commands showing login functionality"

1. [Udemy Course Link to Full Stack React with Meteor & React by Andrew Mead][1]
2. [Andrew Mead's Homepage][2]
3. [Wikipedia Entry on URL Shortening][3]
4. [My Article on Using React Developer Tools][4]
5. [React Documentation: Thinking in React (good for theoretical overview of state)][5]
6. [Github Repository for Javascript's History API][6]
7. [MDN: Javascript Constructors][7]
8. [MDN: Falsiness in Javascript][8]
9. [Meteor Documentation: Main Page][9]
10. [Meteor Documentation: Accounts][10]
11. [Meteor Documentation: Accounts (multi-server)][11]
12. [Meteor Documentation: Passwords][12]
