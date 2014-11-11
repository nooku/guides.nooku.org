# Creating the Database Table

Let's define the basic database schema for our component. Execute the following SQL statement in your MySQL client,
such as [PHPMyAdmin](http://www.phpmyadmin.net/home_page/index.php).

Before doing so, replace the prefix '#__' with the prefix you chose during Joomla installation.

    CREATE TABLE IF NOT EXISTS `#__todo_items` (
      `todo_item_id` SERIAL,
      `title` varchar(255) NOT NULL,
      `description` longtext NOT NULL
    );

## Naming Conventions Explained

Have a good look at the [Essentials > Naming Conventions](/essentials/naming-conventions.md) page. There is an easy for follow section for the database side of things.

The name of the table is `#__todo_items`. 'todo' is the name of our component, followed by the name of the entity we want
to store in this table which in this case is simply 'items'. This name should always be plural; a table is always a collection of items.
So the database table naming convention is:

`dbprefix` + `component name` + `_`  +`entities`

Next we have the primary key. Every table should have one. We define it as SERIAL.

> SERIAL is an alias for BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE.

The name of the primary key is `todo_item_id` . Again we use the component's name 'todo', followed by the name of the entity,
in singular this time, since it describes one item. In other words the primary key name format is:

 `component name` + `_` + `entity_id`

## Insert the Data Please!

Now we insert some sample data into the blog table. Again, replace #__ with your database prefix:

    INSERT INTO `#__todo_items` (`title`, `description`) VALUES
        ('todo item one', 'This is the first Todo'),
        ('todo item two', 'This is the second Todo'),
        ('todo item three', 'This is the third Todo');


Now we have a database defined let's delete the controller we created in the previous part. For this delete the controllers
folder and enclosed file in:

    DELETE     /components/com_todo/model/items.php

If you go back to http://joomla.dev/todo_tutorial/index.php?option=com_todo&view=items, you will see that even though you
have deleted your model, the page still renders as it did before.

**Remember, we only needed to create that file because we didn't have an associated table yet. Now we do!**

## Great!

You have created your database table for your Todo items and registered your component as a Joomla! extension.

## Up Next: Your Todo Item Views


