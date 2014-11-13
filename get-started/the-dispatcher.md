# The Dispatcher

In our case we don't want to have a view named "todos", but would like our default view to be a list of the Todo "items" in the system.
To get this done we have to create a specialized dispatcher that will override the Framework's default.

First, create the file

`/administrator/components/com_todo/dispatcher/http.php`

and insert the following code

```php
    <?php
    class ComTodoDispatcherHttp extends ComKoowaDispatcherHttp
    {
        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'controller'	=> 'item'
            ));
            parent::_initialize($config);
        }
    }
```

Looking at the first line; notice that we're extending the [`ComKoowaDispatcherHttp`](http://api.nooku.org/class-ComKoowaDispatcherHttp.html). This is the class which gets loaded
in response to an HTTP request if we don't make our own dispatcher class.

>For reference, its located at `/libraries/koowa/components/com_koowa/dispatcher/http.php`.

In our class, we're telling our component to load the "item" controller as the default. Which is the same as saying:

>When there is now view param in the request, we want to see items

Now if we refresh our page

http://joomla.dev/todo/administrator/index.php?option=com_todo

The Framework will redirect the request to

http://joomla.dev/todo/administrator/index.php?option=com_todo&view=items

_which is great!_

We already defined our default layout for a list of our items, so we get something like
![Success, our first Todos Table.](/resources/images/todotutorial/backend-todos-first-list.png)

#### Optional Exercise: Create a Dashboard

The above is the simplest way to get your backend going. However, there is often a desire to create some kind of Dashboard
in the administrator application side of a component. Maybe you want some statistics related to your Todos, maybe some instructions
for other admins using your component, or even data from another part of the system that you think is relevant.

Note: If you haven't read about the MVC architecture you are strongly advised to do so. Being familiar with MVC is required
to understand the Framework.

In this case a Dashboard
A) is singular
B) won't have a single table or model associated with it

To implement this view and have it be our default screen we'll need to do a few things.

1) We need a controller that doesn't expect a model. The fallback [`ComKoowaControllerDefault`](http://api.nooku.org/class-ComKoowaControllerDefault.html)
descends from [`KControllerModel`](http://api.nooku.org/class-KControllerModel.html), which expects an associated database tabl
e.
We need to our dashboard controller to extend [`ComKoowaControllerView`](http://api.nooku.org/class-ComKoowaControllerView.html) instead.

We create the file

`/administrator/components/com_todo/controllers/dashboard.php`

And add the following code

```php
<?php  class ComTodoControllerDashboard extends ComKoowaControllerView{}
```

2) Then create a template layout

`/administrator/components/com_todo/view/dashboard/tmpl/default.html.php`

And add the following code

```php
<h1>Todo Dashboard</h1>
```

3) Update the `ComTodoDispatcherHttp` and make `dashboard` the default controller instead

```php
    <?php
    class ComTodoDispatcherHttp extends ComKoowaDispatcherHttp
    {
        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'controller'	=> 'dashboard'
            ));
            parent::_initialize($config);
        }
    }
```

That's it. Pointing your browser to http://joomla.dev/todo/administrator/index.php?option=com_todo will now redirect and load your new Dashboard.

![Todos Default Dashboard.](/resources/images/todotutorial/backend-todos-dashboard.png)
