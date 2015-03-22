# Behaviors

The Framework encourages us to **Separate what Varies** by giving us tools to encapsulate different strategies. It provides the architecture for us so we don't have to do it ourselves. The structure that you will and should use most in customizing your applications is the [Behavior](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/behavior/abstract.php#L19).

## Background

These behaviors are essentially strategies for customization classes that let you control what happens before and after all of the major actions in the Dispatcher, MVC, and Database layer of the Framework.

The Framework supports composition at its core. Most classes extend from KObject and as such, inherit the mixin method. This method takes an object and **mixes** in that objects mixable methods into its own interface.

> For more background on the mixin checkout [The Mixin](http://guides.nooku.org/essentials/mixin.html) section of these guides.

What's important here is that the classes that get mixed are child classes of KMixinAbstract, and KBehaviorAbstract falls into this category. That means by adding a behavior to an object you also get the opportunity to mixin methods into that object's interface.


## Database Behaviors

Utilizing database behaviors lets us create [Trigger](http://en.wikipedia.org/wiki/Database_trigger) like procedures in our applications, without having to store them with the database. They provide a means to contextually affect change both before and after `select`, `insert`, `update` and `delete` actions performed in the database layer.

Remember, these are strategies and the real power lies in the fact that once we've separated them out and the follow the single responsibility principle (SRP ) we can use them in many other table objects. And, because they are mixins they add methods and functionality to the row level objects.

One of the easiest examples to understand is the [`KDatabaseBehaviorCreatable`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/creatable.php) database behavior. Its purpose is to simply make sure that when we insert a record into a table, it adds the date, and the user id of the logged in user. Additionally, the behavior provides a `getAuthor` method to the row object and which grabs the created_by of the object and returns a system level user object, so you don't have to.

### Benefits

+ We never have to rewrite the code that makes sure that we record those values.
+ There is no question: Where do we write that code? In the controller, model because we are defining these actions at the table level? Controller logic is cleaner.
+ It helps promote standardization of column names across tables for this information. In the `creatable` behavior user id is always saved as `created_by` and the date is always stored in `created_on`.


You can build your own behaviors in your own applications, and augment or adjust the behavior of current ones to better match your needs by simply extending them.

Let's list the framework level behaviors and give some background about each.

+ [Creatable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/creatable.php) - Strategy for storing information about the creator of a record and the date.
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

Getting to know what the core framework behaviors do is part of the learning curve of using Nooku. The content of the Model `$_state` is coupled to the contents of a request coming down the chain from the dispatcher to the controller. The principal work of the model to take those values are relevant to it, building queries based on them and return that result. The model needs to explicitly identify values are relevant to it by `inserting` them into the state. By encapsulating all that work into different behaviors a model definition can become very short:

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
That example has all of the Model behaviors that exist as part of the Framework and we describe them in brief below.

+ [Indexable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/indexable.php#L16) Gets the index information from the associated table's schema and populates the state with those columns, and automatically builds the where part of the query based on those states.
+ [Sortable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/sortable.php#L16) Adds `sort` and `direction` to the model's state and then builds the `ORDER BY` part of the query object based on those values if they exist.
+ [Paginatable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/paginatable.php#L16) This behavior takes care of adding `offset` and `limit`to the state variable and then adding those values to the `LIMIT` part of the database query.
+ [Searchable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/searchable.php#L16 ) Adds `search` to the state variable, and lets you specify columns that you want to seach on, but has the default column `title`. If your table has `title`as a column and you make.

## View Behaviors

There is one major action in the View and that's the `render` method. That means we get an opportunity to affect change in the data before it gets rendered into a view, or change output after that rendering.

There are no View behaviors included in the Framework, but that doesn't mean they aren't useful. Think of useful it may be to

+ change the template of a view due to a model state
+ for a given situation pull in other data from other MVC triads
+ register filters with the Template engage before it renders
+ changing the template engine from [`koowa` to `mustache`, `markdown` or `twig`](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/template/engine) to complile some output, and then switching back to render the rest
+ effecting the schema used in CSV or JSON return.

### Real Example

Backbone JS expects GET requests to be responded to with either a singular JSON object when an ID is sent, or a JSON array of objects for a collection of entities. Nooku returns documents that follow the JSONAPI spec, which has much more information like url templates and model state information. We can use a view behavior to completely change the body of our response in these situations:

```php

class ComTodoViewBehaviorBackbone extends KViewBehaviorAbstract
{

    public function _beforeRender($context)
    {
        $view = $this->getMixer();
        // lets get the data from the model.
        $data = $view->getModel()->fetch();
        $result = array();

        // if we are getting a collection, make sure we build an array for backbone
        // Check if the model state is unique instead of $view->isCollection(), it checks the plurality of the view. We don't want that.

        if($view->getModel()->getState()->isUnique())
        {
            // extract the properties of the row object
            $result = $data->getProperties();
        }
        // if a collection, we just want a list so get only the values of the array representation of the RowSet
        // @see https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/rowset/abstract.php#L558
        else $result = array_values($data->toArray());

        // set the content of the view.
        $view->setContent($result);

    }

}
```
We are simply grabbing the information from the model of the view, formatting it and setting the view content. Once the `$_content` of the view is set, the [KViewJson::_renderData](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/view/json.php#L144) will never be called and that's where the object to be rendered is usually built.


## Controller Behaviors

The core Controller package does a great job with handling the standard Browse, Read, Edit, Add and Delete (BREAD) requests and the action of rendering of those requests. Each of those expose before and after command chains, and we use a number of behaviors out of the box to augment Controller functionality and interface.

+ [Findable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/findable.php) - Special Joomla-centric behavior that handles updating the search index in Joomla when an entity is updated itself
+ [Cacheable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/cacheable.php) - Another Joomla-centric behavior to handle the caching of requests based on state and the invalidation of that cache when an entity changes.
+ [Persistable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/persistable.php#L16) - Persist the state of a request to the session and load them back into a current state of a subsequent request where the view, model type, layout and action are the same. Helps with browsing.
+ [Editable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/editable.php#L16) - Places three additional action methods in a controller's interface: `save`, `add` and `apply` and handles setting and retrieving the redirect URL based on the `referrer` cookie information. Used in heavy conjuction with the [Toolbar](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/controller/toolbar) package.
+ [Permissible](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/permissible.php) - Provides the interface for handling whether of not a specific BREAD or `render` action can be performed.

It should be noted that the Dispatcher object is a special case controller and has access to the same behavior architecture.

