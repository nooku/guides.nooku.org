# Using JoomlaTools

Before starting this tutorial, you should have already set up Joomla! with Nooku Framework installed or be working with Nooku Platform.

For the sake of this tutorial, we will assume that you have used the Tools from [developer.joomlatools.com](http://developer.joomlatools.com)
on your local machine, and can be reached at [http://joomla.dev/todo/](http://joomla.dev/todo/).

After you get your vagrant box set up and running, from the command prompt run

    $ vagrant ssh

When inside the box, use the `joomla` command line tool to create a new Joomla site

    $ joomla site:create todo

Create a composer file in your new todo_tutorial directory

    $ cd /var/www/todo;

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

With all that done you should be good to go to get started with doing your first Nooku Framework powered extension.

