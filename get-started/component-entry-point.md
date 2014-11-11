# Component Entry Point

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

