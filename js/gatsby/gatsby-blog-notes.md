# Create a Blog Using Gatsby
Notes predominantly based off going through GatsbyJS's blog [post][1] about creating blogs using GatsbyJS

> Gatsby is an incredible static site generator that allows for React to be used as the underlying rendering engine to scaffold out a static site that truly has all the benefits expected in a modern web application. It does this by rendering dynamic React components into static HTML content via server side rendering at build time. This means that your users get all the benefits of a static site such as the ability to work without JavaScript, search engine friendliness, speedy load times, etc. without losing the dynamism and interactivity that is expected of the modern web. Once rendered to static HTML, client-site React/JavaScript can take over (if creating stateful components or logic in componentDidMount) and add dynamism to the statically generated content. Gatsby recently released a v1.0.0 with a bunch of new features, including (but not limited to) the ability to create content queries with GraphQL, integration with various CMSs—including WordPress, Contentful, Drupal, etc., and route based code splitting to keep the end-user experience as snappy as possible. In this post, we’ll take a deep dive into Gatsby and some of these new features by creating a static blog. Let’s get on it!

## Adding Important Plugins for Blogs

Gatsby has a ton of useful [plugins][2] that they maintain themselves, and even more if stability or ease of use is less important. The whole thing is written on the [MERN Stack][3] so anyone with skills in that stack can create their own additions to Gatsby. Check out their [official plugins][4] to see if there's something particularly interesting.

### Using Plugins
*An overview of how the plugin system works - skip if already familiar*

Plugins are just Node.js packages that use Gatsby's APIs. A lot of things that typically get bundled in with frameworks and libraries like gatsby are already included, but in Gatsby to make the project better fit its specific needs they are seperated out into seperate plugins. [Here][4] is a list of the offical plugins.

#### How to Use Plugins?

Since the plugins are just node packages, just use either NPM or yarn to install them. For example, `npm install --save gatsby-transform-json` will add the plugin for providing JSON data-layer functionality for the gatsby server. Then in the project's `gatsby-config.js` in the project root directory simply add `gatsby-transformer-json` to the plugins array like so:

`gatsby-config.js`:
```js
module.exports = {
  plugins: [
    `gatsby-transformer-json`,
  ],
}
```

**NOTE**: Some plugins will need extra options specified in the `gatsby-config.js` file to work properly, so be sure to read the plugin page for each plugin before use.

#### Creating Your Own Plugins

If creating a new plugin for use in other gatsby projects, or a single-use one specific to a single site, it's possible to create new plugins. Start by creating a `plugins` directory at the root of the project, *if there isn't already one there.* Custom plugins will still need to be added to the `gatsby-config.js`, just like any official plugin. A project or site's root directory should have a `plugins` directory resembling the below filetree.

```
plugins
└── my-own-plugin
    ├── gatsby-node.js
    └── package.json
```

Each plugin needs a `package.json` even though the minimum contents of the file can be just an empty JS object `{}`. The `name` & `version` fields in `package.json` are read from the package file. The name is used by GraphQL data structures when they mutate and version is used to clear the cache when it changes.

For *local-only* plugins it's best to leave the version field empty. Gatsby will automatically md5-hash it from the contents of all gatsby-\* file contents. If the name is empty, it's inferred from the plugin folder name. It's **important** to note that all gatsby-\* files are ignored by the babel compiler, so if creating these plugins using ES6 or other non-supported syntax by the local version of node; they will need to be placed in a `src` subfolder and be built into the plugin folder root.

### Adding Necessary Plugins to a Gatsby Blog

To make this gatsby site a nice blog, some plugins will be needed. These can be categorized as: **functional**, **source**, and **transformer** plugins.

#### Functional Plugins

**NOTE** *check on fresh system if react-helmet is needed as dependency to download*

These plugins add, or extend functionality *(e.g. offline support, generating sitemaps, etc.)* or they modify Gatsby's webpack configurations. For this particular blog, avoiding page reloads and an ability to dynamically change the `title` tag within head `tags` would be nice. The plugins `gatsby-plugin-catch-links` & `gatsby-plugin-react-helmet` will handle this added functionality and then some. `gatsby-plugin-catch-links` handles the history `pushState` API and avoids page reloads when following links and instead loads on-demand rendered pages from the server or cache. `gatsby-plugin-react-helmet` uses [react-helmet][5] to modify `head` tags, and gatsby statically renders any of these `head` changes. ***Note*** *newer versions of gatsby will already have gatsby-plugins-react-helmet installed*.

