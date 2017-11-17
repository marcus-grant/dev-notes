# Gatsby Notes
Notes on Using Gatsby

## LevelUpTuts Tutorial
This is what I've learned from LevelUpTuts's [GatsbyJS Tutorial][1]

### Install Gatsby
First, `npm` is required, so make sure the local environment properly has `node` & `npm` installed. Then simply enter: `npm install --global gatsby-cli` into the command line to install the `gatsby-cli` package which manages gatsby through the command line.

### Create a Project
Enter, `gatsby new PROJECT_NAME` in the directory where the project is going to be worked on with a name replacing `PROJECT_NAME`. Then using node gatsby will create a functional boilerplate project. `cd` into the project and let's have a look. `gatsby develop` will start the development server on [http://localhost:8000](http://localhost:8000) with a very basic boilerplate version of the site. But hey, everything is working now and new stuff can be done to it with a mix of JS and HTML to render the page. Now let's go over how this works on a basic level by adding some stuff to this sparse site

### Files Overview
- `node_modules`: as expected from a node project, don't touch it in most cases
- `public`: which is where our generated site and its computed components go to
- `src`: the source code folder which gets used to generate the site
  - `src/pages` the source files used to create pages
  - `pages/layout` the layout source files helped to lay things out
    - `index.css` contains the main css used
    - `index.js` the root javascript file used 
- `.gitignore` which just has a few useful gitignores for node projects and a gatsby project specifically so it doesn't update with git like the `pulic` folder
- `gatsy-config.js` which is there to handle the global configs and metadata of the site, and its plugins
  - `gatsby-plugin-react-helmet` is already pre-generated there which helps handle how react gets generated into static site stuff
- `package.json` is the standard `npm` stuff that's needed for the node server and its dependencies for the project and scripts like `build`, `develop`(just used), `format`, `test`
- `README`: the pre-generated README file for the project

#### layout/index.js
- The root javascript file for managing layout of the whole site. 
- Has some imports important to get a basic layout of the gatsby page
  - There is also a `./index.css` which is the entry point for all css being used
- The first component is a stateless functional component `Header`
  - Simply just returns a `div` with inlined styled components and a header doing the same
  - The divs with their inline styles can all be changed to be whatever you want them to later, as most programmers will do, they'll use css preprocessors or styled components instead
  - There's also a `TemplateWrapper`component which brings in `children` as `props` as it has to be able to wrap a template for the page
    - This highlights how easy it is to create pages, because if you just create a new page in `pages/` and give it a default export and importing it gives you the new page with the same style, react routing, and its generation from gatsby all in one go
    - If a new page in `pages/` is created and it gets exported and imported, going to that page should then should get you it generated.

### Plugins & SASS
Plugins will allow expanded capibility which cna be added by anyone with skills in the [MERN][2] stack of which this site is built. A basic overview with linked tutorials on how to use, modify & create plugins is listed in the [GatsbyJS Plugins Docs][3]. For now though, we're going to use the [gatsby-plugin-sass][4] plugin inorder to give this site sass styling capilities as they are a *much* better way to handle stylesheets. To download & configure it copy its name `gatsby-plugin-sass` and in the terminal enter `npm install --save gatsby-plugin-sass` or if using yarn `yarn add gatsby-plugin-sass`. It should now be installed once all its steps are gone through. Now let's go into the `gatsby-config.js` and add the plugin into the `plugins` section of the file as it doesn't do this automatically whenever npm gets used to add a plugin. **Note** the use of backticks instead of quotes, this is just to seperate string interpolation from code use, really though **it doesn't matter**.

Now we can go into the `src` folder and change the `index.css` to `index.scss`, change the import statement in `src/layout/index.js` from `import './index.css` to `import './index.scss`. If the page now gives errors, it might be necessary to stop the develop server and rerun it. Now the very same site styles should come back into play except they will be done by using sass. This is nice because in several other build environments like webpack or gulp several other configs and loaders will be needed to be set up. And because **SCSS** is a **S**uperset of css, all the premade css from the `gatsby new` command still functions as expected.

### Sourcing Data
Since one of the primary usages of Gatsby is to serve either blogs, or wikis, or any kind of content which can easily be written in basic markup like markdown, it's easily made to render these filetypes into web-pages. A lot of important functionality is hidden away in plugins since not every use case for gatsby requries rendering from a specific source file. To add this functionality, add `gatsby-source-filesystem`. Then go into `gatsby-config.js` to add its configuration. However, this configuration is a bit unique as it will have options that make it accessible as a query on the node server. In `gatsby-config.js` add an object instead of a string to plugins, like below:
`gatsby-config.js`:
```js
  // ...
  plugins: [
    `gatsby-plugin-react-helmet`,
    `gatsby-plugin-sass`,
    {
      resolve: 'gatsby-source-filesystem',
      options: {
        path: `${__dirname}/src/pages`,
        name: 'pages',
      },
    },
```
***Rewrite from  tutorial 4***
Transform gets used a lot to transform tihngs like metadata and info etc
,t

Then we will also want another plugin that *transform*s our data known as `gatsby-transformer-remark`, which again 
## References
[1]: https://www.youtube.com/watch?v=b2H7fWhQcdE "LevelUpTuts: GatsbyJS Tutorial"
[2]: http://mern.io/ "Mern Stack Page"
[3]: https://www.gatsbyjs.org/docs/plugins/ "GatsbyJS: Plugins Docs"
[4]: https://www.gatsbyjs.org/packages/gatsby-plugin-sass/ "GatsbyJS: Sass Plugin"

1. [GatsbyJS LevelUpTuts Video Tutorials on Youtube][1]
2. [MERN.io: MERN Stack Overview][2]
3. [GatsbyJS: Plugins Documentation][3]
4. [GatsbyJS: Sass Plugin Documentation][4]
