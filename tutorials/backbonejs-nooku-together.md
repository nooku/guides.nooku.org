# Tutorial: Using Backbone.js with Nooku
<!-- toc -->
## Skill Level
Intermediate

## Relevant Sections and Classes

KContollerView, KViewJson, Joomla Todo Extension, Bootstrapper, TodoMVC.com, Joomlatools Vagrant Box, Object Manager

## Goal

Create a Backbone.js powered client-side app and use Nooku to provide the REST service backed. To get started quickly, we will use our existing example [Joomla Todo](https://github.com/nooku/joomla-todo) extension and make use of the Backbone.js example that is part of [TodoMVC](http://todomvc.com/).

>TodoMVC.com showcases and compares most of the major clientside frameworks by solving the same todo list example (name mention the devs here).

The goal of this tutorial is to incorporate Backbone.js, Nooku and Joomla into one working component.
Setup

For this tutorial we will assume you have the Joomlatools Vagrant Box running locally.  Also, you should have completed the Nooku Getting started tutorial to make sure you have some of the core concepts covered.

If not, make sure you have at least the Box running and know how to use the Joomla console. You can find detailed instructions on the Joomlatools developer site. Assuming that your box is up and running, simply start an SSH session with

```shell
$ vagrant ssh
```

We are going to create a fresh Joomla install for this tutorial. Run the following Joomla Console command:

`
$ joomla site:create todo # change the site name if you already have a ‘/var/www/todo/’ site.
 `

Last, we are going to use [Composer](https://getcomposer.org/) to install a special branch of our Joomla Todo package. This one has the TodoMVC Backbone package already included and ready for use to make our changes.

```shell
$ cd /var/www/todo
$ composer require nooku/joomla-todo:dev-tutorials/backbone#866bc29c60f599f5b5192e7f1e1a4ca44b960c0d 
```
> The '318740f1221e4c3b1d7642ce1a1162a0460cc4e4' is the revision number of the tutorial starting point. We've included a similar **Composer command** at the end of each section. You can choose to follow along and do the coding yourself, or just see the changes by running those commands. If you just want to use the finished code simply remove the '#' and everything after.
 
With all that done we can get to work powering the todo list with data from the Nooku back end.

## TodoMVC Backbone Example...Where?

We are going to use the TodoMVC CSS and Javascript to make things easy. You could write your own CSS to make things blend better, as this is merely a tutorial the default will do.
To help things along we've placed the Backbone.js assets in the Joomla `/media/com_todo/lib/backbone` folder for you already.

### Create the "Backbone" View and Controller

There are a number of approaches that you can take to serve up your single page application. We are going use PHP to produce and serve the initial HTML markup and assets by adding a new ‘View’ to the `com_todo` component.

> An advantage to using PHP to render our page is the potential to send the initial payload of data along with the original page request, instead of having to do another round trip to the server.

**Here are the steps:**

1. Add a new folder to the `/site/components/com_todo/views/` directory view called **backbone**
2. Then add another **tmpl** to that directory;
3. Finally, add a new file called `default.html.php`. This will hold all the markup for our single page application, loading all of the Javascript and styling we need.
4. Place the following code in `/com_todo/controller/backbone.php`

```php
class ComTodoControllerBackbone extends KControllerView {}
```

Without this last step any call to get our new view will fail miserably. In order for a request to `view=backbone` to work we also need to create an _empty_ controller that extends [`KControllerView`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/view.php).

> Nooku’s default controller is [`KControllerModel`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/view.php), and as such, expects to receive data from a database connected model.  We don’t have a table (#__todo_backbones) so we need to extend directly from KControllerView which doesn’t expect a model, only a view.

At this point, your new file structure should look something like :

![New Backbone View and Controller to support our single page application](/resources/images/backbone-tutorial/backbone-controller-view-added.png)

If you now go to http://joomla.dev/todo/component/todo/backbone you should see an empty component area.

> Just in case you didn’t notice: we did not need to create a view class, and we barely needed to create the controller class.

**Composer command:** `composer require nooku/joomla-todo:dev-tutorials/backbone#2251f54cee839856e8291f1316ac2e599bc853c5`

## Build the Single Page Application Markup

To begin to show the application’s Todo list, we take the inner html of body tag from the existing `/media/com_todo/lib/backbone/index.html`:

1. place the contents of the `<body>` tag  into our new file at `/components/com_todo/views/app/tmpl/default.html.php`
2. add `data-inline` to all of the `<script>` tags, including those of `type=”text/template”`.
3. update all the `src` attributes of the scripts with the new locations of their respective resources, using the `media://` alias, e.g. `media://com_todo/lib/backbone/js/backbone.js`.
> Note, you can remove the jquery.js reference, and be sure to be sure to note `backbone.localStorage.js` asset has been moved  to `/media/com_todo/lib/backbone/localstorage/`.

4. Add these two <ktml:style> tags:
```html
<ktml:style src="media://com_todo/lib/backbone/todomvc-common/base.css" />
<ktml:style src="media://com_todo/lib/backbone/todomvc-app-css/index.css" />
```

> We need to add `data-inline` to the script tags to ensure that the scripts stay in place and aren’t filtered and placed in the `<head>` tag of the page which would happen otherwise.


Pointing your browser now to http://joomla.dev/component/todo/backbone should show you:

![TodoMVC Backbone Example in Joomla Todo Component](/resources/images/backbone-tutorial/todomvc-backbone-in-com-todo-package.png)

At this point everything looks nice and does store todo list items to ‘localstorage’ in your browser, because the TodoMVC example has that implemented out of the box. To implement our Nooku powered persistence we’ll need to tell Backbone where to go. If you are already a Backbone rockstar you can likely skip over the **Understanding Backbone** section.

**Composer command:**` composer require nooku/joomla-todo:dev-tutorials/backbone#b507a4bf13604c5e4fd22173cd35111e34f0980d`

## Understanding Backbone

Let’s just go over quickly the important pieces we need to know about Backbone.js to better understand how everything works.

### Models

Backbone Models represent one entity item, containing a representation your application data. You can think of it as one row object in your database. The Model holds processing logic for that data as well.
>Backbone Models are the basic data object in the framework — frequently representing a row in a table in a database on your server. A discrete chunk of data and a bunch of useful, related methods for performing computations and transformations on that data.
[Annotated Backbone.js](http://backbonejs.org/docs/backbone.html#section-37)

### Collections

Backbone Collections are lists or sets of Models but they can and do hold more processing logic as it relates to these lists.
>If models tend to represent a single row of data, a Backbone Collection is more analogous to a table full of data … or a small slice or page of that table, or a collection of rows that belong together for a particular reason — all of the messages in this particular folder, all of the documents belonging to this particular author, and so on. Collections maintain indexes of their models, both in order, and for lookup by id.
[Annotated Backbone.js](http://backbonejs.org/docs/backbone.html#section-39)

### The “Sync”

The [Backbone.sync](http://backbonejs.org/docs/backbone.html#section-82) defines how the library deals with persisting data to the server. Both the Backbone.js Model and Collection objects have a `sync` method that proxy `Backbone.sync` by default.

We have the ability to change the persistence strategy by overriding this method. For example, we would want to extract the [`entities` property](http://guides.nooku.org/json-api/document-structure/entities.html) of Nooku’s default response and populate the model or collection `attributes` property. We may wish to store some of the other top level properties as similarly named properties in the model or collection.

### The URL

The most relevant  piece of the server persistence strategy of Backbone are the respective `url` properties of the Collection and Model. The framework assumes that the Collection’s url will be the plural representation, and that a specific model can be requested by attaching the `id` to the end of that url. For example, if the endpoint for a list of todos was /todos/, then the first todo could be accessed through /todos/1.

The `Backbone.sync` method uses [underscore’s nifty `result` method](http://underscorejs.org/docs/underscore.html#section-161) to process the url property of both in the same way. `_.result(object, property, fallback)` figures out if that property is a function and executes it if so, otherwise it simply returns the value of the property. Because of this you can define more complex url strategies, which is exactly what the default `Backbone.Model.url` property does.

`Backbone.Model.url()` is in fact a function and uses `_.result` in succession to check if a `urlRoot` property is present in the Model, or whether the Collection that is associated with this Model has a `url` property defined; either of which must be true or an error will get registered in the console.

## Setting the Collection Url

With all that new Backbone knowledge we see that we need only define the TodosCollection url property in /media/com_todo/backbone/js/collections/todos.js:

```javascript

    var Todos = Backbone.Collection.extend({
        model: app.Todo,
                 // Save all of the todo items under the `"todos"` namespace.
        //       localStorage: new Backbone.LocalStorage('todos-backbone'),
        url: '/todo/component/todo/tasks',
```

**Make sure to comment out the local storage setting.**

You can open http://joomla.dev/todo/component/todo/tasks/?format=json in the browser to see the JSON response at any time.

> Joomla URLs: If we wanted a ‘nicer’ url to use, we could specify a Menu item in Joomla with the route `/todo/todos` and the CMS would route the request accordingly.

**Composer command:**` composer require nooku/joomla-todo:dev-tutorials/backbone#255a0d26bb3ce0a0036b85f82f38a8c811a4a477`

## Specialising the JSON output

The response that Nooku returns out of the box extends the [JSONAPI](http://guides.nooku.org/json.html) standard and has more information than the Backbone needs or expects.  Backbone expects responses to GET requests for collections to be returned from the server as a JSON array of models (JSON objects).
```
[
	{id: 1, title: "take in dry cleaning"},
	{id: 2, title: "pick up in dry cleaning"}
]
```
It also expects a request for a singular item to be returned as a plain JSON object (without the JSON API spec meta, limits, etc).

We've already discussed one option above in changing `Backbone.sync`, but we can also override the schema of the JSON that our Todo package returns from the server. Since we are focusing here on how Nooku can fit into what the client side needs, we’ll do just that. We can adjust the JSON response by creating our own view classes in the Todo package for `tasks` and `task` for that format.

Here’s an example for the singular `task` response that you should place at `/components/com_todo/view/task/json.php` :

```php
class ComTodoViewTaskJson extends KViewJson
{
    /**
     * Simplified render action
     * @param $context
     * @return mixed
     */
    function _actionRender($context)
    {
        if(!$this->_content)
        {
            // set the content of the view.
            $this->_content = $this->_renderData();
        }

        return parent::_actionRender($context);
    }

    /**
     *  Get the properties of the entity in an array.
     * @return mixed
     */
    protected function _renderData()
    {
        // get the data
        $data = $this->getModel()->fetch();

        // extract the properties of the entity object
        $output = $data->getProperties();
        return $output;
    }
}
```

We also need to create the plural form by extending `ComTodoViewTaskJson` so we get to use the same `_actionRender` method. Place the following code in a new file at `/components/com_todo/view/tasks/json.php` .

```php
class ComTodoViewTasksJson extends ComTodoViewTaskJson
{
    /**
     * Get the entity list as an array
     * @return array
     */
    protected function _renderData()
    {

        $data = $this->getModel()->fetch();

        // extract the properties of the entity object
        $output = array_values($data->toArray());
        return $output;
    }
}
```

Great! Now Backbone.js will get the response schema it’s looking for.  Refresh your browser: all the tasks that came installed with com_todo should now show up in the list:

![TodoMVC Getting Data from Nooku Backend](/resources/images/backbone-tutorial/todomvc-with-com-todo-tasks-loading.png)

**Composer command:**` composer require nooku/joomla-todo:dev-tutorials/backbone#5d826b846da44770f93bf05655d555c265b07a68`

## Saving new Tasks: Handling CSRF tokens

Nooku aims to do as much for you out of the box as it can, and so adds some security when it comes to saving data to the server. To make ‘not safe’ requests, i.e. POST, PUT or DELETE methods, a user must be logged in by default; and you must send a valid CSRF Token along with the request.

> The Nooku HTTP Dispatcher adds the the ‘X-CSRF-Token’ header and `csrf_token` cookie to each GET request, so its available for us to use.

We handle that readily by getting and storing that csrf token from the response headers of the GET requests Backbone initiates; then we can add that same token to all ajax requests sent through jQuery Ajax object, i.e. Backbone.$.ajax.
Let’s make a new Javascript file at `/media/com_todo/lib/backbone/csrf.js` and add these two Ajax event handlers.

```javascript

Backbone.$.ajaxSetup({
    // make sure we send our csrf token
    beforeSend: function(xhr, settings) {
        if(settings.data != undefined) {
            // settings.data is a string, need the object
            var data = Backbone.$.parseJSON(settings.data);
            data.csrf_token = Backbone.csrf;
            // turn it back into a string.
            settings.data = JSON.stringify(data);
        }
    },
    // set the csrf for the Backbone namespace
    complete: function(xhr, message) {
        // for successful requests
        if (xhr.status == '200') {
            Backbone.csrf = xhr.getResponseHeader('X-Csrf-Token');
        }
    }
    
});
```
With that code in place we add a script tag to our `default.html.php` layout, right after the loading of backbone.js:

```html
<script data-inline src="media://com_todo/lib/backbone/js/backbone.js"></script>
<script data-inline src="media://com_todo/lib/backbone/js/csrf.js"></script>
```

>If you are using the Vagrant setup mentioned above you can simply login with `admin/admin`.

**Composer command:**` composer require nooku/joomla-todo:dev-tutorials/backbone#fbbedf2d1ec66e9a7187875f9a6a296e130d6653`

## Let’s Save a ‘task’ to our List

We are finally at the point where we can save a task to our list. Open your inspector, and look for the tab that shows you all of the page’s requests. Then try adding an item to your todo list. You should see a new request with `POST` as its method and if all goes well the server should respond with `201 Created` status message:

![TodoMVC Backbone first post to Nooku backend](/resources/images/backbone-tutorial/first-todo-task-post.gif)


## Wrapping up

That’s it in a nutshell! We have integrated the TodoMVC Backbone.js example with the Nooku Framework example Todo Extension.  The modifications are actually quite minimal. Most of the work was done in changing the schema of the JSON response with the new JSON View class to match the output expected by Backbone.
Let’s recap the steps:


1. We created a new App View layout and empty controller, because we need no database table.
2. We changed the way com_todo formats the JSON output to match what Backbone.js expects.
3. We changed the `url` property of the `TodoCollection` to our component’s todo collection view.
4. We handled the need for CSRF authentication tokens by sending them with each request with some light Javascript.
