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
* taken [https://medium.com/@pshrmn/a-simple-react-router-v4-tutorial-7f23ff27adf](from)
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



## Misc.
-
