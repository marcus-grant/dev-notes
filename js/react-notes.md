# React-Notes
A general collection of highly unorganized notes relating to react. Use these to make a cleaner and perhaps subdivided set of notes later, this is more of a scratchpad to quickly get unfiltered and edited knowledge onto the hard disk.

## Fullstack React
*From Full Stack React Book*

### React App Design Process
1. Break app into components
  - Map out component structure of app by examining the planned app's UI
  - Single-responsibility for each component
  - Each component should conform to a principle of *minimal viable functionality*
2. Build a static version of the app
  - Bottom-level (user-visible) components made to render HTML on static props
  - Still pass this static information down from parents
3. Determine what props should really be a state
  - Use these questions to help determine whether a prop should be a state:
    1. Is it passed in from a parent via a props? If so, probably not a state
      - Helps to de-deuplicate data and its references
      - When going up the tree, the last time a prop is listed, that's where the state begins and the prop ends
    2. Does it change over time?
      - **Most Important** property of state is that it changes
    3. Can you compute it based on any other state or props? If so, not state
      - For simplicity, state should depend on as little data as possible
      - Ideally an independant variable that changes based on user input
      - State should ideally also only be a parent to other data flows, never a child
4. Determine in which component each piece of state should live
    - Follow these [criteria](https://facebook.github.io/react/docs/thinking-in-react.html) to help decide the appropriate component to hold a state
      - Identify every component that renders something based on that state
      - Find a common owner component
        - A single component above all others of that state in the hierarchy
      - Either the common owner or another component higher up in the hierarchy should own that state.
      - If it's tough to identify which compnent to own the states, it's quite possible that the break-down of components isn't granular enough, perhaps it makes sense to make another component higher in the hierarchy to hold this state
5. Hard-Code initial states
  - Define the initial states of the components that will own states
  - Hard-code these states into the top-level components that should have states
  - After initial states are added into the parent, make sure those props are passed to the children using the render functions of the parent
  - `{this.state.someState}`
6. Add inverse data flow
  - With the chain of custody of data from parent to child component firmly in place, it is now easier to define the more complicated reverse flow
  - Since data should only be passed one way by way of props, data is passed to the top, by intead defining an arrow function that is owned by the owner of the state, and then passed down to its child components till it reaches the place where that associated prop changes
  - This way, instead of child components of state changing state, they invoke the parent's event handler function involved with changing that state
7. Add Server communication

### States v Props
- **props** are the immutable residues of **state**
- props are the **one-way data pipeline** where parent components pass information to their child components
- State is managed in appropriate parent components and then passed down to their children as props
- Due to how React behaves to changes, this makes sense because any changes in what is displayed, only needs re-rendering of the DOM of everything at and below the owner of the state and its associated props

## Fundamentals
*[from freecodecamp](http://bit.ly/2vYaG9A)*
- Everything in react is about build React components and reusing them to render HTML
- At its simplest, react is a plain JS functions
  - Where `ReactDOM.render(<SomeComponent label="poop" />, reactMountPoint` describes an HTML element of id `reactMountPoint` to render everything inside the first parameter using the `ReactDOM`
- React components **always** need to start with a capital letter
  - React needs to be able to distinguish from standard HTML element tags
- Every component receives a list of ***props***, née HTML attributes or properties
- `createElement(ComponentOrElementName, { prop1: "some_val", prop2: "someVal", propN: "some-val" }`
  - `createElement` is one of the primary functions in the top-level of the React API
  - ? TODO: follow up on createElement because it might be incorrect
-

## React Router
[6]: http://bit.ly/2xrCaTH
[7]: http://bit.ly/2xsbdiM
* taken [from Paul Sherman on Medium][6] & [FreeCodeCamp on Medium][7]
- There are multiple types of routers react can Use
  - For browsers: `<BrowserRouter>` & `<HashRouter>`
  - `<BrowserRouter>` should be used when there's a server that will handle dynamic requests
    - Knows how to respond any possible URI stored in the server
  - `<HashRouter>` is for static websites
    - can only respond to requests for files it knows about
  - A good default would be `BrowserRouter`, typically `HashRouter` is only used when a website is predominantly serving static files
- Each router creates a standard javascript object, `history`, which tracks the current and previous locations being and having been visited
- The other components provided by the React Router rely on having that `history` object throughout the context, so it must be rendered inside those objects
- A React Router component that doesn't have a router as one of its ancestors will fail to work
- Here is some more info on the [https://medium.com/@pshrmn/a-little-bit-of-history-f245306f48dd](history) object
- `<Router>`objects can only have one child element
  - To deal with this it's important to to create an `<App>` that renders the rest of the application, seperate from the router
```jsx
import { BrowserRouter } from 'react-router-dom'

ReactDOM.render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
), document.getElementById('root'))
```
## FreeCodeCamp Tutorial From FreeCodeCamp.com
[from][7]
`"React Router keeps your UI in sync with the URL. It has a simple API with powerful features like lazy code loading, dynamic route matching, and location transition handling built right in. Make the URL your first thought, not an after-thought."`
### Creating the Site Dev-Environment
** NEEDS ALOT OF EDITING TO MAKE SURE PROCEDURE WORKS **
- To start either clone this [https://github.com/dabit3/beginners-guide-to-react-router](repo), or keep following along to set up the react environment
- This environment will be set up with React, Babel & webpack
```
npm init -y
```
- `-y` simply answers in the affirmative in all upcoming prompts
- Next, install react, react-dom, and the epynomious react-router and make sure npm saves them as dependencies
```
npm i react react-dom react-router@2.0.1 --save
```
- Next install the development dependencies. This includes:
  - [webpack](https://webpack.github.io/), which is a bundler of complex web assets, particularly javascripts, and gives easy to use wrappers to tie together the whole development workflow
  - [babel](https://github.com/babel/babel), a transpiler that converts modern javascript like es6, which we will be using, into fully compatible javascript
```
npm i webpack webpack-dev-server babel-core babel-loader babel-preset-es2015 babel-preset-react --save-dev
```
- Now, create configurations for webpack and babel
```
touch .babelrc webpack.config.js
```
- Typically, the app code resides in its own folder within the project root, usually under the name `project-root/app`
```
mkdir app
```
- In the `app` directory make three files with the `touch` command, which just creates empty files of the specified name in the current workind directory of the terminal
```
cd app
touch index.html app.js main.js
```
- by now there should be two folders, `app` and `node_modules`, and four files `.babelrc`, `package-lock.json`, `package.json` and `webpack.config.js` within the app directory
  - Inside `app`, should be three files;  `app.js`, `index.html` & `main.js`
- These configuration files are however largely if not entirely empty, so let's configure `.babelrc` properly first, by adding the presets for react and ES2015
```
{
  "presets": [
    "es2015",
    "react"
  ]
}
```
- In `webpack.config.js`, add the following configuration lines to get started:
```
module.exports = {
  entry: './app/main.js',
  output: {
    path: './app',
    filename: 'bundle.js'
  },
  devServer: {
    inline: true,
    contentBase: './app',
    port: 8100
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel'  
      }
    ]
  }
}
```
- Now that babel is set up for transpiling react and ES2015 properly, and a basic configuration for webpack to the current project file/folder structure, it's time to create webpack-dev-server configuration in `package.json`
  - Open `package.json` and insert the `webpack-dev-server` script with the `start` key to tell npm to start the dev server with the keyword `start`
  - If the `scripts` key isn't there already, insert the below text towards the end, before the final closing `}` bracket
```
"scripts": {
  "start": "webpack-dev-server"
}
```
- Which when inserted with other parts of the `package.json` file might look something like this:
```json
{
  ...,
  ...,
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "webpack": "^3.5.5",
    "webpack-dev-server": "^2.7.1"
  },
  "scripts": {
    "start": "webpack-dev-server"
  }
}
```
- Now let's create the opening HTML and React stuff
  - Open `index.html` and create a base html page
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>React Router</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="./bundle.js"></script>
  </body>
</html>
```
- Now, go into `main.js`, to create the point of entry for the app like below:
```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './app'
ReactDOM.render(<App />, document.getElementById('root'))
```
- This is defining the javascript file defined inside `webpack.config.js` by the key `entry`, which tells the webpack-dev-server where to go first when serving the website
- With the entry point in place, it's time to make a quick place holder display for the app inside of `app.js`, just to make sure everything till now works
```javascript
import React, { Component } from 'react'
import { Router, Route, Link, IndexRoute, hashHistory,
   browserHistory } from 'react-router'

const App = () => <h1>Hello World!</h1>
export default App
```

## Routing
- To let the router wrap all routes of the app, replace the `App` component with a React class returning a `Router` component
- Each route gets defined by a `<Route>` component
- When an address is matched, *whether through the address bar or through links* a `<Route>` component, it will return the component specified to it
- In `app.js`, change the file to this:
```javascript
import React, { Component } from 'react'
import { Router, Route, Link, IndexRoute, hashHistory, browserHistory } from 'react-router'
class App extends Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path='/' component={Home} />
        <Route path='/address' component={Address} />
      </Router>
    )
  }
}
const Home = () => <h1>Hello from Home!</h1>
const Address = () => <h1>We are located at 555 Jackson St.</h1>
export default App
```

### 404 Link for Non-Matched URI's
- It's good to have a Route for non-defined addresses or links
- To add it, follow the same pattern of defining a const arrow function representing the returned HTML for a 404 page, then add it **to the end** of the `<Router>`'s list of `<Route>`s
  - The 404 must be the last route, because it is defined using a wildcard `*`, as its path
  - That wildcard means that *any* link will trigger that route, so it must be placed after all other Routes so that it only triggers if no other route is matched
```javascript
import React, { Component } from 'react'
import { Router, Route, Link, IndexRoute, hashHistory, browserHistory } from 'react-router'
class App extends Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path='/' component={Home} />
        <Route path='/address' component={Address} />
        <Route path='*' component={NotFound} />
      </Router>
    )
  }
}
const Home = () => <h1>@Home</h1>
const Address = () => <h1>@Address</h1>
const NotFound = () => (
  <h1>404, the internet isnt made of tubes</h1> )

