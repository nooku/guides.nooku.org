# The "Hello World!" Todo Items

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

## Add a Model...For now

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
