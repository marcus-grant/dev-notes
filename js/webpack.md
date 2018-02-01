Configuring Webpack for Various Projects
========================================

Webpack is an awesome bundler and stuff. It helps bunle various javascript files, run preprocessors, run scripts, etc. Insert unecessary preamble here.

Installing Webpack
------------------
Webpack is just a node module that gets installed locally to a project directory. But, remember, node projects need to be initialised somehow, and if it isn't done through a script like *create-react-app*, it will need to be done through `npm` using the init option like so: `npm --init`. In case it wasn't known already, this will create within a folder, a `package.json` file which holds all the dependency information as well as scripts and node configurations. Also a `node_modules/` and all metadata necessary for those modules and the current project. To install webpack, as any node module/package, use the command: `npm install --save-dev webpack` or if yarn is preferred, `yarn add --dev webpack`.

Initial Webpack Setup
---------------------
After installing the webpack module locally, it's time to setup the initial configurations. But before that, a word about project structure. This article will use a popular convention of putting source files that are to be transpiled or pre-processed into the final result of the project into a `src/` folder in the root of the project directory. Then the resulting bundled output along with any other public assets that the final web app will use is placed in a `dist/` directory also in the root of the project.

`webpack.config.js` to be created in the project root, is where all webpack configurations should be defined. To start things off, copy this configuration.

```js
// We are using node's native package 'path'
// https://nodejs.org/api/path.html
const path = require('path');

// Constant with our paths
const paths = {
  DIST: path.resolve(__dirname, 'dist'),
  SRC: path.resolve(__dirname, 'src'),
};

// Webpack configuration
module.exports = {
  entry: path.join(paths.JS, 'app.js'),
  output: {
    path: paths.DIST,
    filename: 'app.bundle.js'
  },
};
```
Some quick remarks about the configuration file:
- `const path` defines a path object from the internal JS library that deals with directory and file paths
- `const paths` is just for convenience to setup constants for the various paths that will e used by the project
- `module.exports` is where webpack actually gets configured:
  - `entry` is where to define the entry point for the javascript portion of the client side app
    - it joins the predefined constant `SRC` with the path to index.js, the entry point for the app
  - `output` is a set of configurations that defines how the transpiled, preprocessed, & bundled app is stored
    - `path` just tells the file where to store the finished javascript file
    - `filename` just names the finished file
- Much more can be done inside `webpack.config.js`, but those will be covered later

Now with that config in place, it's possible to run the webpack binary by itself without specifying any parameters

Webpack for React (Babel)
-------------------------
- React requires transpilation from JSX into strict JS syntax, typically ES5 or older and into somewhat optimized JS code
- Dev dependencies:
  - `yarn add --dev babel-cli babel-core babel-loader babel-preset-env babel-preset-es2015 babel-preset-es2016 babel-preset-react babel-preset-stage-2`
- There also needs to be an HTML file that the main React app renders into by ID typically in either: `./index.html`, `src/index.html` or `dist/index.html`
```html
<!doctype html>                                                                                                  
<html>                                                                                                           
  <head>                                                                                                         
    <meta charset="UTF-8">                                                                                       
    <title>Express API Demo</title>                                                                              
  </head>                                                                                                        
  <body>                                                                                                         
    <div id="react-root"></div>                                                                                  
    <script src="bundle.js"></script>                                                                            
  </body>                                                                                                        
</html>                                                                                                          
```
***NEED TO ADD STUFF ABOUT YOUR PARTICULAR GO_TO WEBPACK CONFIG***

SASS Compilation & Loading
--------------------------
- Dependencies: `yarn add --dev css-loader extract-text-webpack-plugin html-webpack-plugin node-sass post-css-loader sass-loader`
  


Misc. Notes
-----------
- If babel can't resolve `.jsx` extensions in imports, according to this [babel forum thread][14], there should be a `resolve` conficuration within `webpack.config.js`
`webpack.config.js`:
```js
// within module.exports at top level
// ...
resolve: {
  extensions: ['.js', '.jsx']
},
// ...
```

References
----------
[10]: http://jimfrenette.com/2017/11/webpack-3-sass-cssnano-autoprefixer-workflow/ "Jim Frenette: Webpack 3 with SASS loaders"
[11]: https://www.twilio.com/blog/2015/08/setting-up-react-for-es6-with-webpack-and-babel-2.html "Twilio Blog: Setting Up Webpack for React & ES6 using Babel & Webpack Loaders"
[12]: https://stanko.github.io/webpack-babel-react-revisited/ "MuffinMan: Setting Up Webpack, Babel & React from Scratch (Revisited)"
[13]: http://bit.ly/2zoKnrv "StackOverflow: Loader Error in Webpack on JSX"
[14]: http://discuss.babeljs.io/t/es6-import-jsx-without-suffix/172/2 "Babel Forums: ES6 import .jsx without suffix/extension"

10. [Jim Frenette: Webpack 3 with SASS loaders][10]
11. [Twilio Blog: Setting Up Webpack for React & ES6 using Babel & Webpack Loaders][11]
12. [MuffinMan: Setting Up Webpack, Babel & React from Scratch (Revisited)][12]
13. [StackOverflow: Loader Error in Webpack on JSX][13]
14. [Babel Forums: ES6 import .jsx without suffix/extension][14]
