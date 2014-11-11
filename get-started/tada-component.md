# Getting Started with Nooku

## Todo Component Tutorial Series

The best way to showcase the core functionality of the Framework is to show you step by step how to build something real.

## Try Using Our Tools

Before starting this tutorial, you should have already set up Joomla! with Nooku Framework installed or be working with Nooku Platform.
For the sake of this tutorial, we will assume that you have used the Tools from [developer.joomlatools.com](http://developer.joomlatools.com)
on your local machine, and can be reached at [http://joomla.dev/todo_tutorial/](http://joomla.dev/todo_tutorial/).

After you get your vagrant box set up and running, from the command prompt run

    $ vagrant ssh

When inside the box, use the `joomla` command line tool to create a new Joomla site

    $ joomla site:create todo_tutorial

Create a composer file in your new todo_tutorial directory

    $ cd /var/www/todo_tutorial;

    $ vi composer.json

and paste the following

```javascript
{
  "require": {
    "nooku/nooku-framework": "2.0.*"
  }
}
```

Lastly, run

    $ composer install

With all that done you should be good to go to get started with doing your first Nooku Framework powered component.

