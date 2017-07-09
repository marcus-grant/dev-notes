# Notes on Responsive HTML/CSS/JS using Bootstrap

## Bootstrap Fluid Containers
- Responsive design is responsive because the layout responds to changes in the browser window size
- One html can be used effectively by mobiles, tablets, small windows & big windows
- Bootstrap is a nice component kit of well defined defaults for CSS and layouts
- To include it in a project...
  - Clone its git repository
  - Download the latest release and put the css/js/fonts folders in the right places
  - Insert this tag near the top:
`<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.1/css/bootstrap.min.css"/>`

## Bootstrap Stuff
- `container-fluid` as a `div` element gives a container that will make some solid inferences as to the layout of its children
- `text-center` as a class can center an element

### Buttons
- `btn` is bootstrap's default `button` element class, looks good!
- `btn-block` gives a button the width of its container instead of the width of its text like `btn` does
- `btn-primary` takes the main color of the site, useful for keeping consistent highlights and accents
  - Needs `btn-block` & `btn` as inheritors?
- `btn-danger` is the button color for notifying destructive or dangerous actions that could take place if the button is used

### Grids

