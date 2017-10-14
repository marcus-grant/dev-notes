# Using D3 with React to Create Fast Modular Data Visualizations

## Why D3?
- D3 is the gold standard right now for creating data visualizations
- It's strength lies in how many useful declarative functions their team has developed for handling data in the scope of creating visualizations based on them
- It generally follows a flow of...
	- **Select** to select HTML elements to modify and render,
	- **Update** to update & manipulate the data it's rendering
	- **BLAH BLAH** fix later to be more factual
- This is great by itself if that's all that's being done, but in a more complex context it can be better to use a more modular framework that's good for the entire DOM

## Why React?
- React is one of the best ways to create declarative, modular and dynamic web pages
- It's strength lies in the fact that it creates a virtual version of the DOM that gets manipulated by the developer created *components* and of which a diffing engine that only renders changes to the virtual DOM to the actual one
- This virtual DOM also makes it easier to combine HTML tags and javascript code within the same file
	- This filetype is known as **JSX**, or **J**ava**S**cript e**X**tended
	- This makes it easier to manage complex markups that are going to be modified by code, because they can be handled in the same file, and split into single purpose parts called components
- For this reason, handling state and rendering elements is better left to react

## Combining the Strengths of Both
- So by now it should be clear that D3 is really good at processing data that needs to be visualized
- However, though it is quite good at rendering this data, if react is given processed data from D3, react is better at rendering it
	- **Especially** if the visualization is part of a large complex app that would benefit from better code reuse, and combinations of javascript libraries

## Strategies for Using Both
*Then the question becomes how do we use both strengths'?*
- React takes in data, and stores it within its components' states
- React components meant to contain the visualization will receive the data as a prop
- D3, because it's so good at wrangling the data and performing math on it, will process this data stored in its host React component's props
- D3 will then pass the data onto components created specifically to contain the `<svg>` tags where visuals will be rendered as a component that renders `<g>` tags
- D3 will also be used to perform some of the interactions that it's also quite good at, like...
	- Watching for click events on tags representing data **TODO** is this true?
	- Formating tooltips
- React would also have a different relative strength, which is animating changes to the DOM for which there are several React-based libraries that make it easy

**TODO** Add <App /> Creation first

## Creating Shapes with React
- This article will work from the deepest dependency first, try it out inside a running app, and then move up to those components' parents and repeat the process
- The first dependency is going to be the Components whose sole purpose is to take only the data necessary to draw the shapes necessary to create paths inside `<g>` tags
- In this case, since the final product is a line graph created from connecting lines to the dots of a scatter plot, the first component needed that doesn't depend on anything besides react is this `DataDots` component representing a collection of dots representing data points
```jsx
import React from 'react';
const renderDots =
(props) => {                                                                                                                       return (coords, index) => {                                                                                                       const dotProps = {                                                                                                                 cx:   props.xScale(coords[0]),                                                                                                   cy:   props.yScale(coords[1]),                                                                                                   r:    props.radius || 2 ,                                                                                                       key:  index                                                                                                                   };                                                                                                                               return <circle {...dotProps} />;                                                                                               };                                                                                                                             };                                                                                                                                                                                  
export default (props) => {                                                                                                       return <g>{ props.data.map(renderDots(props)) }</g>;                                                                           };                                                                                                                               ```
