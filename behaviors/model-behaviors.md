# Model Behaviors

The ability to separate out strategies for building queries and models states helps to simplify model code. It also provides a number of opportunities to affect the input and the output of a given model at runtime.

Like all Nooku behaviors, Model behaviors get fired before and after each major action that a model performs: `fetch`, `create`, `count` and `reset`.

Getting to know what the core framework behaviors do is part of the learning curve of using the Framework. The content of the Model `$_state` is coupled to the contents of a request coming down the chain from the dispatcher to the controller. The principal work of the model to take those values which are relevant to it, building queries based on them and then return that result. The model needs to explicitly identify values are relevant to it by `inserting` them into its state. By encapsulating all that work into different behaviors a model definition can become very short:


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
That example has all of the model behaviors that exist as part of the Framework and we describe them in brief below.

Each behavior extends from the `KModelBehaviorAbstract` class, which is a descendant of `KMixinAbstract`. That means `public` methods in the behavior class can be added to a model object's interface. For example, the above mentioned `ComAcmeModelBars` model gets a `getPaginator` method mixed into its interface. Calling that method on the model, i.e. $model->getPaginator() creates a `KModelPaginator` object with the appropriate state values and returns it. This is all because we have said the model has the `paginatable` behavior.

## Example

Let's look at at the `Indexable` behavior ([`KModelBehaviorIndexable`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/indexable.php)) in some detail. In many cases its desireable to assign a unique identifier to a row in a table. To ensure that row is unique, those tables apply a `unique index` to a column of that table, and so each row must have a different value for that column.

The `Indexable` behavior takes care of finding out what unique indexes you have defined in your schema and adds them to the model's state variable, stipulating that they are unique. Then, before you call either the `fetch` or `count` method, it takes care of injecting these unique state values into the `WHERE` clause of the query.

This means that for every model that is `Indexable` in our application we don't have to worry about making sure that we've defined unique states in the model or even building queries based on those states, because it is automatic.

## The Complete List of Model Behaviors

+ [Indexable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/indexable.php#L16) Gets the index information from the associated table's schema and populates the state with those columns, and automatically builds the where part of the query based on those states.
+ [Sortable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/sortable.php#L16) Adds `sort` and `direction` to the model's state and then builds the `ORDER BY` part of the query object based on those values if they exist.
+ [Paginatable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/paginatable.php#L16) This behavior takes care of adding `offset` and `limit`to the state variable and then adding those values to the `LIMIT` part of the database query.
+ [Searchable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/behavior/searchable.php#L16 ) Adds `search` to the state variable, and lets you specify columns that you want to seach on, but has the default column `title`. If your table has `title`as a column and you make.
