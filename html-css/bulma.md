Bulma UI Notes
==============

Scrimba Course
==============

Overview
--------

* Based on flexbox
    * Makes sectioning & aligning more natural
    * Don't need to know much about bulma, but it's how it works behind the scenes
* Can prototype:
    * Columns
    * Forms
    * Layouts
    * Components
    * Layout Elements, etc.


A Useful Template index.html & debug.css
----------------------------------------

* ***Insert initial image***
* To begin with, here's a pretty good starter boilerplate to play around with bulma CSS

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Hello Bulma!</title>
        <link rel="stylesheet"
        href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.0/css/bulma.min.css">
        <script defer src="https://use.fontawesome.com/releases/v5.0.7/js/all.js"></script>
        <!-- <link rel="stylesheet" href="styles/debug.css"> -->
        <!-- styles go here -->
        <!--
        <style>
        .center {
            display: flex;
            justify-content: center;
            align-items: center;
        }
        </style>
        -->
    </head>
    <body>
        <!-- Template Elements Go Here -->
    </body>
</html>
```
* Then it's sometimes nice to have a stylesheet that can be used for debugging purposes, save it as `debug.css`

```css
/*! debug.css | MIT License | zaydek.github.com/debug.css */
*:not(path):not(g) {
	color:                    hsla(210, 100%, 100%, 0.9) !important;
	background:               hsla(210, 100%,  50%, 0.5) !important;
	outline:    solid 0.25rem hsla(210, 100%, 100%, 0.5) !important;

	box-shadow: none !important;
}
```
* Just uncomment the html above's commented out link tag to enable the debug stylesheet



First Project: A Bulma Documentation Site
-----------------------------------------

* The filetree
```
./
	docs/
		bulma.html
	images/
	styles/
		debug.css
	index.html
```

* `./index.html`
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>Hello Bulma!</title>
		<link rel="stylesheet" 
        href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.0/css/bulma.min.css">
		<script defer src="https://use.fontawesome.com/releases/v5.0.7/js/all.js"></script>
        <!-- <link rel="stylesheet" href="styles/debug.css"> -->
	</head>
	<body>
		<section class="section">
			<div class="container">
				<h1 class="title">
					Hello World
				</h1>
				<p class="subtitle">
					My first website with <strong>Bulma</strong>!
				</p>
			</div>
		</section>
	</body>
</html>
```

* `./docs/bulma.html`
```html
<!--
	hero:       https://bulma.io/documentation/layout/hero/
	section:    https://bulma.io/documentation/layout/section/
	image:      https://bulma.io/documentation/elements/image/
	columns:    https://bulma.io/documentation/columns/basics/
	media:      https://bulma.io/documentation/layout/media-object/
	icon:       https://bulma.io/documentation/elements/icon/
	breadcrumb: https://bulma.io/documentation/components/breadcrumb/
	level:      https://bulma.io/documentation/layout/level/
-->

<!-- hero: https://bulma.io/documentation/layout/hero/ -->
<section class="hero">
	<div class="hero-body">
		<!-- ... -->
	</div>
</section>

<!-- section: https://bulma.io/documentation/layout/section/ -->
<section class="section">
	<div class="container">
		<!-- ... -->
	</div>
</section>

<!-- image: https://bulma.io/documentation/elements/image/ -->
<figure class="image is-128x128">
	<img src="https://bulma.io/images/placeholders/128x128.png">
</figure>

<!-- columns: https://bulma.io/documentation/columns/basics/ -->
<div class="columns">
	<div class="column">
		<!-- ... -->
	</div>
</div>

<!-- media: https://bulma.io/documentation/layout/media-object/ -->
<article class="media">
	<figure class="media-left">
		<!-- ... -->
	</figure>
	<div class="media-content">
		<div class="content">
			<!-- ... -->
		</div>
	</div>
</article>

<!-- icon: https://bulma.io/documentation/elements/icon/ -->
<span class="icon has-text-success">
	<i class="fas fa-check-square"></i>
</span>

<!-- breadcrumb: https://bulma.io/documentation/components/breadcrumb/ -->
<nav class="breadcrumb" aria-label="breadcrumbs">
	<ul>
		<li><a href="#"><!-- ... --></a></li>
		<li class="is-active"><a href="#" aria-current="page"><!-- ... --></a></li>
	</ul>
</nav>

<!-- level: https://bulma.io/documentation/layout/level/ -->
<div class="level">
	<div class="level-item">
		<!-- ... -->
	</div>
</div>
```

* This should produce this:
![Initial Render of Bulma Docs Page][i01]

* Then that gets removed for this html, `index.html`
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>Hello Bulma!</title>
		<link rel="stylesheet" 
        href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.0/css/bulma.min.css">
		<script defer src="https://use.fontawesome.com/releases/v5.0.7/js/all.js"></script>
        <link rel="stylesheet" href="styles/debug.css">
	</head>
	<body>
        <section class="hero is-fullheight has-background-black">
            <div class="hero-body">
                <!-- ... -->
            </div>
        </section>
	</body>
