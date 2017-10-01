# General Javascript Notes
My disorganized collection of Javascript notes

## ES6

### Spread Operator
- Spread operator allows for copying references to all fields of a dictionary or object notation definition
```js
let user = {
  name: 'Marcus',
  location: 'Brooklyn',
  age: 30,
};

let person = {
  ...user,
  age: 25
};
```
- The above will copy all fields of `user` and then override the `age` field with 25
- Without the spread operator it's also possible to override specific fields, by just referencing a variable within the definition
```js
let age = 28;

let person = {
  ...user,
  age,
};
```
- This will have the same effect, except the value of age is different when definining it this way
- 

