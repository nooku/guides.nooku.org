# Todo Component Backend

Now we're going to create the back-end for our component, com_todo.
In other words we will create the environment for an administrator to manage all the Todo Items in the system: create/modify/delete Items.

## The Entry-Point aka "The Component Loader"

The entry-point contains the very first code that gets executed when you visit

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo

To get going just like the Front End component create the following file

    /administrator/components/com_todo/todo.php

Then place the following code in that file:

```php
<?php echo KObjectManager::getInstance()
          ->getObject('com://admin/todo.dispatcher.http')
          ->dispatch();
```

**Tip:** This file is the exact same as in the Front End version of the com_todo entry point except that the dispatcher
identifier is for `admin` instead of `site`.

This line simply loads the dispatcher and calls the `dispatch` action.

Now, try pointing your browser to

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo.

*You will get an error, something like*
![No Todos Todos Table.](/resources/images/todotutorial/backend-todos-error.png)

When there is no `view` parameter in the request, Nooku tries to load the pluralized name of the component as its `view`, in our case **'todos'**.
But this won't work because we haven't defined the view by that name, nor have we defined a table to match.

As you can see the `view` is very important. It tells the component dispatcher which `Controller` to load, which loads the `Model`
 and the actual `View` object. The `view` in a Nooku Framework component is fundamental. This is why that at the very least the folder
 structure and the template of the view needs to be defined.

The Framework needs to know how you want that information rendered.

## Create a View

Let's recreate (or _not create_ as the case may be) the Items View as we did in the Front End version, but this time use a table to display the data. Just create the file

    /administrator/components/com_todo/view/items/tmpl/default.html.php

And add the following did before

```php
    <table>
        <thead>
            <th><?=translate('ID') ?></th>
            <th><?=translate('Title') ?></th>
            <th><?=translate('Description') ?></th>
        </thead>
      <? foreach($items as $item) : ?>
            <tr>
                <td>
                    <?= $item->id?>
                </td>
                <td>
                    <a href="<?= route('view=item&id='. $item->id ) ?>">
                                <?= $item->title ?>
                       </a>
                </td>
                 <td>
                    <?= $item->description?>
                </td>
             </tr>
        <? endforeach; ?>
    </table>
```

With the this template file in place and point the browser to

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo

_we still get an error "View: todos not found"_

### Don't get discouraged! All of this has a point!

The Framework is still trying to load the same "todos" view, because we haven't told it to do any different.

## The Dispatcher

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

Looking at the first line; notice that we're extending the `ComKoowaDispatcherHttp`. This is the class which gets loaded
in response to an HTTP request if we don't make our own dispatcher class.

>For reference, its located at `/libraries/koowa/components/com_koowa/dispatcher/http.php`.

In our class, we're telling our component to load the "item" controller as the default. Which is the same as saying:

>When there is now view param in the request, we want to see items

Now if we refresh our page

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo

The Framework will redirect the request to

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo&view=items

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

1) We need a controller that doesn't expect a model. The fallback `ComKoowaControllerDefault` descends from `KControllerModel`,
which expects an associated database table. We need to our dashboard controller to extend `ComKoowaControllerView` instead.

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

That's it. Pointing your browser to http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo will now redirect and load your new Dashboard.
![Todos Default Dashboard.](/resources/images/todotutorial/backend-todos-dashboard.png)

## 3. The Controller Package

Lastly, a little bit about the Controller package. Aside from the example for a Dashboard above there is no need for us to create
a Controller for the Todo Items. The behavior provided by the `ComKoowaControllerModel` is robust and fulfills all of our needs,
and this is the class that the Framework falls back to.

**Because we have defined a database table that conforms to the naming conventions that the Framework expects, we don't need to define a class.**

### Controller Toolbars

One of the more important pieces in the backend is the Toolbar. The administrator toolbar is the series of command buttons at the
top of the screen that you are all familiar with. For our component to work properly and be user friendly, we need to
be able to create, edit and delete Todo items. And guess what, if you don't need anything special, you don't have to create these either as Nooku
give you a nice implementation out of the box.

We touch on the Toolbars a little more in the next section.

## Up Next: Editing Your Data


