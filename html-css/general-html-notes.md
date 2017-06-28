# General HTML notes
Unorganized collection of notes as I get better at full-stack and frontend development

## Headers
- `h1-h6` are all headers of decreasing rank, `h1` > `h2`
- `h1` is generally used as the main heading, and `h2` for subheadings and so on

## Pararaph elements

- Block text or **p**aragraph elements are designated by the `<p>` & `</p>` tags
- represent normal sized text
- Separately handled as formatting to distinguish what the majority of long text should look like

## Comments

- `<!--` opens a comment
- and close it with `-->`

## In-Line Style Properties on HTML Elements
- Each element ( *tagged items* ) can be given different properties than the default ones
- This is done using the `style=` specifier inside an HTML element's opening tag
- One such specifier is the element's style's color
- Putting all that together it's possible to change the color of an element, by altering it like so: `<h1 style="color: blue">I've Got the Blues</h1>`.
- These properties can all be applied invividually on each HTML tag of each HTML element, or...

## CSS Selectors Instead of Invividual Style Properties
- CSS is added just for the purpose of reducing the busy work of defining properties such as style on a web page. A signle page could have hundreds of different elements, and if each one had to have its tag specified it would take forever to build a site, let alone trying different looks out.
- Instead **selectors** are used; either at start of html, or in seperate CSS file which defines the properties each element will have.
- When putting selectors inside an HTML file they would look like so:
```html
<style>
  h1 {color: red;}
  p {color: dark-grey;}
</style>
…
```
- Don't forget the semi-colon at the end of each specifier
- A **selector** essentially just selects the element it wants to give properties to

## CSS Classes
- Instead of giving every element of the same kind only one possible set of properties by using selectors, **classes** can be defined within a `<style>` block or CSS file instead.
- Each class has its own set of properties that elements can be made to adhere to if they are declared as a member of that style class.
- Style classes are defined beginning with a `.` immediately followed by a class name, opening bracket, properties, and a closing bracket. Like below:
```css
<style>
  .blue-text
  {
    color: blue;
  }
  .red-text
  {
    color: red;
  }
</style>
```

- Again, don't forget the `;` at end of each property
- Also, the `.` operator seperates selector on the left, which can be blank if it's generic and doesn't need to be specific to a selector. On the right will be the class name.
- A style sheet follows the below syntax and pattern, remember the selector can be blank

```css
<style>
  selector.class-name;
  {
    property1: value1;
    property2: value2;
    ...
    propertyn: valuen:
  }
</style>
```
- Multiple element attributes can be called on HTML tags by seperating them with a space like below
  - `<p class="class1 class2">`
  - **TODO** how are conflicts handled if one of the shared properties of each class are attempted to be applied on the same tag?



## CSS Properties
- `font-size` is a CSS property to define text size of any element involving text size (most)
- `font-family` specifies a prioritized list of fonts based on the browser's configuration usually using web-safe fonts of one or more
  - common font families: `monospace`, `serif`, `sans-serif`, `cursive`, `fantasy`, `system-ui`
- It is possible to specifically define the primary font of a family name within a stylesheet, the below examples are valid, below that invalid
```css
font-family Gill Sans Extrabold, sans-serif;
font-family: "Goudy Bookletter 1911", sans-serif
```
- **Google Fonts** can be requested by a stylesheet, or any other valid supplier of fonts, which will send the font glyphs to the browser where the markup is applied and load them. Ideally, they already have those fonts cached or installed into their browser, and that http fetch request simply pulls from the local cache instead of downloading those glyphs.
  - To do so, create an html link tag that looks at the appropriate google font link, and formats it appropriately to the file that's about to receive it
  - For example, here a style sheet receives the *Lobster* font: `<link href="https://fonts.googleapis.com/css?family=Lobster" rel="stylesheet" type="text/css">`
  - With that link, in the stylesheet, and set by the `rel="stylesheet"` `type="text/css` now the sheet has access to it by name, so now it's possible to just enter `lobster` as the value for the `font-family` property