Install by using either yarn or npm like so: `yarn add gatsby-plugin-catch-links` or `npm install --save gatsby-plugins-catch-links`. And then add the plugin to `gatsby-config.js` like below.

`gatsby-config.js`:
```js
module.exports = {
  siteMetadata: {
    title: `Your Name - Blog`,
    author: `Your Name`,
  },
  plugins: [
    'gatsby-plugin-catch-links',
    'gatsby-plugin-react-helmet',
  ],
}
```

Now, `gatsby develop` should succesfully load the page like it did before, but with the new plugin. All that needed to be done was to add the package with either npm or yarn, and to edit the `gatsby-config.js` file.

#### Source Plugins

Source plugins work on Gatsby's [Node Interface][6] to create *nodes* that get transformed into a usable format by a transformer plugin. From the link on the Node Interface:

> The “node” is the center of Gatsby’s data system. All data that’s added to Gatsby is modeled using nodes.

A typical workflow for a site that doesn't source all its data from the internet, *(think Wordpress, Netflify, etc)*, will need to source file from the local filesystem. To do this a source plugin, `gatsby-source-filesystem` is needed.

For this site, since the bulk of the blog's content and each article, is to be authored in markdown files, add `gatsby-source-filesystem` using yarn or npm like before. Then modify the `gatsby-config.js` file like this.

`gatsby-config.js`


To install these plugins, as the previous section explained in detail, run `yarn add gatsby-source-filesystem` or `npm install --save gatsby-source-filesystem`. Do the same for `gatsby-plugin-catch-links`, but this plugin only needs the name of the plugin in the plugins array. *In newer versions helmet is already installed when creating the project directory, so no need to add it*. Then change `gatsby-config.js`'s plugin array to look like the below snippet.

