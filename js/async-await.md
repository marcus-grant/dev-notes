Asynchronous Functions with Async, Await & Promises
===================================================

One of the niceties of javascript is that due to its nature as the primary language on the web, be it websites or on servers, it was built with asynchronous programming in mind. That doesn't mean that the syntax was always as nice as it is today however. Modern javascript's handling of asynchronous behavior is much more manageable thanks mostly to ES6's [promises][01] and ES7's [async/await][02] syntaxes. Without which we'd still have to deal with unpleasantly long chains of callbacks and response handlers for all the different possible outcomes that can result from an asynchronous action. Let's learn how to use them.


Promises
--------

*consider writing seperate article about promises & either way include examples of recreating promises by hand*

Before learning about `async` & `await` syntax it's important to first understand promises and how to use them, because they get used as responses to *all* `async` functions.

`Promise` is a built in syntax in javascript starting with ES6 that allows the representation of the returns of functions as being in different states of operation. This allows for easier asynchronous operation because functions can now be sent off to do their own thing away from the main execution loop and the program can now always easily know what that function is up to.

There are three possible states of a function that returns a promise:

- *pending*: the initial state, has neither resolved succesfully or in failure, yet.
- *fulfilled*: operation completed successfully.
- *rejected*: operation completed, but failed.

Promises are essentially objects with prototypes for these three states in the form of functions `catch()`, `then()`, & `finally()` in order to handle the three states mentioned before.

![Promise Lifecycle Diagram][i01]


Async Functions
---------------

The great thing about `async` syntax is that it allows us to write asynchronous functions in a manner that makes the code at least appear synchronous, making it much easier for programmers to reason about what is going on under the hood. To create an `async` function just use the keyword `async` before the function definition.

```javascript
// function syntax
async function asyncFoobar() {
  return 'foobar!';
}

// arrow syntax
const asyncFoo = async () => {
  return 'foo!';
}
```

Await
-----

The `await` keyword **is always paired within an** `async` **block**. An `async` keyword without an associated `await` isn't of much use without the await because it's the `await` that indicates when asynchronous stuff is being done, everything else within the `async` block is done syncrhonously. This also means that an `await` not being used within an `async` block will throw syntax errors.

### When to Use Await?

If there's an asynchronous function inside an async block, that's when we need to indicate what part of it needs to be done asynchronously using `await`. This means that the `async` function block will stop execution till the part after `await` is done executing. The classic asyncrhonous action in javascript is requesting data through HTTP requests like this.

```javascript
// function syntax
async function getData() {
  const data = await axios.get('https://some-url.net/api/some/endpoint');
  return data;
}
// arrow syntax
const getOtherData = async () => {
  const data = await axios.get('https://some-url.net/api/some/endpoint');
  return data;
}

```

### Why Await & Not Just Promises?

The syntax is much cleaner, and most developers would agree that it's easier to reason through what the code is doing because they're not following through complex chains of callbacks to follow the program flow. Below is the same example using only Promises.

```javascript
async function getDataWithPromise() {
  let data;
  axios.get('https://some-url.net/api/some/endpoint')
    .then((result) => {
        data = result;
    });
  return data;
}
```

This gets even more complicated when needing to chain other asynchronous blocks and having to also manage errors.


Handling Errors
---------------

### Try & Catch

The most common approach to handle errors in `async` & `await` is to use the `try` & `catch` syntax. Just use the `try` & `catch` keywords inside the `async` block and the javascript compiler will know to run the stuff inside `try`, and only run the stuff inside `catch` if the asynchronous function returns a *failed* resolution to the function.

```javascript
async function getData() {
  try {
    // fetch data from endpoint
    const response = await axios.get('https://some-url.net/api/endpoint');
    const data = await response.json();
    return data;
  } catch (error) {
    // code that properly handles the communication or JSON parsing error
    alert(error);
  }
}
```

Note that the `try` block above has two `await` functions because `axios`'s `get` & `json` functions are both asyncrhonous. Any number of asynchronous calls to wait for can be made inside a `try` block, and the first failure will be *caught* in the `catch` block and which one failed can be identified by the passed on `error` object. From there any number of options can be coded in to handle the error. Probably, `alert()` isn't the ideal way to handle the error.

### Other Error Handling Option

There's another way to *catch* the above error and this is to do with the fact that all asyncrhonous functions will return a failed *promise* object when an error occurs. This method of handling an error involves just appending `.catch()` function calls with a custom function handle the error to the call to the asynchronous funciton block.

```javascript
getData()
  .catch((error) => {
  // handle error
  alert(error);
});
```


Other Asynchronous Syntax
-------------------------

### Async Class Methods

Class methods can also be asynchronous by specifying the `async` keyword.

```javascript
class APIHandler {
  async getData() {
    const data = await.get('https://some-url.net/api/endpoint');
    return data;
  }
}
```

Then to call the class method...

```javascript
const api = new APIHandler();
api.getData().then(/* do stuff to data */);
```

### And Then with Await

In the last example, if it wasn't clear already, awaitable functions can have the `.then()` promise response applied to it.

```javascript
async function getData() {
  const data = await axios.get('https://some-url.net/api/endpoint')
    .then((result) => return result.ids)
  return data;
}
```

### Promise.all with Await

Multiple promises can be resolved with `Promise.all`, the same is true when used with `await`.

```javascript
async function getData() {
  const response = await Promise.all([
    axios.get('endpoint-a'),
    axios.get('endpoint-b'),
  ]);
}
```

In Summary
----------

So in summary, `async` & `await` are pretty great. They greatly help with manageability and readibility of code. If you can use, you probably should, your collegues will probably be your friends. Just remember:

- Async functions always return promises
- Await can only be used inside async blocks
- Await waits till the function *promise* resolves or rejects.

Now write some cleaner asynchronous javascript!


References
----------

*fix links*

[01]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise "MDN: Javascript Promise"
[02]: https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await "MDN: Basics of Async / Await"
[03]: https://scotch.io/tutorials/asynchronous-javascript-using-async-await "Scoth.IO: Asynchronous JS Using Async & Await"

[i01]: https://media.prod.mdn.mozit.cloud/attachments/2018/04/18/15911/32e79f722e83940fdaea297acdb5df92/promises.png "MDN: Promise Lifecycle Diagram"
