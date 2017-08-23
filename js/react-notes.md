# React-Notes
A general collection of highly unorganized notes relating to react. Use these to make a cleaner and perhaps subdivided set of notes later, this is more of a scratchpad to quickly get unfiltered and edited knowledge onto the hard disk.

## Fundamentals
*[from freecodecamp](http://bit.ly/2vYaG9A)*
- Everything in react is about build React components and reusing them to render HTML
- At its simplest, react is a plain JS functions
  - Where `ReactDOM.render(<SomeComponent label="poop" />, reactMountPoint` describes an HTML element of id `reactMountPoint` to render everything inside the first parameter using the `ReactDOM`
- React components **always** need to start with a capital letter
  - React needs to be able to distinguish from standard HTML element tags
- Every component receives a list of ***props***, née HTML attributes or properties
- `createElement(ComponentOrElementName, { prop1: "some_val", prop2: "someVal", propN: "some-val" }`
  - `createElement` is one of the primary functions in the top-level of the React API
  - ? TODO: follow up on createElement because it might be incorrect
- 

## Misc. 
