Dropdown Menus in React
=======================

The Div & Span Way
------------------

- This makes heavy use of CSS (probably requires a preprocessor like LESS or SASS)
- It renders two states
  - The `expandedList` view which shows all the options when the menu is selected
  - The `collapsedList` view which is the default state
- State is managed by state property `isExpanded` & `selectedOption` to handle selection

