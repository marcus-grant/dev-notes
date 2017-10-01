# React Notes on Creating Styling, Layout, Sizing, & Color
**Note** this will for now correspond with the udemy course on meteor's coverage on the topic and then later bits and pieces of other works

## SCSS & SASS
- Meteor requries a specific atmpphere package to work with extended CSS types
- Perhaps a bit irritating due to the strict semantics of meteor's structure, but when handled correctly it makes the whole process incredibly seamless
- Install SCSS & Sass support with atmosphere package, `meteor add  fourseven:scss`
  - `meteor add <package>@<version>` is the general form for adding meteor packages
- The only real difference between SCSS & SASS is, particularly how classes are deliminited
- The same file structure is recognized with the main styling applied in `client/main.css`
- The only thing that really changes to extend to **SCSS** is to change `client/main.css` to `client/main.scss`
- To test this out, let's make a REALLY ugly styling feature
`client/main.scss`
```scss
$red: red;

* {
  color: $red;
}
```
- If meteor is running, and the above is saved into `client/main.scss` then this even uglier version of the app should come up.
  - If not, then likely an error is also associated because this syntax doesn't work in regular CSS
![ugly red scss version of the app's style](./red-scss.png) 

### CSS Reset & Header Styling
- Resets are used to essentially clear any default styling settings that might already exist within the browser or the project's included css files
- A basic reset for the `margin` & `padding` attributes looks like this:
```scss
* {
    margin: 0;
    padding: 0;
  }
```
- With the wildcard selector appearing first `*`, which ensures that before any new styling is applied, the reset occurs
- Then you can add new attributes like:
```scss
body {
  color: #555555;
  font-family: Helvetica, Arial, sans-serif;
}
```
- **className** is useful here because it will allow for specific styling based off of *React-based* classes instead of standard html ones
```scss
.title-bar {
  background: #e35557;
}
```
- Above is a class definition of associated attributes, specifically a background bound to the class `title-bar`
- By switching to the React class `TitleBar` and applying to its root return `<div>` element the property `className="title-bar"` this will now be applied
`imports/ui/TitleBar.js`
```js
  render() {
    return (
      <div className="title-bar">
        <h1>{this.props.title}</h1>
      </div>
    );
  }
```
- this leads to...
![red titblebar using classNames](./classname-red-title.png)
- Now it's easy to add all sorts of changes for all title-bar appearance attributes, like perhaps `color: #ffffff;` to make a better text color *white* for the title bar

### Relative Sizing
- To change the font size of the `h1` tag, it's possible to use `px` as the unit, but it might be better to set everything to size dynamically based off of one root component
- This sizing is known as `rem` and it stands for **Root ?SOMETHING** and it's relative to the size of the root element, being the `html` element font size
- To set the root size,
```scss
html {
  font-size: 62.5%;
}
```
- This will set the new size point from the default 16px to 16px * 62.5% = 10px

### Wrapping
- A good standard abstract styling technique to use is called *wrapping*.
- Wrappers are invisible style elements that contain elements within some kind of bounded environment that is consistent with other elements.
- For example, with a titlebar or header, it looks much nicer if the items below the item bar follows the same horizontal position as the items within
- To create a basic wrapper...
`client/main.scss`
```scss
.wrapper {
  margin: 0 auto;
```
- `0 auto;` will set the top & bottom margin to 0 and left/right to auto
- `max-width` helps determine the auto margins from above, by resitricting them to their width
- **Remember**, it's easy to forget to give `className`s to newly defined css selectors or classes
- It can be good to give put a wrapper inside parent components so it doesn't apply layout to eveyrhing
```js
render() {
  return (
    <div className="title-bar">
      <div className="wrapper">
        <h1>{this.props.title}</h1>
      </div>
    </div>
  );
}
```
- If wrapper were the root dive here, the red titlebar would only layout for 60rem of width which would look odd

### Style Imports
- Just like how jsx files can be imported to break up functionality into seperate parts that can individually be included, so too can SCSS & SASS
- `wrapper` & `title-bar` would make good candidates to split into individual style files
- meteor has a canononical place for these in `imports/client/styles/`
- it is common practice to make partial pieces to a pre-existing stylesheet have underscores before the name, like `client/_main.scss` 
- To import, say in the `client/main.scss`, the partial just created `imports/client/styles/_main.scss`, just use `@import '../imports/client/styles';`

### BEM - Block, Element, Modifier Conventions
- A very useful naming convention for style classes that has emerged is known as the **BEM** convention
- **Block**
  - A semantic type in the **BEM** convention that defines any larger collection of blocks or higher DOM level pieces used as the least granular identifier for the BEM scheme.
  - Think headers, containers, menus, input, abstract react classes that contain many others components subordinate to it
  - Denoted by its name only
- **Element** a subordinate part to a **block** that has no standalone meaning and is semantically tied to its **block**
  - think menu item, list item, checkbox caption, header title, error message, etc
  - denoted by underscores seperating it from the block name it belongs to
- **Modifier** a flag on a block or element
  - Use them to change appearance or behavior of a **block** or **element**
  - denoted by double dashes `BLOCKNAME__ELEMENTNAME--MODIFIERNAME`
  - ie. disabled, highlighted, checked, fixed, big, color yellow
- Using this convention to name and organize style properties into classes and subclasses can be very useful for the sake of maintainability, scalibitlity and to easily fix specific scopes of a style
- **TODO** add specific examples with animations in SCSS

### Flexbox
- Makes it easy to setup grid layouts that adjust to screen size
- Also helps to create proper spacing and layouts despite the container size

## 3rd Party React Components
- Sometimes saves time when standard commonly reused components are required
- How to get react to **responds** better to mobile view ports `flipmove` 
- First let's figure out how to make...

### React Handle Passed JSX
- React Components can just as easily be passed JSX into it, like many normal HTML elements can, but without definining within React Components how these are to be handled, nothing gets done with them and they never become rendered
- `{this.props.children}`
  - This is how to reference a components' instance's passed in html or react components
- This is important when using 3rd party components because they will use that `.children` reference
- `react-flip-move` is a nice way to make the player list flip and move to reorder themselves whenever the ranking changes between players
- install with `meteor npm install react-flip-move@2.7.2`

## Dynamic SCSS with ES6
- A very nifty feature, this allows to inject into the ES6 code different `className` values based off of computed values
- In the below incomplete snippet of a React component a string is generated using the `$` operator and the js evaluation block `{}` to generate a `className` string that is based on a computed value
`imports/ui/Player.js`
```js
// ...
let itemClassName = 'item item--position-${this.props.player.position}';
return (
  <div key={this.props.player._id} className={itemClassName}>
{/* ... */}
```

## SASS Functions
- SASS & SCSS support functions being used to calculate values
- Variables can be defined **TODO** put variables somewhere **END** using `$green`
  - `$` operator is used to both define and reference variables
- Functions can then be used to define these variables
- There are *tons* of variables pre-defined in SASS, like `darken()` below which is used to darken green for a border
```SASS
$green: #e4ede0;
$green-alt: darken($green, 15%);
```

