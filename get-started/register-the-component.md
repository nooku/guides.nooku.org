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