export default App
```

### IndexRoute & links
- From the React Router documentation
> The primary way to allow users to navigate around your application. <Link> will render a fully accessible anchor tag with the proper href.  >

- To create navigable links that are routed, the `<Link>` tag is used
  - `Link` has an attribute `to` which needs to be specified to a valid `Route` in order to link the link component to it
- Below is a component named `Nav` that creates two links, one for `Home` & `Address`

```javascript
const Nav = () => (
  <div>
    <Link to='/'>Home</Link>&nbsp;
    <Link to='/address'>Address</Link>
  </div>
)
```

- To make the `Nav` component persistent across all pages
- This is done by wrapping child routes in a main `<Route>` component
- Also, the home component needs to get updated by creating a new component called `Container`

```javascript
const Container = (props) => <div>
  <Nav />
  {props.children}
</div>
```

- `{props.children}` will allow any routes wrapped within this route to be rendered in this component.
- Rewriting the App component, wrap the `HomePage`, `Address` & `NotFound` routes inside the new Container route
- Also, set `HomePage` to be the `IndexRoute`, *ie the root route for the server*
- **IndexRoute** Is a type of `Route` which specifically points to the root address of the server


### Multiple Child or IndexRoutes
- If nested index routes are required, say if route options exist within another one and it's desirable to have one be the opening one
- In this example, rework the `app.js` file:
```javascript
class App extends Component {
  render () {
    return (
      <Router history={hashHistory}>
        <Route path='/' component={Container}>
          <IndexRoute component={Home} />
          <Route path='address' component={Address}>
            <IndexRoute component={TwitterFeed} />
            <Route path='instagram' component={Instagram} />
          </Route>
          <Route path='*' component={NotFound} />
        </Route>
      </Router>
    )
  }
}
```

- This sets the `IndexRoute` of the `Address` component to be `TwitterFeed`, so whenever the `Address` component is visited, `TwitterFeed` always comes up first
- Now create the `Instagram` & `TwitterFeed` components
```javascript
const Instagram = () => <h3>Instagram Feed</h3>
const TwitterFeed = () => <h3>Twitter Feed</h3>
```

- Then finally go to the `Address` component to add the `<Link>`s for the new `TwitterFeed` & `Instagram` components that get linked with the `Address` component
-

### activeStyle, activeClassName and IndexLink




## Misc.
-
