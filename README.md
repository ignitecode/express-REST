# Create and Read with Express

### Objectives

- Understand `module.exports` and why we use it
- Write an HTML form that can make POST requests
- Describe how inputs transform into a req.body object on the server side
- Use bodyParser to read parameters passed from forms via POST requests
- Build RESTful routes that are wired to the correct semantically named view

### Preparation

*Before this lesson, students should already be able to:*

- Identify HTTP verbs, with crud actions, and view layers
- Build routes
- Pass parameters via dynamic segments and query parameters

<br>

## Intro

So far, we've created routes in Express and passed parameters from the browser to our server. We've constructed our views and dynamically built our html using EJS.

But now we're going to see the glue that ties this all together – forms.

## Express Generator

For brevity, we're going to bootstrap our Express app using the [Express Generator](https://expressjs.com/en/starter/generator.html). While it would absolutely be great for you to build this all from scratch, it's important to make sure we focus right now on the one _new_ piece of our application structure: passing information from the client to our server, persisting that data in a data structure, and sending it back.

<br />

&#x1F535; **I DO, THEN YOU DO: PART 1 - Starter Code Set-up**

1. Install the Express generator globally (from any directory in the Terminal): `npm install express-generator -g`.

2. Let's use the Express generator to instantiate a new app for us with this command: `express --view=ejs --git express-create-read-generator-lesson`

  - This will set our app's view engine to `ejs`
  - This command will create a new project directory for us

3. `cd` into the `express-create-read-generator-lesson-starter` directory and run the command `npm install` which will read the `package.json` file and install all of the dependencies specified into the `node_modules` folder. 

4. In your package.json, add the `start:dev` key/value pair to your scripts object:

	```js
	"scripts": {
   		"start": "node ./bin/www",
   	 	"start:dev": "nodemon ./bin/www"
	},
	```

	**Don't forget the comma after the 'start' line.**

3. To run this, in your terminal, open up a new tab and type `npm run start:dev` to run nodemon.

4. For any other commands that you would like to run, use another terminal tab.

### Express generator plumbing
The express generator gives us a lot.

  * app.js
  * routes folder- where we put our controllers
    * index.js- where we set our home route
    * users.js
  * views folder- where we put our views
    * error.ejs
    * index.ejs- where our express title comes from
  * public folder- where we put our assets
    * images folder
    * javascript folder
    * stylesheets folder
      * style.css
  * bin folder
    * www - provides the set up for our app

This is a great start!  But there are still a few things that we need to add.

#### `--git`

`--git` adds a `.gitignore` file for you, with the `node_modules` inside.  If you decide to create your node app another way, make sure to `touch .gitignore`. 

This will make it so that when you `git add .` & `git commit -m "initial commit"`, you won't also commit your node_modules into your .git folder.  This will be extremely important when you are deploying!  You **DO NOT** want to ever commit your node_modules or you will have all sorts of trouble when you try to deploy.

## Back to the app: Adding in bootstrap

