React Animation Notes
=====================

Miscellaneous
-------------
- Pure CSS styling of react animations
  - The first method of animating react components I managed to get working was to just create two classnames for the same component, and during some event switching them
  - Then simply applying styles like these to the *active* and *inactive* states
```sass
.box {
  background: blue;
  opacity: 0;
  height: 0px;
  width: 100px;
  transition: 1s;
}

.active {
  opacity: 1;
  height: 100px;
  transition: 1s;
}
```


References
----------
[01]: https://reactcommunity.org/react-transition-group/ "React Transition Groups"
[02]: https://blog.prototypr.io/using-reactcsstransitiongroup-for-enter-exit-animations-ea100d68e72f "Using ReactCSSTransitionGroups"
[03]: http://www.thejoemorgan.com/blog/2016/01/05/creating-a-dropdown-menu-in-react/ "Medium, Huan Ji: React Page Transition"
[04]: https://codepen.io/jschofie/pen/rGjmVp?editors=0110 "CodePen: Transitions React?!"

1. [ReactCommmunity: React Transition Groups][01]
2. [Prototypr.io: Using ReactCSSTransitionGroups for Enter-Exit Animations][02]
3. [Medium, Huan Ji: React Page Transition][03]
4. [CodePen: Transitions React?!][04]
