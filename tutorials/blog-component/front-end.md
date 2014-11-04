# The Front End

Before starting this tutorial, you should have already set up Joomla! with Nooku Framework installed or be working with Nooku Platform.
For the sake of this tutorial, we will assume that you have used the Tools from [developer.joomlatools.com](http://developer.joomlatools.com)
on your local machine, and can be reached at [http://joomla.dev/tutorials/](http://joomla.dev/tutorials/).

## Component Entry Point

If you have ever developed a Joomla! component before you know about the _component entry_ point, sometimes referred to as the _component loader_.
The file in most cases in Joomla! loads a controller and fires the execute method on that controller. We're doing something different and loading
and firing our own dispatcher to take care of all that.

So, just create this file:

    /components/com_blog/blog.php

And then add the following line of code to the file:

```php<?php echo KObjectManager::getInstance()->getObject('com://site/blog.dispatcher')->dispatch();```


## Hello World Blog Post

If you have read the [HMVC](/essentials/hmvc.md) article, you will know that it's the view's responsibility to render things to the screen.
So that's what we'll do next:  _Create a View_.

The first view we will add, is the view that renders our list of recent blog posts. For this, create the following file and folder structure:

    /components/com_blog/views/posts/tmpl/default.html.php

> Notice that the folder name "posts" is plural, and not "post", which is singular.
> This is part of the framework's [Naming Conventions](essentials/naming-conventions.md): Since we're creating a view that's going to display multiple blog posts, we use the plural form "posts". Later on we will set up the view that displays a single blog post, which will have a singular name, "post".

We don't have a list of blog posts to display just yet, so inside the newly created default.html.php just add the following line for now:

```php
    Hello World, and welcome to my blog!
```
Also in the [HMVC](/essentials/hmvc.md) article you learned that a controller loads a model. The default controller that Nooku
would create for you expects a model which expects a database table by default. As we don't have a database yet, we will
bypass this behavior by extending from ComKoowaControllerView instead. Please create the the following file and folder structure:

     /components/com_blog/controller/post.php

And then add the following line of code to the file:

    <? class ComBlogControllerPost extends ComKoowaControllerView {}

Now, try opening [http://joomla.dev/tutorials/index.php?option=com_blog&view=post](http://joomla.dev/tutorials/index.php?option=com_blog&view=posts) in your browser
and see if our message reaches your screen. Which it should, if you have set up everything correctly so far.

## What just happened?

As you may have noticed, we didn't create a view class at all. Nor did we create a controller (except bypassing the default one), or a model either.
In the cases where the Framework can't find a class that you're implying that you're using, in this case a posts controller, a posts model
or a posts view, it will just [Fallback](/essentials/object-management.md) to classes provided by the framework and use them instead.

_This is part of what we call Nooku Magic:_

**If you're not trying to accomplish anything out of the ordinary, Nooku Framework will often do the job for you.**

In other words: Don't add code until you're sure you really need it. More on this in the following parts of this tutorial.