Finally, `gatsby-source-filesystem` is used to make gatsby capable of sourcing static data like markdown to be rendered into pages.
`gatsby-config.js`:
```js
module.exports = {
  // previous configuration
  plugins: [
    'gatsby-plugin-catch-links',
    'gatsby-plugin-react-helmet',
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/pages`,
        name: 'pages',
      },
    }
  ]
}
```

Specifying an object, instead of a string in the plugins array is for when a plugin requires some extra options to work. In this case the `gatsby-source-filesystem` plugin needs to be resolved to its plugin name, and then given an options object that specifies that `path` & `name` to locate then to name them.

#### Transformer Plugins

With a source in place (Markdown, JSON, YAML, etc.), transformers as they suggest transform the source data which is inherrently unusable by itself when rendering to a webpage. They need to be be transformed into understandable HTML, CSS and JS that can be queried against by GraphQL. The `gatsby-source-filesystem` loads the files from disk and `gatsby-transormer-remark` uses the [remark][7] markdown processor which is highly extensible and useful for this regard for its ability to transform to react and to use front matter within the markdown files themselves. 

To install, simply use yarn or npm to add `gatsby-transformer-remark` by either entering: `yarn add gatsby-transformer-remark` using yarn or with npm `npm install --save gatsby-transformer-remark`. Then edit the `gatsby-config.js` file, this time again with special options for the plugin, specified as an object. 

`gatsby-config.js`
```js
module.exports = {
  // previous setup
    plugins: [
    'gatsby-plugin-catch-links',
    'gatsby-plugin-react-helmet',
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/pages`,
        name: 'pages',
      },
    },
    {
      resolve: 'gatsby-transformer-remark',
      options: {
        plugins: [] // just in case those previously mentioned remark plugins sound cool :)
      }
    },
  ]
};
```

Now, finally all the plugins should be sorted, and that will be verified with a quick `gatsby develop` and before that closing the server to make sure the changes have taken effect.

## Writing the first Markdown Blog Post

Because of how `gatsby-source-filesystem` has been configured, it expects content to be stored inside `src/pages`, so let's put in the first blog post!

Though gatsby is pretty unopinionated about how to store blog posts (so long as it makes sense to the plugin configurations specified). It is convention however, to name posts using this convention: `MM-DD-YYYY-title` or `YYYY-MM-DD-title` for the non-Americans. So create a new folder of path, `src/pages/2017-11-27-hello-world` to get started, then create an `index.md` file inside it. Then just copy the text below, save it and check your [http://localhost:8000][20] dev server and see what's happened.

`src/pages/2017-11-24-hello-world/index.md`:
```md
---
path: "/hello-world"
date: "2017-07-12T17:12:33.962Z"
title: "My First Gatsby Post"
---

Oooooh-weeee, my first blog post!
```

Unfortunately, nothing new has rendered, but hopefully no browser errors are reported either. This is because only the data source has been added, but gatsby doesn't know how it's going to render it yet *(e.g. style, template, extra content like images, etc.)*. And what's with the `---` marks in the markdown file? This is known as **frontmatter** and it's what will be used to provide extra metadata for the blogpost that Gatsby can then use inject React components with specified data, *(e.g. path, date, title, etc.)*. Any kind of metadata can be specified to alter how it is handled so later, be sure to experiment with it.

## Creating a (React) Template

Because Gatsby supports server side rendering to React components, that's how in this tutorial they will be written. First, create a file *(and the src/templates folder if it doesn't exist)* like below:

`src/templates/blog-post.js`:
```js


import React from 'react';
import Helmet from 'react-helmet';

// import '../css/blog-post.css'; // make it pretty!

export default function Template({
  data // this prop will be injected by the GraphQL query we'll write in a bit
}) {
  const { markdownRemark: post } = data; // data.markdownRemark holds our post data
  return (
    <div className="blog-post-container">
      <Helmet title={`Your Blog Name - ${post.frontmatter.title}`} />
      <div className="blog-post">
        <h1>{post.frontmatter.title}</h1>
        <div className="blog-post-content" dangerouslySetInnerHTML={{ __html: post.html }} />
      </div>
    </div>
  );
}
```

This just creates a React template for blog posts, with some soon to be explained magic. Basically it just takes `helmet` & `react` to return a some `div`s containing the `helmet` metadata for title, which fetches from the markdown frontmatter, `post.frontmatter.title`, and returns the contents of a blog post inside `post.html`.

### Writing the Blog Post GraphQL Query

Inside the same template definition for blog posts, a GraphQL query will be needed to get the meta data for the blog post so that it can be used to render the page using it. Each piece of data that the query gets will be stored via the `data` property.

`src/templates/blog-post.js`
```jsx
export const pageQuery = graphql`
  query BlogPostByPath($path: String!) {
      markdownRemark(frontmatter: { path: { eq: $path } }) {
		html
        frontmatter {
		  date(formatString: "MMMM DD, YYYY")
		  path
		  title
        }
	  }
	}
`;
```

GraphQL can be confusing at first but it's an incredibly powerful way to integrate server-client communication, database queries and data serialization/deserialization.

*Note that if this is the first time learning about GraphQL, consider going to [this][8] source to learn more, either before continuing or after finishing the blog tutorial.*

There's a lot of depth to that query so let's look at it. `BlogPostByPath` is the query name that becomes exportable whenever blog post data is needed. **Note** that these names for queries should always be unique. The `markdownRemark` function is used to gather all the queried blog data. Inside of that, `frontmatter` is being supplied by the `$path` variable which simply pulls the data path of a given article. In the example post, `/hello-world` becomes the `$path` if that gets queried. 

`markdownRemark` is then used to is the property inserted via the prop, `data`, wihtin the defined GraphQL query *(the backtick block)*. HTML, for example, the transformed HTML is acquired through `data.markdown.html`.

GraphQL queries like these take place during the build time. The component is injected with the `data` prop seeded by the GraphQL query. Unless front-end accesses to the server are created, all that gets rendered happens at build time as HTML via React, GraphQL and Gatsby.

## Creating the Static Pages

Most of the [Node API][6] is exposed by changing the `gatsby-node.js` file. Each export within that file is parsed by gatsby to determine how the API gets used as per [these][9] API specifications. For now, just the `createPages` API will do. 

`gatsby-node.js`:
```js
const path = require('path');

exports.createPages = ({ boundActionCreators, graphql }) => {
  const { createPage } = boundActionCreators;

  const blogPostTemplate = path.resolve(`src/templates/blog-post.js`);
}
```

Here, `createPages` API, which Gatsby calls automatically, if exported by `boundActionCreators` *(a Gatsby object with API calls)* will grab the *path* to the blog specified by the source plugins so it now creates pages using `createPage` and `graphQL`

### Querying for Posts

`gatsby-node.js`:
```js
{
// previous gatsby-node export
// new query below
return graphql(`{
  allMarkdownRemark(
	sort: { order: DESC, fields: [frontmatter___date] }
	limit: 1000
  ) {
	edges {
	  node {
		excerpt(pruneLength: 250)
		html
		id
		frontmatter {
		  date
		  path
		  title
		}
	  }
	}
  }
}`)
	.then(result => {
	  if (result.errors) {
		return Promise.reject(result.errors);
	  }
	});
}
```

Here, a GraphQL query is added to `gatsby-node.js`. It is used to getch all markdown nodes under the reference, `allMarkdownRemark` as a GraphQL property. For each `edge` available on the server database it will look at each of them as specified by the `node` property. Each `node` property is programmed to expect an `excerpt` which is trimmed to 250 characters, `html`, for the generated HTML, `id` as a unique identified, and its `frontmatter` which has been specified before, but needs to be declared for the server. **Note** that `exports.createPages` expects a Promise callback to be returned to ensure that the query works, so at the end it checks if any of the callbacks were errors and handles them. Also **note** that the `gatsby-plugin-remark` has exposed some useful data to query like the `excerpt` property which is basically a short preview snippet of the post.

The queries should now be ready. It's time to create the pages with the `createPage` action creator.

### Creating the Pages

`gatsby-node.js`:
```js
return graphql(`{
  allMarkdownRemark(
	sort: { order: DESC, fields: [frontmatter___date] }
	limit: 1000
  ) {
	edges {
	  node {
		excerpt(pruneLength: 250)
		html
		id
		frontmatter {
		  date
		  path
		  title
		}
	  }
	}
  }
}`)
	.then(result => {
	  if (result.errors) {
		return Promise.reject(result.errors);
	  }
	});
	// Add to the query chain a handler forEach node
	result.data.allMarkdownRemark.edges
	  .forEach(({ node }) => {
		createPage({
		  path: node.frontmatter.path,
		  component: blogPostTemplate,
		  context: {} // additional data can be passed here
	  });
	});
}
```

The `Promise` chain from before exposes all the edges containing blog posts accessible through `result.data.allMarkdownRemark.edges`. Each edge is structured with a node, that is itself structured to hold all of the data necessary to `createPage`s. **Note** it is very important that the structure of the GraphQL query matches that of the nodes defined by the respective plugins or custom code that gets used to fetch node data.

`createPage` accepts an object which requries the `path` & `component` properties to be defined, which is done above. Optionally, the property `context` is used to inject data to make it available to the blog post template component. **Note** this can be useful to `console.log` inorder to better understand the data model at play here. Every time Gatsby builds, `createPage` is called, which then along with other Gatsby APIs and internal code to create a static HTML file of the `path` specified in a post's `frontmatter`. This results in a serialized and parsed string of its React template injected with the data from the GraphQL query. Finally! Things are visibly coming together!

Running, `yarn develop` and going to [http://localhost:8000/hello-world][21] will now finally show the first post created, "Hello World!".




## References
[1]: https://www.gatsbyjs.org/blog/2017-07-19-creating-a-blog-with-gatsby/ "GatsbyJS Docs: Creating a Blog Tutorial"
[2]: https://www.gatsbyjs.org/docs/plugins/ "GatsbyJS Docs: Plugins"
[3]: http://mern.io/ "MERN Stack Official Site"
[4]: https://www.gatsbyjs.org/docs/plugins/#official-plugins "Offical GatsbyJS Plugins"
[5]: https://github.com/nfl/react-helmet "Github: React Helmet"
[6]: https://www.gatsbyjs.org/docs/node-interface/ "GatsbyJS: Node Interface"
[7]: https://github.com/wooorm/remark "Github: wooorm/remark"
[8]: https://www.howtographql.com/ "How to GraphQL Tutorials"
[9]: https://www.gatsbyjs.org/docs/node-apis/ "Gatsby: Node API Specifications"
[20]: http://localhost:8000 "Dev Server Address"
[21]: http://localhost:8000/hello-world "Dev Server, User Created Post"

1. [GatsbyJS Docs: Creating a Blog Tutorial][1]
2. [GatsbyJS Docs: Plugins][2]
3. [MERN Stack Official Site][3]
4. [GatsbyJS Docs: Offical Plugins][4]
5. [Github: React Helmet][5]
6. [GatsbyJS: Node Interface][6]
7. [Github: Remark][7]
8. [HowTo GraphQL Tutorials][8]
9. [Gatsby: Node API Specifications][9]
