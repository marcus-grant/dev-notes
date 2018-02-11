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

## States
- For react states inside of ES6 classes, currently the best way to handle class methods that modify state is to follow this pattern
```js
class SomeClass extends React.Component {
  constructor(props) {
    super(props);
    this.state = { someStateProperty: 42, };
    this.someEventHandler = this.someEventHandler.bind(this);
  }

  someEventHandler() {
    console.log('Event Happened!');
    this.setState({ someStateProperty: this.state.label + 1 });
  }

  render() {
    return (
      <SomeComponent eventHandler={this.someEventHandler} />
    );
  }
}
```
- Notes about above snippet:
  - The airbnb eslint configuration requires the older, non-arrow function, method of binding this within the constructor to a non-arrow class function
  - Simply pass the function reference as a prop to the child component
  - without the airbnb config, using more modern configs it's possible to ignore the constructor binding by simply using an arrow

## Miscalleneous
- When passing callbacks as event handlers and passing arguments...
  - if passing without arrows but with args, the function gets called during the render call of the component and endless loops and unexpected behavior can form
  - **BAD**
  - `<button onClick={combinedCallback(props.id)}>Click</button>`
  - **GOOD**
  - `<button onClick={()=> props.combinedCallback(props.id)}>Click</button>`
  - `<button onClick={props.callbackWithoutArgument}>Click</button>`


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
- Links have a property where their appearance changes based on whether the route is active
- There are two main ways of defining what that appearance is, `activeStyle` & `activeClassName`

#### activeStyle
- To apply `activeStyle` simply add the `activeStyle` as a property to the respective `<Link>`
- `<Link activeStyle={{color: '#53acff'}} to=''>Home</Link>`
- Do this for all tags that should exhibit  this behavior
- In the first code example below, if that change is put into effect on the previous examples, then the nested `IndexRoute`s all get highlighted as active if their parent route is
- To fix this, use the `onlyActiveOnIndex` property on the appropriate `Link` components
  - `<Link onlyActiveOnIndex activeStyle={{color:'#53acff'}} to='/'>Home</Link>`
- There's also the sibling component to the `<Link>` component, `<IndexLink>` that automatically bakes that behavior in
  - `IndexLink` only shows active when the current route is linked *exactly* to that component
  - The second example below shows as a modification to the one above that example that fixes the problem using `IndexLink`s

```javascript
const Nav = () => (
  <div>
    <Link activeStyle={{color:'#53acff'}} to='/'>Home</Link>&nbsp;
    <Link activeStyle={{color:'#53acff'}} to='/address'>Address</Link>&nbsp;
    <Link activeStyle={{color:'#53acff'}} to='/about'>About</Link>
  </div>
)
```
* Active link styling using the activeStyle propery on <Link>'s from 1. above *

```javascript
const Nav = () => (
  <div>
    <IndexLink activeStyle={{color:'#53acff'}} to='/'>Home</IndexLink>&nbsp;
    <IndexLink activeStyle={{color:'#53acff'}} to='/address'>Address</IndexLink>&nbsp;
    <IndexLink activeStyle={{color:'#53acff'}} to='/about'>About</IndexLink>
  </div>
)
```
* The same Nav component definition but with IndexLink's instead *

#### activeClassName
- Instead of explicitly defining what this style should be for every single `Link` or `IndexLink` component, it's possible to use style classes Instead
- This is accomplished using the component attribute, `activeClassName` inside a JSX `Link` or `IndexLink` tag, and giving a style class name, `.active`, in this case but it could be anything, within a CSS file or `<style>` tag
```html
<style>
  .active {
    color:#53acff
  }
</style>
```
- Then replace the `activeStyle` of the previous `app.js` examples with `activeClassName` and the now defined `active` style class, the effect should be the same


### Named Components
- To be able to access named components the child elements are accessible by name on `this.props`. In this case `this.props.children` is undefined
- All route components can participate in the nesting
- First create a new Component that will be rendering our Named Components
- These components would then be available as props
```javascript
const NamedComponents = (props) => (
  <div>
    {props.title}<br />
    {props.subTitle}
  </div>
)
```

- Then create two new components called `Title` & `SubTitle`
```javascript
const Title = () => (
  <h1>Hello, Im a Title component</h1>
)
const SubTitle = () => (
  <h2>Hello, Im a SubTitle component</h2>
)
```