- Fonts can be **downgraded** in case some fonts are for whatever reason inaccessible
  - Do this by making an ordered list of property values representing font names under a font-family propery like below, where Helvetica is the preferred font, but can be **downgraded** to the standard Sans-Serif
  - `p { font-family: Helvetica, Sans-Serif; } `
- `width`: discussed below
- `height`: discussed below
- `border-*`: discussed below, each application of the `style` value will be different based on the element
  - `border-color`, `border-width`, `border-style`

## Image Elements/Tags
- Images are included in sites through marking up using the `img` element and pointing it to the specific image's URL by using the `src` attribute
- `<img src="https://www.some-image.com/meme1.jpg">`
- All images **must** have an `alt` attribute
  - Should an image load fail, or the screen reader be needed, the alt fills it in with placeholder text
  - `<img src="https://some-pic.com/my-img.jpg" alt="You hopefully won't be seeing this">
- Most use cases of the `img` element will work with self closing tags, ie *they don't need a closing tag*
  - Intuitive, because the image opening tag contains all the information the markup needs, everything else would be specified in stylesheets
  - **NOTE** I tried a long string for the `alt` attribute and it didn't work until I reduced its length
- **Image Size** is defineed in several ways:
  - `width` as a property, controls an element's width and its values can be many things, but `px` is the most common unit
  - For example, a set of classes below *.large-image* and *.small-image* each have their own widths
```css
<style>
  .larger-image {
    width: 500px;
  } .smaller-image {
    width: 100px;
  }
</style>
```
  - due to browser implementations, sometimes these properties get wonky when zoom levels aren't @ 100%

## Border Properties
- `border-color`, `border-width`, `border-style` are all common properties to borders
- `border-radius` defines *how rounded* each border is, `px` & `%` are both valid values, `%` is in relation to the parent element, so a value of `50%` would make for a circular image, or other square element


## Anchor Elements
- `a` elements represent **anchors** which are links to external elements of the page
- Anchors need closing tags
- Below is an image of an anchor nested inside a `p` tag to represent an external link as an anchor
![anchor-diagram](https://i.imgur.com/hviuZwe.png "FreeCodeCamp's diagram showing how to use anchors")
- **NOTE on nesting** nesting open/close tags within another can be done to combine both tags in a union around a specific portion of one part of the parent tag, like nesting an anchor inside a `<p>text <a href="blah.com">text</a></p>` p tag to make part of the text a link
- Sometimes **dead links** can come in handy as place holders while developing the site, or for changing the behaviour using javascript
  - Do this by replacing the value of the `a` tag's `href` property with a `#` symbol to mark it as dead
- **Turn an image into a link** link by nest an `img` tag inside an `a` tag
  - **Note** this wouldn't work vice versa, because an `a` tag needs an open & close 
    - **TODO** verify

## Lists
- **Bulleted Unordered Lists** is a special tag `<ul>` ( *UnorderedList* ) which encloses a set of `<li>`( *list item* ) representing list items
```html
<ul>
  <li>milk</li>
  <li>cheese</li>
</ul>
```
- **Ordered Lists** using `<ol>` tags work the same way, but will markup numbered lists

## Text Fields
- Uses tag of name `input`
- Good way to make forms
- Ex: `<input type="text">
- Self-closing tag
- Place holder text is defined by attribute `placeholder` and specifies the text that appears before any text is entered
- **Web Forms** are like text fields, but send back data to the server by using the `form` element
- Ex: `<form action="/url-to-submit-data"></form>`
- The `action` attribute defines where the data goes
- Nest a text field within a form to give it the same appearance
- **Buttons** can be added to submit information
  - `button` is the html tag for this
  - `type` defines its purpose, in this case `submit`
  - `<button type="submit">this button submits the form</button>`
  - These are also useful as nested elements to the `form` element


# References
[anchor-diagram]: https://i.imgur.com/hviuZwe.png "FreeCodeCamp's diagram for anchor tags"
