# The Front End

## Register the Component

The first thing we need to do is make sure our component is registered in the database (however, this is done automatically when installing a new component
with the normal Joomla! installer).

We know that our component is going to be called `com_todo` so we can perform an insert with the following code in the database.

> For those of you are using the Joomlatools vagrant box, you can use http://phpmyadmin.joomla.dev

*Replace '#__' with your database prefix:*

For Joomla 2.5.x and 3.x
```mysql
    INSERT INTO `sites_todo_tutorial`.`#__extensions`
        (`extension_id`, `name`, `type`, `element`, `folder`, `client_id`,
         `enabled`, `access`, `protected`, `manifest_cache`, `params`,
         `custom_data`, `system_data`, `checked_out`, `checked_out_time`,
         `ordering`, `state`
         )
    VALUES
        (NULL, 'com_todo', 'component', 'com_todo', '', '0', '1', '1',
        '1', '', '', '', '', '0', '0000-00-00 00:00:00', '0', '0');
```
With that done, we are free to dive into our component development.

## Component Entry Point

If you have ever developed a Joomla! component before you know about the _component entry point_, sometimes referred to as the _component loader_.
The file in most cases in Joomla! loads a controller and fires the execute method on that controller. We're doing something different and loading
and firing our own dispatcher to take care of all that.

So, just create this file:

    /components/com_todo/todo.php

And then add the following line of code to the file:

```php
<?php echo KObjectManager::getInstance()                     // load the Object Manager
            ->getObject('com://site/todo.dispatcher.http')  // Get an HTTP Dispatcher
            ->dispatch();                                   // call the dispatch action
```


## The "Hello World!" Todo Items

If you have read the [HMVC](/essentials/hmvc.md) article, you will know that it's the view's responsibility to render things to the screen.
So that's what we'll do next:  _Create a View_.

The first view we will add, is the view that renders our list of todo items. For this, create the following file and folder structure:

    /components/com_todo/view/item/tmpl/default.html.php

> Notice that the folder name "items" is plural, and not "item", which is singular.
> This is part of the framework's [Naming Conventions](/essentials/naming-conventions.md): Since we're creating a view that's
going to display multiple todo items, we use the plural form "items". Later on we will set up the view that displays a single blog
post, which will have a singular name, "post".

We don't have a list of items to display just yet, so inside the newly created default.html.php just add the following line for now:

```php
    Hello World, and welcome to my Todo Items!
```

### Add a Model...For now

Also in the [HMVC](/essentials/hmvc.md) article you learned that a controller loads a model. The default controller that Nooku
would create for you expects a model, which in turn expects a database table by default. As we don't have a database yet, we will
bypass this behavior by extending our items model from `KModelAbstract` instead. Please create the the following file and folder structure:

     /components/com_todo/model/items.php

And then add the following line of code to the file:

```php
    <?php class ComTodoModelItems extends KModelAbstract {}
```

**We're going to get rid of this later when we set up our database.**

Now, try opening [http://joomla.dev/todo_tutorial/index.php?option=com_todo&view=items](http://joomla.dev/todo_tutorial/index.php?option=com_todo&view=items)
 in your browser. If you set everything up correctly you should see something like

![Hello World, and welcome to my Todo Items](/resources/images/todotutorial/hello-world.png)

## What just happened?

As you may have noticed, we didn't create a view class at all. Nor did we create a controller. The model we created was simply to bypass
the framework default. In the cases where the Framework can't find a class that you need represented in the Component's MVC Context, i.e. item controller, a item model
or a items view, it will iteratively [Fallback](/essentials/object-management.md) to classes through a hierarchy of classes provided by the
framework and use them instead.

_This is part of what we call Nooku Magic:_

**If you're not trying to accomplish anything out of the ordinary, Nooku Framework will often do the job for you.**

In other words: Don't add code until you're sure you really need it. More on this in the following parts of this tutorial.

## Up Next: Creating the Database