- Now, create a new route for the `NamedComponents` component, and define the `Title` & `Subtitle` components in the `IndexRoute`:
```javascript
<Route path='/namedComponent' component={NamedComponents}>
  <IndexRoute components={{ title: Title, subTitle: SubTitle }} />
</Route>
```

- Finally, add a link in the `Nav` component containing navigation logic, to associate `NamedComponent` with a real link
```javascript
<IndexLink activeClassName='active' to='/namedComponent'>NamedComponents</IndexLink>
```

- The result when reloading the new page should be that there should now be a new link, *Named Components* in the navigation section
- Clikcing this new link should bring up the newly defined Title and Subtitle components which then get passed down as props and instructs React to render them as defined

### Route Parameters
- A useful use for a URL is to be able to pass route parameters to the web app through the string being passed to the endpoint
- In React Router this is also possible to emulate through a Single Page application
- To demonstrate how this is done, take a look at this modified version of the *About* component definition
```javascript
const About = (props) => (
  <div>
    <h3>Welcome to the About Page</h3>
    <h2>{props.params.name}</h2>
  </div>
)
```

- Now, go to http://localhost:8100/#/about/frodo , the name *frodo* gets displayed as part of the heading tag
- However, try to go to http://localhost:8100/#/about , without a parameter specified there's no match with any routes, and thus the 404 result gets shown
- In order to remedy this, an **optional** value for the for the `Route` attribute `path` needs to be specified by using an enclosing set of paranthesis
```javascript
<Route path='/about(/:name)' component={About} />
```

- Now if http://localhost:8100/#/about is visited, it will simply display the rendered about page with out the subtitle with the name from the route parameter
- This can be taken even a step further so that an empty `<h3>` isn't rendered at all if there is no parameter route given
```javascript
{ props.params.name && <h2>Hello, {props.params.name}</h2> }
```
- Replacing the previous `<h2>` from the `About` component definition and replacing it with the above block of code, will only render said `h2` tag if `props.params.name` is passed into the prop's component
  - The double ampersands `&&` tells react that the statement after it should only be carried out if the previous one evaluates logically as true, which would happen if the prop, `props.params.name` is present
- Now that tag will only be rendered if the prop is present

### Query String parameters
- It's possible to pass in specific query strings as props from the route address that gets rendered at a specific route and access these parameters in `props.location.query`
- To demonstrate the mechanics of a query string, create a new component called `Query` and have it render a property called `props.location.query.message`
```javascript
const Query = (props) => (
  <h2>{props.location.query.message}</h2>
)
```

- Now let's set up the new Query Route within the address route already created
```javascript
<Route path='/address' component={Address}>
  <IndexRoute component={TwitterFeed} />
  <Route path='instagram' component={Instagram} />
  <Route path='query' component={Query} />
</Route>
```

- Then finally link to this route by creating a new Link component, and passing in a query string called message and giving it a value.
- This is done in the `to` property of a `Link` tag

```javascript
<IndexLink
  activeClassName='active'
  to={{
    pathname: 'address/query',
    query: { message: 'Hello from Route Query' }
  }}>Route Query</IndexLink>
```

- Now if the *Route Query* link is clicked on the newly update site, the

### Changing State Based on Current State
From [FullStack React][01]
>“whenever a state update depends on the current state, it is preferable to pass a function to setState()”
```js
decrement = () => {
  this.setState(prevState => {
    return { value: prevState.value - 1, };
  });
}
```

### Conditional Rendering using Logical Operators
- From [MDN][02]
- These operators can be used to **short-circuit** evaluations
- This is due to boolean logic...
  - anything false **AND** anything else, is false
  - anything true **OR** anything else, is true 

 Operator | Usage            | Description 
--------- |----------------- |------------ 
 AND `&&` | `expr1 && expr2` | Returns `expr1` if convertible to false, otherwise `expr2`
 OR `||`  | `expr1 || expr2` | Returns `expr1` if convertible to `true`, otherwise `expr2`
 NOT `!`  | `!expr` | Returns `false` if `expr` can be evaluated *truthy*, otherwise, `false`





## Misc.
-

References
----------
[01]: https://www.fullstackreact.com/ "FullStack React by ng-book"
[02]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators "MDN: Logical Operators in JS"

1. ["FullStack React" by ng-book][01]
2. [MDN: Logical Operators in JS][02]
