Unit Testing React using Enzyme & Jest
======================================
Notes on using **TDD** *(Test Driven Development)* principles in Unit Testing React with [Enzyme][01] and [Jest][02]. Many notes taken from Toptal's [article][03] on the topic.

* Doesn't seem obvious how to unit test React without insane complexity for creating test cases and assertions for the resulting DOM


How to Unit Test a React Component
----------------------------------
### From [Toptal][03]
* These testing operations can be performed
  * Verify a function in `props` is called when a certain event is dispatched
  * Get result of the `render` function given the current component's state and pattern match it
  * Check if the number of children of a component matches an expected quantity 
* In order to use the above methods, [Jest][02] & [Enzyme][03] are used


Using Jest to Create Unit Tests
-------------------------------
Largely taken from Jest's offical [documentation][04] & Toptal's [article][03]
* Jest is made by Facebook so it's really good at interacting with virtual DOM elements from a React app
* Has a *CLI* utility for executing tests, similar to Jasmine & Mocha
  * Jasmine & Mocha are other popular JS utilites for unit testing
* Helps to create mock functions with almost no configurations
* Has nice set of matchers that makes test assertions easier to read and implement
* **Snapshot** testing helps check & verify component rendering results
* **Snapshots** get used to capture a component's tree and save it into a file that can be used to compare it against a rendering tree
  * This includes anything passed to the `expect` function as first argument


Using Enzyme to Mount React Components
--------------------------------------
* Enzyme gets used to mount & traverse React's virtual DOM
  * This helps gain access to its properties, state, and children props for assertions
* There are two basic functions for component mounting:
  * `shallow` is a function to load, *in memory only*, the root component
  * `mount` is a function that loads the full DOM tree
* Combined, enzyme & jest allows for mounting React 
* Below is how this workflow is visualized, credit goes to [Alonso Ayala Ortega][03]
![Workflow Diagram for Enzyme & Jest Testing][i01]


Setting Up the Testing Environment
----------------------------------
* These packages are needed:
  * `yarn add --dev enzyme enzyme-adapter-react-16 jest jest-cli babel-jest babel-preset-env regenerator-runtime babel-core`
  * This [article][07] suggests these dependencies:
  * `yarn add --dev jest babel-jest react-test-renderer enzyme enzyme-adapter-react-16 enzyme-to-json`
* A `.babelrc` file is probably necessary and should look something like this
```json
{                                                                                          
  "presets": ["env", "react"]                                                              
}                                                                                          
```
* ESLint doesn't typically recognize global variables unless you specify it to do so and jest uses the global functions like `describe`, `it`, `expect`, etc. 
  * This causes irritating linter errors for `no-undef` when it's perfectly fine
  * To fix this, add the [ESLint Environment Configurations][06], `"jest": true,`
  * Below is my sample ESLint which has other configurations besides the jest one
```json
{
    "extends": "airbnb",
    "env": {
      "browser": true,
      "node": true,
      "es6": true,
      "jest": true
    },
    "rules": {
      "no-param-reassign": 0
    }
}
```
* [(From)][07] - To configure jest, `package.json` needs to list a set of `setupFiles`
* Also, while configuring in `package.json`, add the `snapshotSerializer`
```json
"scripts": {
  "test": "jest",
  "test:watch": "jest --watch",
  "test:coverage": "jest --coverage"
},
"jest": {
  "setupFiles": ["./test/jestsetup.js"],
  "snapshotSerializers": ["enzyme-to-json/serializer"]
}
```
* Snapshot serializer allows passing Enzyme wrappers directly to Jest's snapshot matcher, without converting them manually by calling *enzyme-to-json*'s `toJson` function
* These added configurations can be added by simply adding a file: `./test/jestsetup.js` *in this example* to handle all the jest configurations
```js
import Enzyme, { shallow, render, mount } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

// React 16 Enzyme adapter
Enzyme.configure({ adapter: new Adapter() });

// Make Enzyme functions available in all test files without importing
global.shallow = shallow;
global.render = render;
global.mount = mount;
```

* CSS Modules can be included as well, by adding this option to `package.json`
```json
"jest": {
  "moduleNameMapper": {
    "^.+\\.(css|scss)$": "identity-obj-proxy"
  }
}
```
* Then install the [`identity-obj-proxy`][08] package like so:
  * `yarn add --dev identity-obj-proxy`
* This allows hooking into the loader process of webpack among other niceties

Testing Basic Component Rendering
---------------------------------

j


Create the React Component Using TDD
------------------------------------
* In TDD...
  1. Create a failing test which will try to render a React component using enzyme's `shallow` function




References
----------
[01]: https://github.com/airbnb/enzyme "Enzyme Docs: Main Page"
[02]: https://facebook.github.io/jest/ "Jest Docs: Main Page"
[03]: https://www.toptal.com/react/tdd-react-unit-testing-enzyme-jest "Toptal - Test-driven React.js Development: React.js Unit Testing with Enzyme and Jest"
[04]: https://facebook.github.io/jest/docs/en/getting-started.html "Jest Docs - Getting Started"
[05]: https://github.com/FormidableLabs/enzyme-matchers/tree/master/packages/jest-enzyme "Github FormidableLabs/jest-enzyme"
[06]: https://eslint.org/docs/user-guide/configuring#specifying-environments "ESLint Docs: Specifying Environments"
[07]: https://hackernoon.com/testing-react-components-with-jest-and-enzyme-41d592c174f "Medium - HackerNoon: Testing React Components with Jest & Enzyme"
[08]: https://github.com/keyanzhang/identity-obj-proxy "Github - keyanzhang/identity-obj-proxy"

[i01]: ./images/react-unit-testing-enzyme-jest-workflow-diagram.png

[v01]: https://youtu.be/59Ndb3YkLKA "Testing React Applications Talk by Max Stoiber"

1. [Enzyme Docs: Main Page][01]
2. [Jest Docs: Main Page][02]
3. [Toptal - Test-driven React.js Development: React.js Unit Testing with Enzyme and Jest][03]
4. [Jest Docs - Getting Started][04] 
5. [Github FormidableLabs/jest-enzyme][05]
6. [ESLint Docs: Specifying Environments][06]
7. [Medium - HackerNoon: Testing React Components with Jest & Enzyme][07]
8. [Github - keyanzhang/identity-obj-proxy][08]

### Other Resources
* [*Testing React Applications*][v01] by Max Stoiber talk on TechBerlin shown on Youtube
