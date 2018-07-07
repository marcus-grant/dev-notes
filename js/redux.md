Redux Notes
===========

Basics
------

* Redux is a state container for JS apps.
* Though Redux encourages singletons, this can be avoided with flux.
* `createStore()` is a factory function from `redux` used to create the store.
* A reducer is the only **required** parameter for `createStore()`.
* A **reducer** is only a function that reduces the complexity of a state transaction.
    * Reducers get called by the store automatically when an action is dispatched
    * It takes two parameters:
        * a state
        * and an action
    * Reducers always returns the **new** state.
* Initial state of the application is the second argument passed into `createStore()`.
    * This will essentially look exactly as the state definition block of normal react components that use state.
* `store.getState()` returns the current state of the app.
* Here's an example of what a refactored `App` will look like using redux for the state manager.

```jsx
import React, { Component } from "react";
import HelloWorld from "./HelloWorld";
import { createStore } from "redux";  

const initialState = { tech: "React " };
const store = createStore(reducer, initialState);  

class App extends Component {
     render() {
       return <HelloWorld tech={store.getState().tech}/>
     }
 }
```

* Actions are data structures that describe an action that will change state.
* Actions are passed into reducers as their second argument.
* Actions are simply just javascript objects with at least one property: `type`.
* The `type` should be a unique identifier that accurately describes the action that will be changing the state of the program.
* If it's an ATM, then the actions: `WITHDRAW_CASH`, `DEPOSIT_CASH` would be appropriate actions that change state.
* However, staying with the ATM example, all those actions should probably have more information to perform them.
* Actions can also have what's typically called a `payload`, which is another object property that specifies the details about the action, like how much money is intended to be withdrawn or deposited.
* The payload can be a named property of any name so long as it's consistent throughout the modules that refer to it.
```js
{
    type: WITHDRAW_CASH,
    amount: '500',
}
```
* The `amount` payload could actually have more complex information, and it's common to instead name it `payload` and pass along an array or object when the payload is more nuanced.
```js
{
    type: 'DEPOSIT_CASH',
    payload: {
        accountNumber: 123456789,
        amount: 1200,
    },
}
```
* Since a reducer is where all of the actions need to end up, they need to be able to handle multiple action `type`s.
* Typically this is done by grouping reducers to a set of actions with a similar context.
* Then for that reducer, use a switch statement that checks for the `type` field of the action to determine what sort of logic needs to be applied to replace the state with a new one.

```js
function reducer(state, action) {
    switch(action.type) {
        case "WITHDRAW_CASH":
            // do stuff to return a fresh immutable state with withdrawn cash
            return newState;
        case "DEPOSIT_CASH":
            // again, do something to replace the old state...
            // ...with a new one with modified state data due to a cash deposit
            return newState;
        default:
            // make sure there's a default mode when the action can't be found
            return state;
    }
}
```
* It's useful to consider whether actions are too specific.
* For example, consider a counter applet.
    * One button increments by 1
    * Another increments by 10
    * The two buttons should dispatch the same action, but modify the `payload` field differently.
* **Action Creators** are another redux topic, they are simply functions that create *(return)* the correct action object for the app event that requires it.
* Using ES6 this can be a very quick and terse function:
```js
const withdrawCash = amount => ({ type: "WITHDRAW_CASH", amount });
```
* ...as opposed to pre ES6 conventions...
```js
function withdrawCash(amount) {
    return {
        type: "WITHDRAW_CASH",
        amount: amount,
    }
}
```

* It helps to seperate actions and reducers into their directories in the project's source code directory.
* Also it's helpful to tie together all actions and reducers in their own `index.js` file within those directories.
* Stores are suggested this as well, but I don't know how useful that is when there's only one store, and that seems to be the convention for most apps right now.
* ***TODO*** come up with better example

`./store/index.js`
```js
import { createStore } from 'redux';
import reducer from '../reducers';

const initState = { payload: "foobar" };
export const store = createStore(reducer, initState);
```

`./App.js`
```js
import React, { Component} from 'react';
import Label from 'label';
import ButtonPanel from './button-panel';

class App extends Component {
    render() {
        return [
            <Label key={1} text={store.getState().text} />
            <ButtonPanel key={2} incrementAmounts={[1, 2, 4, 8]} />
        ];
    }
}
export default App;
```

`./ButtonPanel.js`
```js
import React from 'react';
import { store } from './store';
import { setAmount } from './actions';

const dispatchBtnAction(e) {
    const amount = e.target.dataset.amount;
    store.dispatch(setAmount(amount));
}

const ButtonPanel = ({ incrementAmounts }) => (
    <div>
        { incrementAmounts.map((amount, i) => (
            <button
                data-amount={`increment-by-${amount}`}
                key={`btn-${i}`}
                className="increment-button"
                onClick={dispatchBtnAction}
            >{`+${amount}`}
            </button>
        ))}
    </div>
);
export default ButtonPanel;
```

`./reducers.js`
```js
export default (state, action) => {
    switch(action.type) {
        case 'incrementAmount':
            return {
                ...state,
                text: action.tech
            };
        default:
            return state;
    }
};
```

* [`data-btn`][01] is an attribute of HTML tags where a name can be associated with an element where the attribute: `data-foobar` makes `data-foobar` when referenced somewhere else accessible. **improve explanation**
* Remember, ***never mutate state directly!!!***


References
----------
1. [MDN: Using Data Attributes][01]

[01]: https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes "MDN: Using Data Attributes"