</html>
```
* This just creates a section with a *hero* element
	* Hero's are just the kind of image or header figure element at the top of a page that is typically pretty huge and made to take the most attention
	* Think of marketing sites that might have a big emphasized logo and maybe a product image at the top
* Note this line: `<section class="hero is-fullheight has-background-black">`
	* `hero` is a class name for bulma's hero class which handles these sorts of elements
	* `is-fullheight` is needed because otherwise the hero is of no height whatsoever, and it will fill in the available vertical space
	* `has-background-black` is just the class that specifies the black backgroudns for all elements
* When putting something inside a hero or section element, it's typical to want to put it inside a `container` div
* To treat the hero header of the site probably we'll also add a `figure` & `img` element inside the `container` `div`.
* The image is then given its own custom style: `style="max-width: 512px;"`
* Then the figure is given classes: `image center` to center the image inside the hero container
* The new `index.html` should look like this:
```html
<body>
    <section class="hero is-fullheight has-background-black">
        <div class="hero-body">
            <div class="container">
                <figure class="image center">
                    <img src="images/bulma-logo-white.png" style="max-width: 512px;">
                </figure>
            </div>
        </div>
    </section>
</body>
```

* It should now look like this:
![Render of Completed Hero Section of Documentation Page][i02]


Adding a New Section
--------------------

* Think of `section`s as the highest (in hierarchy) seperating containers in the semantics of these elements
* Add a new section
* In that section, which is below the hero element, is where some columns with some text boxes are going to be held that will give summary information about different parts of the bulma UI kit.
* Within this new section, it would be nice if the text boxes would have more horizontal boxes laid out to fill available screen width, *(and reduce the number if there's not enough)*.
* This is done by first enclosing the section in a `container`.
    * This is to create a new flexbox container to dynamically lay out all nested items by their own internal sets of rules independent of the outside ones.
* But crucially, the boxes should be placed within `column`s

```html
<!--- text box/card section -->
<section class="section">
    <div class="container">
        <div class="columns">
            <div class="column">
                <!-- Text Cards Go Here -->
            </div>
        </div>
    </div>
</section>
```

* Enabling the debugger stylesheet, this is what should be visible:

![Debugging Render of Empty Column Section][i03]

* Now the new empty `column` is visible inside of the new `columns` container
* Then to add all the columns that are desired, just add new copies of the `column` div inside the `columns` div.
* Then just fill those divs with the text desired.
* This looks a bit uninteresting though, so maybe add some flavor to each `column`.
* One useful one that is to be used here is the `notification` class.
* Now the new section should look like this:
```html
<div class="columns">
    <div class="column">
        <div class="notification">
            Bulma is a modern CSS framework from @jgthms, based on Flexbox.
            Using Bulma, we can describe our website's design using just classes. ᕕ( ᐛ )ᕗ
        </div>
    </div>
    <div class="column">
        <div class="notification">
            Flexbox is a CSS spec that makes sectioning and aligning more natural.
            We don't need to know Flexbox but it's how Bulma works behind-the-scenes. ⦤(^ー^)⦥
        </div>
    </div>
    <div class="column">
        <div class="notification">
            Instead of writing our CSS per-element, we can use predefined classes.
            With enough classes, we can describe our website design using semantics. ٩(^ᴗ^)۶
        </div>
    </div>
</div>
```
* Which should look like this:
![Three new columns with text using notification style][i04]

* And responsiveness will already be built in by invoking these style classes, try resizing the width of the browser window.
    * The `columns` container should shrink to a minimum of 1 column wide of text boxes.
    * The `columns` container should expand to a maximum of 3 column wide of thext boxes.

* `notification` can be modified to, for example, `is-primary` to change the color of the text box to whatever the defined primary color is
    * Fun fact: the default primary is the same green color of Bulma's *(from DBZ)* hair.


Colors
------

* The site looks rather drab at the moment, I like minimalism and monochrome styles but even they can be made to look better.
* Let's focus on color here though.
* Start by removing the hero section's background color (`has-background-black`)
* That will leave the bacgkround white, but the logo is white so it isn't visible.
* There's another logo that's colorful in the file `images/bulma-logo.png`.
* It's a bit big so change the size attribute to `style="max-width: 160px;"`
* Then give the hero, `has-bacgkround-white` as a class to make it look more clean
* Now let's give each column their own thematic modifiers:
    * `is-info`
    * `is-primary`
    * `is-warning`
* 




References
==========

1. [Scrimba: Bulma CSS][01]

[01]: https://scrimba.com/p/pV5eHk/c9EqQSD "Scrimba: Bulma CSS"

[i01]: ./pics/scrimba-bulma-docs-page-initial.png "Screenshot of Initial Documentation Page HTML"
[i02]: ./pics/scrimba-bulma-docs-page-hero.png "Render of Completed Hero Section of Documentation Page"
[i03]: ./pics/scrimba-bulma-docs-page-blank-columns.png "Debugging Render of Empty Column Section"
[i04]: ./pics/scrimba-bulma-docs-page-three-columns.png "Three new columns with text using notification style"
