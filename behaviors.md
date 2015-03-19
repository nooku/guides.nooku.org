# Behaviors

The Framework encourages us to **Separate what Varies** by giving us tools to encapsulate different strategies. It provides the architecture for us so we don't have to do it ourselves. The structure that you will and should use most in customizing your applications is the Behavior.

## Background

These behaviors are essentially strategies for customization classes that let you control what happens before and after all of the major actions in the MVC and Database layer of the Framework.

The Framework supports composition at its core. Most classes extend from KObject and as such, inherit the mixin method. This method takes an object and **mixes** in that objects mixable methods into its own interface.

> For more background on the mixin checkout [The Mixin](http://guides.nooku.org/essentials/mixin.html) section of these guides.

What's important here is that the classes that get mixed are child classes of KMixinAbstract, and KBehaviorAbstract falls into this category. That means by adding a behavior to an object you also get the opportunity to mixin methods into that object's interface.


## Database Behaviors

Utilizing database behaviors lets us create Trigger like procedures in our applications, without having to store them with the database. They provide a means to contextually affect change both before and after `select`, `insert`, `update` and `delete` actions performed in the database layer.

Remember, these are strategies and the real power lies in the fact that once we've separated them out and the follow SRP (single responsibility principle) we can use them in many other table objects. And, because they are mixins they add methods and functionality to the row level objects.

One of the easiest examples to understand is the [`KDatabaseBehaviorCreatable`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/creatable.php) database behavior. Its purpose is to simply make sure that when we insert a record into a table (that is configured to have this behavior), it adds the date, and the user id of the logged in user. Additionally, the behavior provides a `getAuthor` method to the row object and which grabs the created_by of the object and returns a system level user object, so you don't have to.
### Benefits

+ We never have to rewrite the code that makes sure that we record those values.
+ There is no question: Where do we write that code? In the controller, model because we are defining these actions at the table level? Controller logic is cleaner.
+ It helps promote standardization of column names across tables for this information.


You can build your own behaviors in your own applications, and augment or adjust the behavior of current ones to better match your needs.

 Let's list the framework level behaviors and give some background about each.

+ [Hittable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/hittable.php) - Provides the row object with the interface to increment the  hit count, usually for counting views. Requires column name `hits` and most appropriately of  **int** type.
+ [Identifiable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/identifiable.php) - Handle storing a [Universal Unique Identifier]() string along with the object when the object is first stored. Requires a **unique** column called `uuid`.
+ [Lockable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/lockable.php) - Locking a row record precludes it from being edited by some other user while another user is editing it.  When a row is lockable, it gets all the methods it needs to control that lock. Applicable columns include `locked_by` and `locked_on`.
+ [Modifiable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/modifiable.php) - Handles the automatic recording of the last user to modifying a record.
+ [Orderable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/orderable.php) - Provides all that's required to maintain a database table's  `ordering` column when a row is inserted or updated.
+ [Parameterizable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/parameterizable.php) - Handles the storing and retrieval of serialized data from a specified column in a table. The default column is `parameters`, but others can be specified. The formats are PHP, INI, JSON, Yaml and XML.
+ [Sluggable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/sluggable.php) - Builds a unique token string based on column values specified in its configuration. The default column is `title` but others can be added. An example outcome: if a row object has a `title` property set to "Red Dog", the `slug` property will become "red-dog".


## Model Behaviors

The ability to separate out strategies for building queries and models states helps to simplify model code. It also provides a number of opportunities to affect the input and the output of a given model at runtime.

Like all Nooku behaviors, Model behaviors get fired before and after each major action that a model perform, `fetch`, `create`, `count` and `reset`.

Getting to know what the core framework behaviors do is part of the learning curve of using Nooku. The content of the Model state is coupled to the contents of a request coming down the chain from the dispatcher to the controller. The principal work of the model to take those values are relevant to it, building queries based on them and return that result. The model needs to explicitly identify values are relevant to it by `inserting` them into the state. By encapsulating all that work into different behaviors a model definition can become very short:

```php
class ComAcmeModelBars extends KModelDatabase {

    protected function _initialize(KObjectConfig $config)
    {
        $config->append(array(
            'behaviors' => array('paginatable', 'sortable', 'searchable','indexable'),
        ));
        parent::_initialize($config);
    }


}
```
That example has all of the following capabilities:

+ [Indexable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/indexable.php#L16) Gets the index information from the associated table's schema and populates the state with those columns, and automatically builds the where part of the query based on those states.
+ [Sortable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/sortable.php#L16) Adds `sort` and `direction` to the model's state and then builds the `ORDER BY` part of the query object based on those values if they exist.
+ [Paginatable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/paginatable.php#L16) This behavior takes care of adding `offset` and `limit`to the state variable and then adding those values to the `LIMIT` part of the database query.
+ [Searchable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/searchable.php#L16 ) Adds `search` to the state variable, and lets you specify columns that you want to seach on, but has the default column `title`. If your table has `title`as a column and you make.

## View Behaviors

## Controller Behaviors
