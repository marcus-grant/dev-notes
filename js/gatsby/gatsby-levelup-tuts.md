# Gatsby Notes
Notes on Using Gatsby

## EDITS
[ ] Rewrite as bullets
[ ] Make own file for just the leveluptuts notes

This is what I've learned from LevelUpTuts's [GatsbyJS Tutorial][1]

## Install Gatsby
First, `npm` is required, so make sure the local environment properly has `node` & `npm` installed. Then simply enter: `npm install --global gatsby-cli` into the command line to install the `gatsby-cli` package which manages gatsby through the command line.

## Create a Project
Enter, `gatsby new PROJECT_NAME` in the directory where the project is going to be worked on with a name replacing `PROJECT_NAME`. Then using node gatsby will create a functional boilerplate project. `cd` into the project and let's have a look. `gatsby develop` will start the development server on [http://localhost:8000](http://localhost:8000) with a very basic boilerplate version of the site. But hey, everything is working now and new stuff can be done to it with a mix of JS and HTML to render the page. Now let's go over how this works on a basic level by adding some stuff to this sparse site

## Files Overview
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

### layout/index.js
- The root javascript file for managing layout of the whole site. 
- Has some imports important to get a basic layout of the gatsby page
  - There is also a `./index.css` which is the entry point for all css being used
- The first component is a stateless functional component `Header`
  - Simply just returns a `div` with inlined styled components and a header doing the same
  - The divs with their inline styles can all be changed to be whatever you want them to later, as most programmers will do, they'll use css preprocessors or styled components instead
  - There's also a `TemplateWrapper`component which brings in `children` as `props` as it has to be able to wrap a template for the page
    - This highlights how easy it is to create pages, because if you just create a new page in `pages/` and give it a default export and importing it gives you the new page with the same style, react routing, and its generation from gatsby all in one go
    - If a new page in `pages/` is created and it gets exported and imported, going to that page should then should get you it generated.

## Plugins & SASS
Plugins will allow expanded capibility which cna be added by anyone with skills in the [MERN][2] stack of which this site is built. A basic overview with linked tutorials on how to use, modify & create plugins is listed in the [GatsbyJS Plugins Docs][3]. For now though, we're going to use the [gatsby-plugin-sass][4] plugin inorder to give this site sass styling capilities as they are a *much* better way to handle stylesheets. To download & configure it copy its name `gatsby-plugin-sass` and in the terminal enter `npm install --save gatsby-plugin-sass` or if using yarn `yarn add gatsby-plugin-sass`. It should now be installed once all its steps are gone through. Now let's go into the `gatsby-config.js` and add the plugin into the `plugins` section of the file as it doesn't do this automatically whenever npm gets used to add a plugin. **Note** the use of backticks instead of quotes, this is just to seperate string interpolation from code use, really though **it doesn't matter**.

Now we can go into the `src` folder and change the `index.css` to `index.scss`, change the import statement in `src/layout/index.js` from `import './index.css` to `import './index.scss`. If the page now gives errors, it might be necessary to stop the develop server and rerun it. Now the very same site styles should come back into play except they will be done by using sass. This is nice because in several other build environments like webpack or gulp several other configs and loaders will be needed to be set up. And because **SCSS** is a **S**uperset of css, all the premade css from the `gatsby new` command still functions as expected.

## Sourcing Data
### Configuring Gatsby for DataSources & Markdown Remark
**Rewrite for brevity**
Since one of the primary usages of Gatsby is to serve either blogs, or wikis, or any kind of content which can easily be written in basic markup like markdown, it's easily made to render these filetypes into web-pages. A lot of important functionality is hidden away in plugins since not every use case for gatsby requries rendering from a specific source file. To add this functionality, add `gatsby-source-filesystem`. Then go into `gatsby-config.js` to add its configuration. However, this configuration is a bit unique as it will have options that make it accessible as a query on the node server. In `gatsby-config.js` add an object instead of a string to plugins, these changes will be shown with the others made to this file like the code block a little bit below. So add an object with key, `resolve` and value `gatsby-source-filesystem`, like the plugin name. Then add another key, `options:` which has a value of another object which will contain the path to source static data, in this case `${__dirname}/src/pages`, and name it with key value pair `name: 'pages'`. This sets the options for how to not only add this plugin, but then also how this plugin works by specifying the path it uses to source files and giving that a name `pages`. The `${__dirname}` just tells it to use the server root location.

**EDIT** `name:` is to specify the page source look up for official explanation

Another useful plugin for a lot of blogs is called the `gatsby-transformer-remark`. This allows us to use markdown files as something that can be queried by graphQL so metadata can be queried. So as before, add the plugin using `npm`. Then add an entry into `gatsby-config.js`, this time with a simple line inside `plugins:` for the package name. All the new changes inside the `plugins: ` section of `gatsby-config.js` should now look like this:

