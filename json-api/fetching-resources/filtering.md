# Filtering

We use the model’s state in building our queries. By default these states are directly connected to the request variables received by the application. As such, any state variables that you write into your Model and account for in your query strategy, will be reflected in your API.

If your database table has UNIQUE columns, they automatically get added to your model’s state!
Checkout KModelBehaviorIndexable. This behavior gets added to every class that is an instance of KModelDatabase.
This is why we can use id=1 without having to write a model at all.

To illustrate filtering, let’s assume that we’ve created ComTadaModelTodos and added category_id as a state in our class, and account for it in our query building strategy (_buildQueryWhere):

```php
    class ComTadaModelTodos extends KModelDatabase
    {
        public function __construct(KObjectConfig $config)
        {
            parent::__construct($config);
            $this->getState()->insert(‘category_id’, ‘init’);
        }
        protected function _buildQueryWhere(KDatabaseQueryInterface $query)
        {
            if($this->getState()->category_id){
                $query->where('tbl.category_id = ' . $this->getState()->category_id);
            }
            parent::_buildQueryWhere($query)
        }
    }
```
Now, a request to the API that has a category_id set will yield only those results:
```javascript
    {
        "id": "2",
        "uuid": "6631d2b4-8b78-4e70-ab0e-d1db2d1e4dd1",
        "title": "Water the Lawn",
        "slug": "water-the-lawn",
        "description": "My grass is so dry, we need more rain. ",
        "enabled": "1",
        "category_id": "15",
        "params": null,
        "links":
        {
            "self":
            {
                "href": "http://joomla.dev/component/tada/todo?slug=water-the-lawn&format=json",
                "type": "application/json; version=1.0"
            }
        }
    }
```