[Get Bootstrap](https://getbootstrap.com/)

#### In the views/index.ejs:
In the head tag, above the `stylesheets/style.css` link, add this link:

```html
<!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">
```
Just above the closing body tag:

```html
<script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
```

<br />


<!-- ### Create our todos index route

3. In the `app.js` folder, we need to create a `/todos` or `index` route. It will contain a `var seededTodos` which is an array of 2 seeded todos that we would like to render on our `views/todos/index.ejs` page:


```javascript
/* INDEX TODO */
app.get('/todos', function(req, res) {
  var seededTodos = [
    {
      description: "walk Diesel",
      urgent: true
    },
    {
      description: "dry cleaning",
      urgent: false
    }
  ];

  res.render('/todos/index', {
    todos: seededTodos
  });
})
```
<br /> -->

## Single Responsibility

> "Every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class." - https://en.wikipedia.org/wiki/Single_responsibility_principle

<br />

## MVC - Model View Controller

![](assets/mvc-diagram.png)

**MVC** is an application architectural pattern. So far we have been mainly writing everything in the `app.js` file, and the views directory.

You can imagine that each of these files and folders will get larger and larger.    

<br />

**WE DO**

Add an **INDEX** route with the following:

  - A `routes/todos_controller.js` file
  - Move your `/todos` index route to the file.

<details>

<summary> **Let's Add an INDEX Route!** </summary>

## Set-up Solution

We are creating routes are all related to todos.  In order to keep our code readable, let's put them all in a `routes/todos_controller.js`.  In the `routes` directory and create a file called `todos_controller.js`.  This will be our todos controller/route file.

Require express at the top of that `todos_controller.js` file.

```javascript
var express = require('express');
```

Invoke the router.

```javascript
var router = express.Router();
```

<!-- Change all instances of `app` to `router`.

```javascript
/* INDEX TODO */
router.get('/todos', function(req, res) {
  ...
});
``` -->

At the bottom of the file, export the router.

```javascript
module.exports = router;
```

Now in your `app.js`, require the controller file that we just created (around line 10).  **Be sure to include ./ at the beginning of the path to the controller, so node knows that this is not an NPM package.**

```javascript
var todosController = require('./routes/todos_controller.js');
```

Use the todosController for all routes that start with `/todos` (around line 28).

```javascript
app.use('/todos', todosController);
```

Now that we have our todos controller configured and set up in our app.js, in our `routes/todos_controller.js` file, let's add our INDEX route.  We are going to add a `var seededTodos` which is an array of 2 seeded todos that we would like to render on our `views/todos/index.ejs` page.

```javascript
/* INDEX TODO */
router.get('/todos', function(req, res) {
  var seededTodos = [
    {
      description: "walk Diesel",
      urgent: true
    },
    {
      description: "dry cleaning",
      urgent: false
    }
  ];

  res.render('todos/index', {
    todos: seededTodos
  });
});
```

Since we are specifying that the controller will be used for all routes starting with `/todos` (the first parameter in our `app.use()` method in our `app.js` file), we don't need to show `/todos` in our actual routes within our controller file.

```javascript
/* INDEX TODO */
router.get('/', function(req, res) {
  ...
});
```
</details>

<br />

&#x1F535; **CFU**

- Why are we requiring the `todos_controller.js` in the app.js?
- We put `module.exports = router;` at the bottom of the `todos_controller.js` file.  What is this line doing here?
- Why can we use `‘/‘` instead of `‘/todos’` in the todos controller?

<br>

&#x1F535; **YOU DO**

1.  Create a `views/todos/index.ejs` file. Using ejs, add some code that shows us how many todos we have. Like so:

    <!-- ![](https://i.imgur.com/4VhZNh3.png) -->

1. Add a 3rd (or more!) todo to the `seededTodos` object.

<br>

<details>

<summary>**Create an views/todos/index.ejs**</summary>

## Set-up Solution

1. Touch a `views/todos/index.ejs` file.

2. Add in this syntax:

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- Latest compiled and minified CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>To Do List</h1>

    <ul>
      <% todos.forEach(todo => { %>
	    <li>
	      <h1><%= todo.description %></h1>
	      <p><strong>urgent:</strong> <%= todo.urgent %></p>
	    </li>
	  <% }) %>
    </ul>
  </body>
</html>
```
</details>

<br>

## EJS Layouts

[EJS Layouts](https://www.npmjs.com/package/express-ejs-layouts)

- `npm install express-ejs-layouts --save`
- Add the following to your `app.js`

```js
// app.js

var expressLayouts = require('express-ejs-layouts');

...

app.use(expressLayouts);
```

- `touch layout.ejs`
- Add the basic skeleton for your HTML and add `<%- body %>` to the body.
- To dry up your header:
	- `mkdir partials`
	- `touch partials/_header.ejs`
	- In the head of `layout.ejs`: `<% include partials/_header %>`

<br>

## Before we move forward, let's add some basic styles

In your `public/stylesheet/styles.css`, replace the default styles with the styles below:

```css
body {
  font: 1rem "Lucida Grande", Helvetica, Arial, sans-serif;
  text-align: center;
}

h1 {
   margin: 100px 0px 50px;
}

a {
  color: #000;
}

a:hover {
  color: #209aca;
}

ul {
  list-style-type: none;
  padding: 0px;
}

ul li {
  padding: 10px 0px;
}
```


## module.exports


&#x1F535; **I DO, THEN YOU DO**

In our `routes/todos_controller.js`, we have a `var seededTodos` which is an array of 2 seeded todos.

As our app grows in size and complexity, we will want to move our logic out of this file. One method for doing so is by using an object called `module.exports`. This allows us to clean up and share code throughout our app.

`module.exports` allows us to separate our .js files by exposing their contents as one global variable. The global variable isn't assigned until we require the file.

1. For example, let's remove the `seededTodos` data from `routes/todos_conrtoller.js`.  Let's create a `models` folder in our main application.  Inside of that folder, let's create a `data.js` file.  Inside of that `models/data.js` file, add the seededTodos as key/value pairs of the `module.exports` like so:

	```javascript
	module.exports = {
	  seededTodos: [
	    {
	      description: "walk Diesel",
	      urgent: true
	    },
	    {
	      description: "dry cleaning",
	      urgent: false
	    }
	  ]
	};
	```

	Now our `module.exports` will have a property of `seededTodos` which is an array of todo objects.

2. In the `routes/todos_controller.js`, instantiate a variable to grant access to the module that we've just created:

	```javascript
	var data = require('../models/data.js');
	```

3. Now that our variable `data` has been assigned our `module.exports` object, we can access our `seededTodos` in our `routes/todos_controller.js` file like so:

```javascript
/* INDEX TODO */
router.get('/todos', (req, res) => {
  res.render('todos/index', {
    todos: data.seededTodos
  });
});
```

> This is essentially like calling `module.exports.data`


> Refresh the page and you can see that nothing changed in our browser, really we just moved the functionality into a different file.

<details>

<summary>What advantages does this bring to us with regard to separation of concerns in MVC?</summary>

1) The code is more organized and easier to understand by having separate controllers for our routes.

2) By putting the data in a separate file, we can now utilize that data in different files without replicating code.  This can be really useful down the road when we create large files with functions associated with a specific task.

</details>

<br />

You'll start to see the necessity of `module.exports` once we add
more models and more routes to our application.  Keeping everything in the `app.js` will quickly become a headache.

<br>

&#x1F535; **YOU DO**

Add a `models` folder, a `data.js` file, and inside of that, inclue a `module.exports` object with your seededTodos to DRY up your code.

<br>


## Get To Know The Code - REST-ful Routing

In general, programming is very free form — there are many solutions and many possible and perfectly acceptable answers and means of solving problems. However, `Express` is a framework and has opinions about how we should write our code. Similarly, over the last 20 years or so programmers have developed several patterns to help clarify the purpose and design of routes in the context of HTTP and databases.

Our key words here today are:

- **RESTful** routes
- **CRUD**

|   HTTP    | CRUD      | RESTful View names            |
|-------:   |--------   |---------------------------    |
| GET       | Read      | show / index / new / edit     |
| POST      | Create    | no views rendered             |
| UPDATE    | Update    | no views rendered             |
| DELETE    | Delete    | no views rendered             |

<br>

We're going to follow a very specific pattern at first.

|           View    | Purpose                                                                       | Blog example                                              | RESTful route example                                 |
|---------------:   |----------------------------------------------------------------------------   |---------------------------------------------------------  |---------------------------------------------------    |
| show.ejs      | Shows an individual resource                                                  | A page that shows a single blog post                      | localhost:3000/posts/1 | GET /posts/:id               |
| index.ejs     | Shows all instances of a resource                                             | A page that shows all blog posts                          | localhost:3000/posts | GET /posts                     |
| new.ejs       | Displays a form to create a new resource (form makes a post request)          | A page that lets you create a new blog post               | localhost:3000/posts/new | GET /posts/new                 |
| edit.ejs      | Displays a form that lets you edit a resource (form makes a post request)     | A page that lets you edit an already created blog post    | localhost:3000/posts/1/edit | GET /posts/:id/edit     |



<br>


Roughly, here's how the table above would look in our `routes/posts_controller.js`:<!--  (this generic example is BEFORE DRY-ing up our code with express.Router and a separate controller file.) -->

```javascript
var express = require('express');
var router = express.Router();

/* INDEX ROUTE */
router.get('/posts', (req, res)=> {
  console.log("index");
  res.render("/posts/index");
});

/* NEW ROUTE */
router.get('/posts/new', (req, res) => {
  console.log("new");
  res.render('/posts/new');
});

/* SHOW ROUTE */
router.get('/posts/:id', (req, res) => {
  console.log("show");
  res.render("/posts/show");
});

/* CREATE ROUTE */
router.post('/posts', (req, res) => {
  console.log("create");
  res.send("CREATE");
});

/* EDIT ROUTE */
router.get('/posts/:id/edit', (req, res) => {
  console.log("edit");
  res.render("/posts/edit");
});

/* UPDATE ROUTE */
router.put('/posts/:id', (req, res) => {
  console.log("update");
  res.send("UPDATE");
});

/* DELETE ROUTE */
router.delete('/posts/:id', (req, res) => {
  console.log("delete");
  res.send("DELETE");
});

module.exports = router;
```
<br>

<details>
    <summary>**Nested Resources**</summary>

A restful architecture specifies the path to be constructed as a resource, the unique identifier of that resource and then another resource.

That second resource might be another object like a blog post or it might be a page.

So if we had users in our blog example above and users had blog posts and we wanted to see all of the blog posts of a specific user our route would like as follows:

`localhost:3000/users/1/posts` | `/users/:id/posts` | `views/posts/index.ejs` (file path)

Don't worry about this idea of nested resources for now, but know this is what we're moving toward.

</details>

<br>

## To do SHOW Route

Convention dictates that the `show` route returns one instance of a given resource.

&#x1F535; **I DO, THEN YOU DO**

In previous lessons we learned about 'wildcard params'. (e.g. - `http://localhost:3000/:id`)

Can someone remind me what a wildcard is and how we use it?

#### EXERCISE

1) Create a **SHOW** route for a single to do

2) Create a show.ejs view, in our todos directory

