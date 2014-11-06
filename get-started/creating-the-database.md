# Creating the Database Table

Let's define the basic database schema for our component. Execute the following SQL statement in your MySQL client,
such as [PHPMyAdmin](http://www.phpmyadmin.net/home_page/index.php).

Before doing so, replace the prefix '#__' with the prefix you chose during Joomla installation.

    CREATE TABLE IF NOT EXISTS `#__tada_items` (
      `tada_item_id` SERIAL,
      `title` varchar(255) NOT NULL,
      `description` longtext NOT NULL
    );

## Naming Conventions Explained

Have a good look at the [Essentials > Naming Conventions](/essentials/naming-conventions.md) page. There is an easy for follow section for the database side of things.

The name of the table is `#__tada_items`. 'tada' is the name of our component, followed by the name of the entity we want
to store in this table which in this case is simply 'items'. This name should always be plural; a table is always a collection of items.
So the database table naming convention is:

`dbprefix` + `component name` + `_`  +`entities`

Next we have the primary key. Every table should have one. We define it as SERIAL.

> SERIAL is an alias for BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE.

The name of the primary key is `tada_item_id` . Again we use the component's name 'tada', followed by the name of the entity,
in singular this time, since it describes one item. In other words the primary key name format is:

 `component name` + `_` + `entity_id`

## Insert the Data Please!

Now we insert some sample data into the blog table. Remember, replace #__ with your database prefix:

    INSERT INTO `#__tada_items` (`title`, `description`) VALUES
        ('Tada item one', 'This is the first Todo'),
        ('Tada item two', 'This is the second Todo'),
        ('Tada item three', 'This is the third Todo');


Now we have a database defined let's delete the controller we created in the previous part. For this delete the controllers
folder and enclosed file in:

    DELETE     /components/com_tada/controllers/tada.php

**Remember, we only needed to create that file because we didn't have an associated table yet. Now we do!**

## Register the Component

Finally the component must be registered in the database (however, this is done automatically when installing a new component
with the normal procedure through the Joomla! installer).

To do this, insert the following code in the database. Replace '#__' with your database prefix:

For Joomla 2.5.x and 3.x

    INSERT INTO `#__extensions` ( `id`, `name`, `link`, `menuid`, `parent`,
    `admin_menu_link`, `admin_menu_alt`, `option`, `ordering`, `admin_menu_img`,
    `iscore`, `params`, `enabled`)
    VALUES(NULL, 'Blog', 'option=com_tada', 0, 0,'option=com_tada', 'Tada',
    'com_tada', 0, '', 0, '', 1)

## Great!

You have created your database table for your blog posts and registered your component as a Joomla! extension.

## Up Next: Your Todo Item Views


