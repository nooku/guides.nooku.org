# Model Behaviors

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
