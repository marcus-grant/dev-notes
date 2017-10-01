# Devoloping Meteor & React Apps
- There are plenty of debug tools that between meteor & browser dev tools like Chrome 

## JS Debugger
- `debugger;` is keyword that can be entered into the code to initialize a debugger
- This will pause execution whenever the application gets to that point in the code
- Effectively makes a breakpoint where other actions can be taken to examine the app
- From here within the browser developer tools it should now be possible to use things like...
  - The console
    - Go into the console to examine variables 
  - The scope inspector 
    - examine the scope of that breakpoint
    - what references are available
    - what their values are
  - The call stack leading up to this point
  - Step into either the next breakpoint or the next call
- When stepping out of the debugger breakpoint in the browser there will usually be a (*atleast in Chrome*) `websocket` error in the console
  - This is a side effect of stopping the browser for debugging under meteor and can be ignored

## Chrome Extensions For Debugging
- Chrome is very good for developing & debugging React & Meteor Applications especially if its extensions engine is used with some add-ons
- [Meteor DevTools][2] & [React Developer Tools][3] are two very good extensions to the developer panel that help debug meteor and react respectively

### Meteor DevTools
- Meteor DevTools is a nifty Chrome extension that adds lots of useful tools to debug meteor specifically
- Anyone with the Chrome or Chromium browser can get it [here][2]
- After installing the extension and opening the developer panel of chrome, there should be a new section called **Meteor**
- Opening it should bring up a tool panel looking like this:
![Meteor DevTools in Chrome][a]
- There are several tools in here that help with debugging meteor fetures:
  - Security
    - Often without addressing any security issues there will be a lot of security warnings
  - MiniMongo
    - This is a viewer for all the cached information on the local client cached mongo datastore
    - There are collections on the left and individual documents on the right with a search field
    - This makes it easy to debug the mini mongo collections especially as they get large & complex
  - Blaze
    - Blaze, if used will be shown here
    - Generally if using meteor with react this will not be used
  - DDP
    - Lets us view the dataflows between client and server over the websockets that meteor uses
    - Distributed Data Protocol
    - When, for example, players are added and removed in this app, it will show
    - Below is a screenshot of the activity of the DDP when players are added and removed as they data is synchronized between client and server
![DDP Tool When Changing Local Mongo Store][e]
    - `got result` as shown in the message exchange, shows the changes being on the server
    - expanding it further it's possilble to see the details of that change
    - `updated` shows the results of updating the local client

### React Developer Tools
- Facebook themselves created this extension to help debug their javascript library
- The extension can be acquired for Chrome [here][3]
- Bringing Chrome into *Developer Mode* there should now be a new panel either openly displayed or inside the `>>` icon if the window isn't big enough to display all sections
![React Developer Tools Section Screenshot][b]

#### Viewing the Virtual DOM
-What is immediately visible is a set of computed HTML tags with all the proper react properties applied to them like `className` or `props` for example
- Remember that react renders a virtual DOM onto the real one
- All that is usually visible to the regular chrome developer tools is the regular DOM
- With this extension, now the virtual DOM with all the defined components, props etc. become visible
- The tool even allows going into a specific component and changing prop values to test them out live on the rendered app
- On the right panel that shows the virtual DOM, selecting a component should bring up another panel on the left that shows that component's `props`
- Selecting a prop from this panel allows changing its value, in the picture below the `TitleBar` component is being changed from `Score Keep` to `Interdimensional Game Show` *Rick & Morty Reference*
![TitleBar Component Being Changed By React Developer Tools][c]

#### Accessing the Console
- The console is now modified by the extension to allow working with the Virtual DOM to do things like accessing values of props or functions
- Sometimes the console isn't visible alongside the virtual DOM viewer tool that React gives you and it's possible to reposition these panels
  - By going into the vertical three dots icon, there exists a menu with a `show console` option, that allows bringing in the console into the same view
- `$`
  - The dollar sign inside the console is just like the `this` keyword that gets used inside a react javascript method to access props
  - `$r` shows the **r**oot of the application for example




## References
[1]: http://bit.ly/2xOadaU "Udemy Course on Full Stack Web Apps with Meteor & React"
[2]: http://bit.ly/2fELXlm "Chrome Extension: Meteor DevTools"
[3]: http://bit.ly/1dGLkxb "Chrome Extension: React Developer Tools"

[a]: ./images/meteor-devtools.png "Screenshot of App with Meteor DevTools"
[b]: ./images/react-devtools.png "Screenshot of App with React Developer Tools"
[c]: ./images/react-devtool-change-title.png "Screenshot of App React Tool Change of Title"
[d]: ./images/react-devtools-console.png "Screenshot of React Tools Console"
[e]: ./images/meteor-devtools-ddp.png "Screenshot of Meteor DevTools DDP View"

1. [Udemy Course Link to Full Stack React with Meteor & React by Andrew Mead][1]
2. [Chrome Store Link to Meteor DevTools][2]
3. [Chrome Store Link to React Developer Tools][3]
