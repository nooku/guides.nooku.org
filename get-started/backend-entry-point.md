# The Backend Component Loader

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
