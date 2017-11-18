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

These plugins add, orr extended functionality *(e.g. offline support, generating sitemaps, etc.)* or they extend Gatsby's webpack configurations. For this particular blog, avoiding page reloads and ability to dynamically change the `title` tag within head `tags` would be nice. The plugins `gatsby-plugin-catch-links` & `gatsby-plugin-react-helmet` will handle a lot of this added functionality. `gatsby-plugin-catch-links` handles the history `pushState` API and avoids page reloads when following links and instead loads on-demand rendered pages from the server or cache. `gatsby-plugin-react-helmet` uses [react-helmet][5] to modify `head` tags, and gatsby statically renders any of these `head` changes.




## References
[1]: https://www.gatsbyjs.org/blog/2017-07-19-creating-a-blog-with-gatsby/ "GatsbyJS Docs: Creating a Blog Tutorial"
[2]: https://www.gatsbyjs.org/docs/plugins/ "GatsbyJS Docs: Plugins"
[3]: http://mern.io/ "MERN Stack Official Site"
[4]: https://www.gatsbyjs.org/docs/plugins/#official-plugins "Offical GatsbyJS Plugins"
[5]: https://github.com/nfl/react-helmet "Github: React Helmet"

1. [GatsbyJS Docs: Creating a Blog Tutorial][1]
2. [GatsbyJS Docs: Plugins][2]
3. [MERN Stack Official Site][3]
4. [Offical GatsbyJS Plugins][4]
5. [Github: React Helmet][5]
