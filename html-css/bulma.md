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


### Adding a New Section






References
==========

1. [Scrimba: Bulma CSS][01]

[01]: https://scrimba.com/p/pV5eHk/c9EqQSD "Scrimba: Bulma CSS"

[i01]: ./pics/scrimba-bulma-docs-page-initial.png "Screenshot of Initial Documentation Page HTML"
[i02]: ./pics/scrimba-bulma-docs-page-hero.png "Render of Completed Hero Section of Documentation Page"