3) Render a single to do from our `data.seededTodos` array.

For example:

![](assets/show-header.png)
<br />
![](assets/show.png)

<br>

&#x1F535; **CFU**

- How is the line `var todo = data.seededTodos[req.params.id];` grabbing a single todo?
- What does this line allow us to do in our show.ejs view? `{ todo: todo }`

<details>

<summary>**Create a SHOW Route and a views/todos/show.ejs**</summary>

## Set-up Solution

#### In our `routes/todos_controller.js`

```js
/* SHOW TODO */
router.get('/:id', (req, res) => {
  var todo = data.seededTodos[req.params.id];

  res.render('todos/show', {
    todo: todo
  });
});
```

In our `views/todos/show.ejs` template:

```html
<h1><%= todo.description %></h1>
<p><strong>urgent:</strong> <%= todo.urgent %></p>
```

</details>

<br>

## Todo `NEW` Route

Forms take user input and saves that input to a database, so let's start with a form to make a new todo item, as we need todos to add some user interaction.

Based on our table earlier. What view name do we want to use to create a page that has a form that will allow us to make a new resource?

1. Let's create a view for our form:  `touch views/todos/new.ejs`

2. Inside of `views/todos/new.ejs` add the html form tags:

```html
<form>
</form>
```

<details>
<summary>Now, forms need to know where to _send_ information and the HTTP verb that they'll be using to submit a request. Which verb do we use to **create** a new resource?</summary>

