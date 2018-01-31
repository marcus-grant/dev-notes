React PropTypes Notes
=====================

PropTypes for Children
----------------------
- When defining how `children`, components that get wrapped inside the current component, the special prop, `props.children` gets referenced
- The *PropType* definition of this isn't necessarily obvious
- However, this StackOverflow [response][10] has a really concise, but completely sufficient solution:
```js
import PropTypes from 'prop-types'

// ...

static propTypes = {
    children: PropTypes.oneOfType([
        PropTypes.arrayOf(PropTypes.node),
        PropTypes.node
    ]).isRequired
}

// OR...

static propTypes = {
  children: PropTypes.node.isRequired,
}
```

References
==========
[10]: https://stackoverflow.com/questions/42122522/reactjs-what-should-the-proptypes-be-for-this-props-children#42122662 "StackOverflow: React PropTypes for Children"

10. [StackOverflow: React PropTypes for Children][10]