`gatsby-config.js`:
```js
module.exports = {
  siteMetadata: {
    title: `Gatsby Default Starter`,
  },
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
    'gatsby-transformer-remark',
  ],
}
```

### Adding Markdown Data as a Post
- Data sources are looked for by gatsby inside of the `src/pages` folder and user-made sub-directories can be made in there to further organize how those pages are stored.
- You may for example want a seperate folder `images` for all images that need rendering
- To try it out, add a new folder inside this directory called `first-blog`
  - Then add a newfile within named `index.md` which will be the main markdown file for that first blog post.
  - This means that `first-blog` is then used to represent a single blog post

### Mardown Remark
- Markdown when using the remark plugin allows metadata to be inserted into the markdown file by using a syntax of enclosing `---` marks around the metadata in the front of the post
- This is known as **front matter**
  - It will typically have a property: `path:` which specifies the relative path to `src/pages`
  - ... and a property: `title: ` which will have the title of that specific article or post
- Anything below the 2nd `---` front matter mark will then be rendered like any normal markdown
- Below is a very basic version of `src/pages/first-blog/index.md`:
```md
---
path: '/first-post'
title: 'First Blog Post'
---

# Hello World!
```

## Templates
- Since most content created by static site generators or even CMS based sites will have largely the same repeated styles, templates are used to have a few set styles for each post
- Typically a `src/pages/templates/` folder is created to store said templates

### Creating the First Template
- Inside the `src/pages/templates/` directory add a `posts.js` file that will represent the template for how a post should look and behave
- In `posts.js` import:
  - `React from 'react'`
  - `Helmet from 'react-helmet'`
- Then export a `Template` function as the default
  - `Template({ data })` takes a `data` object as its one argument
  - `const {markdownRemark: post} = data;` deconstructs the `markdownRemark` from `data` and stores it in constant, `post`
  - `return`
    - a containing `div` block
    - let's only have the post title displayed for now as an `h1` tag
    - `<h1>{post.frontmatter.title}</h1>`
    - This will pull from the frontmatter the title of the post as the content of the `h1` tag
- That by itself isn't enough to pull data from the frontmatter on the server
- In order to pull data from the markdown stored on the server, a graphQL query needs to be created
  - export a const named `postQuerry` and set it equal to a `graphql` query with its enclosing backticks to form the query itself
  - the entire query along with the whole `post.js` template file is shown below
  - Note the use of a function backtick definition which saves the whole query as a string literal
    - first there is a query function defined `BlogPostByPath` which takes a path as a string
    - inside the query there will be a search of the path by frontmatter using `markdownRemark`
**NOTE** Edit this later when you have a better understanding of graphQL queries

`src/pages/templates/posts.js`:
```jsx
import React from 'react';
import Helmet from 'react-helmet';

export default function Template({data}){
  const {markdownRemark: post} = data;
  return (
    <div>
      <h1>{post.frontmatter.title}</h1>
    </div>
  )
}

export const postQuerry = graphql`
  query BlogPostByPath($path: String!){
    markdownRemark(frontmatter: { path: { eq: $path } }){
      html,
      frontmatter {
        path
        title
      }
    }
  }
`
```

## Creating Pages
- Now the query, template, and markdown are specified, what else is necessary to have these posts appear whenever a templated post is created?
- gatsby needs to be told what a page is, and so far a `post` hasn't been defined as a kind of page
- To define new page types, specify a JS file in the root of the project, `./gatsby-node.js`
  - The name is important because gatsby will look for it specifically
- This will be using the [createPages][5] API of Gatsby
- Inside add 


Then we will also want another plugin that *transform*s our data known as `gatsby-transformer-remark`, which again 
## References
[1]: https://www.youtube.com/watch?v=b2H7fWhQcdE "LevelUpTuts: GatsbyJS Tutorial"
[2]: http://mern.io/ "Mern Stack Page"
[3]: https://www.gatsbyjs.org/docs/plugins/ "GatsbyJS: Plugins Docs"
[4]: https://www.gatsbyjs.org/packages/gatsby-plugin-sass/ "GatsbyJS: Sass Plugin"
[5]: https://www.gatsbyjs.org/docs/creating-and-modifying-pages/ "GatsbyJS: Create Pages"

1. [GatsbyJS LevelUpTuts Video Tutorials on Youtube][1]
2. [MERN.io: MERN Stack Overview][2]
3. [GatsbyJS: Plugins Documentation][3]
4. [GatsbyJS: Sass Plugin Documentation][4]
5. [GatsbyJS: Creating & Modifying Pages Documentation][5]