`POST`
</details>

<br />

<details>
<summary>What's the route that we need to `POST` to?</summary>

`http://localhost:3000/todos`

</details>

<br />

All forms must have a `method` and an `action`.

1. Add an **action** and a **method** attribute to our form. The value of action will be `/todos` and the value of method will be `POST`.

```html
<form action="/todos" method="POST" >
</form>
```
**The order of these don't matter now, but they will when we add in method-override.  You have to have the action attribute before the method when using method-override.**

- Why can we use the `/todos` route for the form when the /todos route is already used to lead us to the index page?

  Now that we have a form that's going to `POST` to the `/todos` index – it will hit our server, find an action with that combination of URL & verb, and run that code.

  There are no input fields or a submit button, but we'll add that next.

<br>

&#x1F535; **YOU DO**

Walk through the steps above to add a basic skeleton form to your app.

<br>

#### Let's add some form fields!

Luckily, form fields aren't that different from something we've seen before in JS – an object. In an object there are keys & values. Each input in a form is a key/value pair. Let's see a semi-complete example & dissect it.

```html
<h1>Add a New Todo</h1>

<form action="/todos" method="POST">

  <label for="description">description:</label>
  <input name="description">

  <label for="urgent">urgent: </label>
  <span>yes</span><input type='radio' name='urgent' value="true" checked>
  <span>no</span><input type='radio' name='urgent' value="false"/>

</form>
```

The *name* attribute contains our `key`. What the user types into the text field or selects as a radio button is our `value`. This would get parsed server side as:

```js
req.body = {
  description: 'whatever the user wrote in the input box',
  urgent: 'true' // the default boolean
}
```

<details>

<summary>So, if you did send that over, how would you access the entire todo object (i.e.- the form body itself)?</summary>

`req.body`

</details>

<br />

