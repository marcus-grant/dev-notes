# General Notes on Full-Stack Python
A collection of not well-organized notes on using python for full stack web development


## Boilerplate Flask App

```python
# ---- Flask Hello World ---- #
# import the Flask class from the flask package
from flask import Flask

# create the application object
app = Flask(__name__)

# use the decorator pattern to
# link the view function to a url

@app.route("/")
@app.route("/hello")
# define the view using a function, which returns a string
def hello_world():
	return "Hello, World!"

# start the development server using the run() method
if __name__ == "__main__":
	app.run()
```
*Hello World Flask App*

- `Flask` is the module needed to be loaded and referred to in order to configure and run a Flask app
- Initialize with `Flask(__name__)` as a flask application reference
- `app.run()` tells the flask server to run so long as it is the main execution file
- The decorator `@app.route` defines the URL routes of the application
	- In the above example, the `hello_world()` function gets called on the root & `/hello` paths
- The `if __name__ == "__main__"` portion has to do with the behind the scenes execution order of python files
	- `__main__` is the `__name__` that gets automatically assigned when the script file originally called to run the application is the current one
- The app.run() should only be run when the main flask file is used, so the final line above is just to make sure main execution order is preserved despite of any extra execution orders that might exist in any subordinate files
- Running this script **while inside the app's environment** should allow a browser to go to [localhost:5000/](http://localhost:5000/) or [localhost:5000/hello](http://localhost:5000/hello) and see the *Hello World!* string defined in the `hello_world()` function

## Dynamic Routes

- Before all urls entered into the route had to be statically defined with functions using the `@app.route` decorator pattern
- Instead a dynamic one can be specified where the url entered can be parsed and used for a specific response based on the input
- Below, the added function using a set of dynamic routes demonstrates this

```python
# dynamic route based on entered url
@app.route("/test/<search_query>")
def search (search_query):
    return search_query
```

- Same way of passing the path to a function using a decorator
- **But** the deocator's path now has a place-holder tag `<search_query>` which defines the function parameter's variable name that gets passed into the function
- By using that variable name `search_query` inside the function, you can retreive the value passed by the frontend
- `search_query` could be any valid python variable name
- `<int:value>` as a pattern instead, returns dynamic route as an integer instead, or gives a 404 if it can't be converted to an integer
- `<float:value>` same as above, but as a float
- `<path:some/great/path>` gives the dynamic route as a path

```python
# dynamic route handlers of each kind
@app.route("/integer/<int:value>")
def int_type(value):
    print(value + 1)
    return "correct"

@app.route("/float/<float:value>")
def float_type(value):
    print(value + 1)
    return "correct"

@app.route("/path/<path:value>")
def path_type(value):
    print(value)
    return "correct"
```

- (http://localhost:5000/integer/1) would return a `2` in the flask app console
- (http://localhost:5000/integer/1.1) would return a 404
- (http://localhost:5000/integer/string) would return a 404 as it can't be turned into an int
- (http://localhost:5000/float/1) returns a 404 as well, because it expects the decimal of a float
- (http://localhost:5000/float/1.1) returns a `2.1`
- (http://localhost:5000/float/string) returns a 404 code
- (http://localhost:5000/path/my/path/float1.9/int2) return `my/path/float1.9/int2`
- 	any valid url character would work past the `/path` dynamic route and get returned, it would just be a unicode string


## Response Objects
- Whenever web requests are sent and received it is wihtin the form of a request/response object that will have several embedded headers related to the sender/receiver
- Among those will be the response code, *ie 200 for a success or 404 for not found*
- The return that the flask functions have been using, so far have only used strings as returns
- In reality those strings are converted to response objects stored as a tuple of the below format:
	- `(response, status, headers)`
- changing the return of a function below the route decorator will define the kind of web response flask will return based on the route its given
```python
# new view to play with response objects
@app.route("/name/<name>")
def index(name):
    if name.lower() == "michael":
        return "Hello, {}".format(name), 200
    else:
        return "Not Found", 404
```
*Example of response object using using a string as the response content (converted to html with string and either a 200 for a response if the right condition is met in the route, otherwise a 400*


