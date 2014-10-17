# Creating the Database Table

Let's define the basic database schema for our component. Execute the following SQL statement in your MySQL client,
such as [PHPMyAdmin](http://www.phpmyadmin.net/home_page/index.php).

Before doing so, replace the prefix '#__' with the prefix you chose during Joomla installation.

    CREATE TABLE IF NOT EXISTS `#__blog_posts` (
      `blog_post_id` SERIAL,
      `title` varchar(255) NOT NULL,
      `text` mediumtext NOT NULL
    );

## Naming Conventions Explained

Have a good look at the [Essentials > Naming Conventions](/essentials/naming-conventions.md) page. There is an easy for follow section for the database side of things.

The name of the table is `#__blog_posts`. 'blog' is the name of our component, followed by the name of the entity we want
to store in this table. This name should always be plural; a table is always a collection of items. So the database table
naming convention is:

`dbprefix` + `component name` + `_`  +`entities`

Next we have the primary key. Every table should have one. We define it as SERIAL.

> SERIAL is an alias for BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE.

The name of the primary key is `blog_post_id` . Again we use the component's name 'blog', followed by the name of the entity,
in singular this time, since it describes one item. In other words the primary key name format is:

 `component name` + `_` + `entity_id`

## Insert the Data Please!

Now we insert some sample data into the blog table. Remember, replace #__ with your database prefix:

    INSERT INTO `#__blog_posts` (`title`, `text`) VALUES
        ('Blog post one', 'This is the first post'),
        ('Blog post two', 'This is the second post'),
        ('Blog post three', 'This is the third post');


Now we have a database defined let's delete the controller we created in the previous part. For this delete the controllers
folder and enclosed file in:

    DELETE     /components/com_blog/controllers/blog.php

**Remember, we only needed to create that file because we didn't have an associated table yet. Now we do!**

## Register the Component

Finally the component must be registered in the database (however, this is done automatically when installing a new component
with the normal procedure through the Joomla installer).

To do this, insert the following code in the database. Replace '#__' with your database prefix ('jos_' by default):

For Joomla 2.5.x and 3.x

    INSERT INTO `#__extensions` ( `id`, `name`, `link`, `menuid`, `parent`,
    `admin_menu_link`, `admin_menu_alt`, `option`, `ordering`, `admin_menu_img`,
    `iscore`, `params`, `enabled`)
    VALUES(NULL, 'Blog', 'option=com_blog', 0, 0,'option=com_blog', 'Blog',
    'com_blog', 0, '', 0, '', 1)

## Great!

You have created your database table for your blog posts and registered your component as a Joomla! extension.