<details>
<summary>How would you access the todo's description?</summary>

`req.body.description`

</details>

<br />

#### How to send information in a form!

To submit a form we need a submit button. Plus, let's encapsulate our label/input combos with some `divs` to get some bootstrap and grouping going on.

```html
<h1>Add a New Todo</h1>

<div class="container">
  <div class="row">
    <form action="/todos" method="POST" class="col">
      <div class="form-group">
        <label for="description">description:</label>
        <input class="form-control" type="text" name="description">
      </div>
      <div class="form-group">
        <label for="urgent">urgent:</label>
        <span>yes</span><input type="radio" name="urgent" value="true" checked>
        <span>no</span><input type="radio" name="urgent" value="false">
      </div>
      <div class="form-group">
        <input class="btn btn-outline-secondary" type="submit" value="Add a New Todo">
      </div>
    </form>
```

Boom! Form, done.

<br>

&#x1F535; **YOU DO**

Finish fleshing out your form with the code above.

<br>

<!-- #### Server Side

Some of the `routes/todos_controller.js` (specifically an INDEX route to show all of our todos, which we're storing in an array and our server/express configuration) is already written for you in the `starter-code`, we should walk through it to reiterate how it works.

```javascript
// routes/todos_controller.js

// This pulls in the module.exports object from
// data.js and assigns it to var data
// specifically it contains our seeded todos
var data = require('../data.js');


// Index shows all of a specific resource
// INDEX TODOS - returns All todos
router.get('/', (req,res) => {
  res.render('todos/index.ejs', {
    todos: data.seededTodos
  });
});
```

<br> -->


## Rendering `new.ejs`

Remember, the *new* action is just a GET request thta is there to render the form.  The form is the POST request that allows us to create a new Todo. The _form_ is the part that says "when you hit enter or hit my submit button, I'm going to POST to the `/todos`!"

> SIDE NOTE: If you have errors, be mindful of our discussion about why order matters when creating Express routes.

<br>

&#x1F535; **YOU DO**

We have our `views/todos/new.ejs` file that includes the form. Now write a **NEW** route to render it. Refer to the table earlier to determine what structure the path should have.

<details>
    <summary>Let's Add a NEW Todo Route</summary>

## Set-up Solution

```js
/* NEW TODO */
router.get('/new', (req, res) => {
  res.render('todos/new');
});
```

</details>

<br>

## Review

How are feeling about your understanding of the lesson so far?

- 1: Things are a bit shaky at the moment.
- 2: Not sure about a few things, but I'm understanding the overall concepts.
- 3: I'm feeling pretty good.

<br />

Get in groups of 3. Answer your question first, then work on the others. We'll come back and each group will explain the answer to your assigned question.

1. What does `CRUD` stand for? What are the 4 HTTP verbs that we're using to accomplish this?
2. What does REST stand for? What does it mean for our app to be stateless? How many REST-ful routes are in a conventional CRUD application?
3. Which REST-ful routes have views associated with them? What do they all have in common?
4. Assume an `actor` resource and a `views/actors/` directory. Create an INDEX route. The response object will return a view containing `{ data: actors }`.
5. Assume an `actor` resource and a `views/actors/` directory. Create a NEW route. The response will return a view.
6. Create a form to create a new instance of an `actor`. The form will contain fields for `name` and `mostRecentFilm`.
7. Explain `module.exports` and why we use it.
8. Explain `require()` and why we use it.
9. What is the routes folder?
10. What two attributes are needed for every form? What are the two HTTP verbs that forms accept?

<br>

-----

### BREAK

------

<br />

## Todo `CREATE` Route

Awesome! So, now we have a form that renders on the page. Let's engage in some Error Driven Development.

&#x1F535; **CFU**

![](https://i.imgur.com/QyawZUf.png)
<br>

- What happens when we add data and click the submit button?
- What's the difference between the 'new' route and 'create' route in terms of functionality?

&#x1F535; **I DO, THEN YOU DO**

Build a **CREATE** route in your `routes/todos_controller.js` using `router.post`. Don't worry about accessing the params from the form yet. Just send a response back ("Create a new todo is working!") to the client so you can verify the route works.

<details>
    <summary>Let's Add a CREATE Todo Route with res.send()</summary>

## Set-up Solution

```js
/* CREATE TODO */
router.post('/', (req, res) => {
  res.send("Create a new todo is working!");
});
```
</details>

<br>

## Receiving parameters from forms

So what happens when we hit the submit button? The request is made and we can see that the correct route is getting hit.

Up until now, how have we passed data/information from the client to the server?

Forms send data inside of what we call the **body** of a request (rather than inside of the `params` or the `queries`).

**GET** requests only send headers.

**POST** requests will send a header, and the parameters get sent in the body.

By default, Express doesn't have the ability to read the body sent by POST requests so we need to work with another package that the express generator automatically installs for us.

<br>

## `body-parser`

`body-parser` (https://github.com/expressjs/body-parser), is a library that parses the body of incoming POST requests and stores the data as a key/value pair on our `req` argument.

We access it via the `req.body` property.

<!-- #### moar configuration

1. Install body-parser using npm

```bash
$ npm install --save body-parser
```

Remember this npm command will fetch the module in question, install it on our server, and load it into our `package.json` -->

<!-- 1. Then inside our `app.js` we need to `require()` the module and configure it. -->
1. Then inside of our `app.js` you can see that the express generator has required body parser and added the app.use() method to configure it, just make sure to update the extended boolean to `true`.

```javascript
// around line 6
var bodyParser = require('body-parser');


// around line 21
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
    extended: true
}));
```

The params passed with a request will be "decoded" automatically, allowing you to use dot notation when working with JavaScript objects! Changing `extended` to `true` makes the objects more JSON-like.

1. In our `.post` route, change `res.send("Create a new todo is working!");` to `res.send(req.body);`

Now what happens when we submit a form?

<br>

&#x1F535; **CFU**

- Why are we using bodyParser?


&#x1F535; **YOU DO**

Look at where `body-parser` is defined in your `app.js`.

<br>

## What is middleware?

You might have noticed that we just used a slightly different method than we have in the past (`app.use()`). We've used `app.listen()` and `app.set()` so far, and now we've used `app.use(bodyParser...`

<br>

&#x1F535; **YOU DO**

Take ten minutes to read about middleware http://expressjs.com/en/guide/using-middleware.html

&#x1F535; **CFU**

- What kind of middleware is body-parser?

---


Middleware is simply code that can be executed anywhere between a request and a response.

In our app, we are logging out the server port once it has started - that is it. We get no other information about requests or errors. This is where Middleware comes in.

<!-- For an example, Let's write some middleware code that logs the type of request for any route that matches `/user`. We're gonna use a third-party library that's already required in the starter-app (`morgan`).

```javascript
app.use('/user/*', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
```

The `next()` is a function built into express, which will, when evoked, move on to the next function in the chain. In this case it explicitly means to continue with the request/response cycle and call the appropriate route based on the request. -->

<br>

## Use `body-parser`

`body-parser` is a third-party middleware. It will:

- add a `body` property to our `req` object
- parse the body of the incoming request, if there is one
- add the data as the value of `req.body`

Now that the form data is accessible via `req.body`. Our next step is to add our todo to the array of seededTodos.

&#x1F535; **CFU**

** 0 || 1 || 2 **

<br>

&#x1F535; **YOU DO**

1. Complete the `.post` route so that it saves the value of `req.body` to our array.
2. Use `res.redirect('/todos')` to redirect the route to the INDEX route rather than using `res.render(file)`

<details>
    <summary>Let's Finish our CREATE Todo Route</summary>

## Set-Up Solution

```js
/* CREATE TODO */
router.post('/', (req, res) => {
    var newTodo = {
        description: req.body.description,
        urgent: req.body.urgent
    };

    data.seededTodos.push(newTodo);

    res.redirect('/todos');
});
```

</details>

<br />

Why do we need to redirect after a `.post` action?

<br>

## LABTIME

<!-- 1. Build a SHOW route (`/todos/:id`) that will render individual todos in a  `show.hbs` view. **Break it down...**
    - First, get the route working. Test it out by using `res.send("Show route is working")`
    - Then, build a view and get it to `render` with `<h1>Show Route</h1>`
    - Finally, add the code to display the todo. For example:

    ![](https://i.imgur.com/Bi2U4Lw.png) -->

1. Add a bootstrap navbar as a partial to your views, with links to your INDEX (`/todos`) and NEW(`/todos/new`) routes. And add the number of to dos to your index page title. For example:

    ![](assets/navbar.png)

2. Create partials for your `head` and `footer` sections and include them in your app's files.

3. Make each to do on the INDEX page clickable (hint: use anchor tags) so that each individual to do, when clicked, will link to it's correct show page (like the image above).

4. If there are no todos, the index page should display:

```html
<h1> You have no To Dos! </h1>
<p>Add one by clicking on the button below!</p>
```

(HINT: look up conditionals in the ejs documentation.
Hint #2: You'll probably have to remove the `seededTodos` in `data.js` to test this out)

5. Add buttons on the show + new pages to link back to the home page.  

6. Add a button on the home page that links to the new to do page.

<!-- 5. Add an external stylesheet and javascript files to the app (check out the Express Views lesson). -->
7. Add a `complete` or `incomplete` field to the seededTodos and the form.

8. Create a view for your Home/Root route.

9. Add edit and delete buttons to your show page.

BONUS:

10. Add a `done` field to your seededTodos and the form.

11. Create separate sections on your INDEX page for complete and incomplete todos.

12. Add validations to your form so the user can't submit a blank option.

<!-- 13. Start from scratch! Build an INDEX, SHOW, NEW, and CREATE routes for a movie resource. Give each movie a `title`, `yearReleased` and `starRating` fields (1-5). -->


<br>

---

<details>

<summary>**RECAP: ClientSide**</summary>

---
### CLIENT vs SERVER
---

Data is sent from the client to the server, (from our browser to our express app). In the browser, we use HTML forms as an easy, user-friendly way to set up the right structure for the muggles using our website.

This way our users can create new todos and update our database without any knowledge of http, routes, javascript, arrays or any of it really! The submit button is a one stop shop that as we've now learned triggers a whole series of processes.


### The form

The form is just an html element that allows us to define how we want the data to be sent. The various attributes we configure let us set where the data is sent when the button is triggered. Remember how in our last unit we wanted to prevent default behavior when the submit button was clicked? We no longer have to do that! Let that default behavior run free!

#### action form attribute

The **action** attribute is _essential_.  It configures what route to send the data to in the form. In our example above, it is sent to the `/todos` route, but it could be anything we want. If we had a recommended song app, that route might be `/songs`. If we wanted to track ufo sightings in New Mexico, it might be `/sightings`.

#### method form attribute

The **method** attribute refers to which HTTP verb we want to use. As you'll see in our next lesson, this can get a little hairy with PUT and DELETE, but this morning, we're just specifying that it needs to be a POST request. Why is it not _essential_ like the action attribute is? Because the form will always default to a GET request for whatever route is set in the action attribute.


## POST vs GET

You might remember that we sent all kinds of data to the server on Tuesday using our :dynamic_segments and query parameters using GET requests. So why don't we just use that method to add ToDos? Semantics.

A GET request, is specifically used by the browser to _ask_ for some resource, an image, a song, a todo, etc, and use the data appended in the url to augment that request.

POST requests are specifically used to create a resource on the server.

POST requests send their data in the _body_, hidden from the user.

The **http request** looks like this:

```
POST / HTTP/1.1
Host: /todos
Content-Type: application/x-www-form-urlencoded
Content-Length: 36

description=take%20out%20the%20trash

```

</details>

<br />

<details>

<summary>**RECAP: ServerSide**</summary>

---
### RECAP: Server Side
---

### Routes

**Routes** are defined in the `routes` folder and when the server is running. They listen for incoming HTTP requests on the specified PORT.

the basic route syntax looks as follows:

```javascript
router.method('/route', function(req, res) {
  // the code that needs to run when this route is called
})

```

### req vs res

#### req argument

The `req` argument is an object that contains information about the incoming HTTP request:

- req.route
- req.query
- req.params
- req.body (see bodyParser below)

#### res argument

The `res` argument is another object that contains information about the response we want to send to the server.

We initially started with using res.send to just send text to the browser, when using ejs. However, we can use res.render to render an html/ejs file.

We can also use res.redirect to trigger another route before sending a response back to the browser

## bodyParser

Express is a very lightweight framework and doesn't have the functionality to read the data sent in the body of a POST request without including this bodyParser module.

Configuring bodyParser on our server adds the `body` property to our `req` object so that we can easily access parameters sent by POST requests.

If you aren't using the express generator, which adds it as a dependency for you, you can configure it as follows:

```bash
$ npm install body-parser --save
```

```javascript
var bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({ extended: true }));
```
</details>

<br>

## Resources

[Bit further ahead: module.exports vs exports](http://www.hacksparrow.com/node-js-exports-vs-module-exports.html)
