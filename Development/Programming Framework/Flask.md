---
layout: default
title:  Flask
---

- [Introduction](#introduction)
- [Installation](#installation)
- [Basic Application Structure](#basic-application-structure)
  - [Initialization](#initialization)
  - [Routes and View Functions](#routes-and-view-functions)
    - [Variable Sections in URL](#variable-sections-in-url)
  - [Server Startup](#server-startup)
  - [The Request-Response Cycle](#the-request-response-cycle)
    - [Request Cycle](#request-cycle)
      - [Context in Flask](#context-in-flask)
      - [Request Dispatching](#request-dispatching)
      - [Request Hooks](#request-hooks)
    - [Responses](#responses)

# Introduction

Flask is a small framework by most standards, small enough to be called a “microframework.”

But being small does not mean that it does less than other frameworks. Flask was designed as an extensible framework from the ground up; it provides a solid core with the basic services, while extensions provide the rest. Because you can pick and choose the extension packages that you want, you end up with a lean stack that has no bloat and does exactly what you need.

Flask has two main dependencies. The routing, debugging, and Web Server Gateway Interface (WSGI) subsystems come from Werkzeug, while template support is provided by Jinja2. Werkzeug and Jinja2 are authored by the core developer of Flask.

There is no native support in Flask for accessing databases, validating web forms, authenticating users, or other high-level tasks. These and many other key services most web applications need are available through extensions that integrate with the core packages. As a developer, you have the power to cherry-pick the extensions that work best for your project or even write your own if you feel inclined to. This is in contrast with a larger framework, where most choices have been made for you and are hard or sometimes impossible to change.

# Installation

```
pip install flask
```

# Basic Application Structure

## Initialization

All Flask applications must create an application instance. The web server passes all requests it receives from clients to this object for handling, using a protocol called Web Server Gateway Interface (WSGI). The application instance is an object of class Flask, usually created as follows:

```python
from flask import Flask
app = Flask(__name__)
```

The only required argument to the Flask class constructor is the name of the main module or package of the application. For most applications, Python’s `__name__` variable is the correct value.

> The name argument that is passed to the Flask application constructor is a source of confusion among new Flask developers. Flask uses this argument to determine the root path of the application so that it later can find resource files relative to the location of the application.

## Routes and View Functions

Clients such as web browsers send requests to the web server, which in turn sends them to the Flask application instance. The application instance needs to know what code needs to run for each URL requested, so it keeps a mapping of URLs to Python functions. The association between a URL and the function that handles it is called a **route**.

The most convenient way to define a route in a Flask application is through the `app.route` decorator exposed by the application instance, which registers the decorated function as a route. The following example shows how a route is declared using this decorator:

```python
@app.route('/')                     # route decorator exposed by the application instance, which registers the decorated function as a route
def index():                        # Called as view functions - registers as handler for the application’s root URL
  return '<h1>Hello World!</h1>'    # response what client receives when it navigates to application’s root URL
```

> Decorators are a standard feature of the Python language; they can modify the behavior of a function in different ways. A common pattern is to use decorators to register functions as handlers for an event.

Functions like index() are called view functions. A response returned by a view function can be a simple string with HTML content, but it can also take more complex forms,

> Response strings embedded in Python code lead to code that is difficult to maintain, and it is done here only to introduce the concept of responses.

### Variable Sections in URL

Flask support type of URL with variable sections. It uses a special syntax in the route decorator to support the dynamic name component

```python
@app.route('/user/<name>')
def user(name):
  return '<h1>Hello, %s!</h1>' % name
```

The portion enclosed in angle brackets is the dynamic part, so any URLs that match the static portions will be mapped to this route. When the view function is invoked, Flask sends the dynamic component as an argument.

The dynamic components in routes are strings by default but can also be defined with a type. For example, route /user/<int:id> would match only URLs that have an integer in the id dynamic segment.

Flask supports types

- int,
- float, and
- path for routes.

The path type also represents a string but does not consider slashes as separators and instead considers them part of the dynamic component.

## Server Startup

The application instance has a run method that launches Flask’s integrated development web server:

> The web server provided by Flask is not intended for production use.

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
  return '<h1>Hello World!</h1>'

@app.route('/user/<name>')
def user(name):
  return '<h1>Hello, %s!</h1>' % name

if __name__ == '__main__':
  app.run(debug=True)
```

- The __name__ == '__main__' Python idiom is used here to ensure that the development web server is started only when the script is executed directly. When the script is imported by another script, it is assumed that the parent script will launch a different server, so the app.run() call is skipped.
- Once the server starts up, it goes into a loop that waits for requests and services them. This loop continues until the application is stopped, for example by hitting Ctrl-C.
- There are several option arguments that can be given to app.run() to configure the mode of operation of the web server. During development, it is convenient to enable debug mode, which among other things activates the debugger and the reloader. This is done by passing the argument debug set to True.
- To run the application, open your web browser and type <http://127.0.0.1:5000/> in the address bar

## The Request-Response Cycle

### Request Cycle

When Flask receives a request from a client, it needs to make a few objects available to the view function that will handle it. A good example is the request object, which encapsulates the HTTP request sent by the client.

The obvious way in which Flask could give a view function access to the request object is by sending it as an argument, but that would require every single view function in the application to have an extra argument. Things get more complicated if you consider that the request object is not the only object that view functions might need to access to fulfill a request.

To avoid cluttering view functions with lots of arguments that may or may not be needed, Flask uses contexts to temporarily make certain objects globally accessible. Thanks to contexts, view functions like the following one can be written:

```python
from flask import request

@app.route('/')
def index():
  user_agent = request.headers.get('User-Agent')
  return '<p>Your browser is %s</p>' % user_agent
```

Note how in this view function request is used as if it was a global variable. In reality, request cannot be a global variable if you consider that in a multithreaded server the threads are working on different requests from different clients at the same time, so each thread needs to see a different object in request. Contexts enable Flask to make certain variables globally accessible to a thread without interfering with the other threads.

#### Context in Flask

There are two contexts in Flask:

1) the application context and
2) the request context.

Flask context globals

| Variable name | Context             | Description                                                                                                         |
| ------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------- |
| current_app   | Application context | The application instance for the active application.                                                                |
| g             | Application context | An object that the application can use for temporary storage during the handling of a request. This variable is reset with each request. |
| request       | Request context     | The request object, which encapsulates the contents of a HTTP request sent by the client.                           |
| session       | Request context     | The user session, a dictionary that the application can use to store values that are “remembered” between requests. |

Flask activates (or pushes) the application and request contexts before dispatching a request and then removes them when the request is handled.

When the application context is pushed, the current_app and g variables become available to the thread; likewise, when the request context is pushed, request and session become available as well.

If any of these variables are accessed without an active application or request context, an error is generated.

In this example, current_app.name fails when there is no application context active but becomes valid once a context is pushed. Note how an application context is obtained by invoking app.app_context() on the application instance.

```python
>>> from hello import app
>>> from flask import current_app
>>> current_app.name
Traceback (most recent call last):
...
RuntimeError: working outside of application context
>>> app_ctx = app.app_context()
>>> app_ctx.push()
>>> current_app.name
'hello'
>>> app_ctx.pop()

```

#### Request Dispatching

When the application receives a request from a client, it needs to find what view function to invoke to service it. For this task, Flask looks up the URL given in the request in the application’s URL map, which contains a mapping of URLs to the view functions that handle them. Flask builds this map using the app.route decorators or the equivalent nondecorator version `app.add_url_rule()`.

To see what the URL map in a Flask application looks like

```python
>>> from hello import app
>>> app.url_map
Map([<Rule '/' (HEAD, OPTIONS, GET) -> index>,
<Rule '/static/<filename>' (HEAD, OPTIONS, GET) -> static>,
<Rule '/user/<name>' (HEAD, OPTIONS, GET) -> user>])

```

- The / and /user/<name> routes were defined by the app.route decorators in the application.
- The /static/<filename> route is a special route added by Flask to give access to static files.
- The HEAD, OPTIONS, GET elements shown in the URL map are the request methods that are handled by the route.
- Flask attaches methods to each route so that different request methods sent to the same URL can be handled by different view functions.
- The HEAD and OPTIONS methods are managed automatically by Flask

#### Request Hooks

Sometimes it is useful to execute code _before or after_ each request is processed. For example, at the start of each request it may be necessary to create a database connection, or authenticate the user making the request. Instead of duplicating the code that does this in every view function, Flask gives you the option to register common functions to be invoked before or after a request is dispatched to a view function.

Request hooks are implemented as decorators. These are the four hooks supported by Flask:

1. `before_first_request`: Register a function to run before the first request is handled.
2. `before_request`: Register a function to run before each request.
3. `after_request`: Register a function to run after each request, if no unhandled exceptions occurred.
4. `teardown_request`: Register a function to run after each request, even if unhandled exceptions occurred.

A common pattern to share data between request hook functions and view functions is to use the `g` context global.

For example, a before_request handler can load the loggedin user from the database and store it in g.user. Later, when the view function is invoked, it can access the user from there.

### Responses

When Flask invokes a view function, it expects its return value to be the response to the request. In most cases the response is a simple string that is sent back to the client as an HTML page.
But the HTTP protocol requires more than a string as a response to a request. A very important part of the HTTP response is the status code, which Flask by default sets to 200, the code that indicates that the request was carried out successfully.
When a view function needs to respond with a different status code, it can add the numeric code as a second return value after the response text. For example, the following view function returns a 400 status code, the code for a bad request error:

```python
@app.route('/')
def index():
  return '<h1>Bad Request</h1>', 400
```

Responses returned by view functions can also take a third argument, a dictionary of headers that are added to the HTTP response.
