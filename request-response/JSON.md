# RESTful JSON API

If you build a view in a Nooku powered component it will have a [JSON API]((http://jsonapi.org) version almost automatically.

JSON API is designed to minimize both the number of requests and the amount of data transmitted between clients and servers. This efficiency is achieved without compromising readability, flexibility, and discoverability.

As the specification matures we'll make more changes to achieve 100% compatibility.

## Document Structure

In accordance with the specification Nooku defines a Top Level JSON object for each document. Have a look at an example of the default JSON:

    {
        "version": "1.0",
        "links": {
            "self": {
                "href": "http://api.awesomeapi.you/chairs/?id=50&var1=y”,
                "type": "application/json; version=1.0"
            }
        },
        "meta": {},
        "entities" : [{
            "id": "50",
            "title": "Arm Chair",
            "description":"This was my grandmother’s armchair",
            "category_id":"5"
        }],
        "linked":{}
    }


The top level properties of any given document give you and your api consumers an idea of how they can query your resources.

We include 5 properties in this level and in accordance with the specification, no others are included: **version, links, meta, entities** and **linked**. We describe the major characteristics of each in the following sections.

### Version

This lets you tell your users what version of your api you are serving up.

Its pretty straight forward to change it from our default of “1.0”. Just define a JSON class in your component and set the `version` in your $config variable.

### Links

The links property in general is used to specify resource relationships for resource objects. In our default implementation we include the “self” relationship which shows the link that this document is in response to and the mime type of the request:


    "self": {
        href: "http://joomla.dev/component/tada?view=todos&format=json&limit=2",
        type: "application/json; version=1.0"
    }



When appropriate, we also include “next” and “previous” pagination links in the section in the exact same form.

    "next":
    {
        href: "http://joomla.dev/component/tada?view=todos&format=json&limit=2&offset=2",
        type: "application/json; version=1.0"
    }

**Remember, this happens automatically! You do not have to do anything.**

You can define how your named entity will render with no problem at all. Just create a JSON view class and define a method with the form ‘_get’ + The name of your entity

States are directly related to request variables and those variables are used to limit results to a set or an individual record.


### Entities

Nooku gives you [BREAD](./BREAD.html) out of the box. Since we’re talking about the structure of our JSON documents we are interested in the Browse and Read operations, which are both GET requests.

Internally, the result of these actions are built into either Rowset or a singular Row object, respectively. In terms of the API and the document, a Row can be thought of as a Resource and a Rowset as a Collection of Resources.

The default JSON document response returns an array of entities, even if there is only one as a result of a Read operation.

#### A Resource and Resource Collection

When a client issues a GET request that has some identifying part that limits the result to one record, its considered a Read request in our BREAD paradigm.

In the default document structure, we’ve chosen to use the word “entities” as our key to store a Resource representation in the document, i.e.

    "entities":
    [
        {
            "id": "1",
            "uuid": "c87d5566-b29a-4358-b328-463ee036aa2f",
            "title": "Walk the dog",
            "slug": "walk-the-dog",
            "description": "The dog really needs to go for a walk. ",
            "enabled": "1",
            "params": null,
            "links":
            {
                "self":
                {
                    "href": "http://joomla.dev/component/tada/todo?slug=walk-the-dog&format=json",
                    "type": "application/json; version=1.0"
                }
            }
        }
    ]

Note the array in the "entities" property.

>Did you know that you can use a Table Behaviors to add the created_on and/or created_by to all of
your records?  You just need the created_on or created_by column in the table and the `array(‘behaviors’ => ‘createable’) in your
table class’ \_initialize method.

Similarly, we send a Collection of Resources as JSON array of resource objects on “entities” property of the response document:

    "entities":
    [
        {
            "id": "1",
            "uuid": "c87d5566-b29a-4358-b328-463ee036aa2f",
            "title": "Walk the dog",
            "slug": "walk-the-dog",
            "description": "The dog really needs to go for a walk. ",
            "enabled": "1",
            "category_id": "9",
            "params": null,
            "links":
            {
                "self":
                {
                    "href": "http://joomla.dev/component/tada/todo?slug=walk-the-dog&format=json",
                    "type": "application/json; version=1.0"
                }
            }
        },
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
    ]

You may also notice that each entity also has its own "links" property, with the “self” relationship.

### Linked

http://jsonapi.org/format/#document-structure-compound-documents
http://jsonapi.org/format/#fetching-includes

In keeping with the specification a “linked” property is included in its default JSON response document. It is meant for you to be able to produce compound documents for your client applications.

If our Todo records were to include a category_id, we may wish to load a list of category resource objects into "linked" property of our response:

    "linked": {
        "categories": [{
          "id": "9",
          "name": "dog related"
        }, {
        "id": "15",
          "name": "yard work"

    }]

## Fetching Resources

### Sparse fieldset: limit the columns you want to return

You can ask for partial results with no extra work. Just add the fields request variable to your request with a comma separated list of fields that correspond to the columns you want to see in your entities.
A request to `http://joomla.dev/component/tada?view=todo&format=json&id=1&fields=id,title,description` yields only the columns we ask for in the returned entity:

    {
        "id": "1",
        "title": "Walk the dog",
        "description: "The dog really needs to go for a walk. ",
        "links":
        {
            "self":
            {
                "href": "http://joomla.dev/component/tada/todo/1?slug=walk-the-dog&format=json",
                "type": "application/json; version=1.0"
            }
        }
    }

### Filtering

We use the model’s state in building our queries. By default these states are directly connected to the request variables received by the application. As such, any state variables that you write into your Model and account for in your query strategy, will be reflected in your API.

If your database table has UNIQUE columns, they automatically get added to your model’s state!
Checkout KModelBehaviorIndexable. This behavior gets added to every class that is an instance of KModelDatabase.
This is why we can use id=1 without having to write a model at all.

To illustrate filtering, let’s assume that we’ve created ComTadaModelTodos and added category_id as a state in our class, and account for it in our query building strategy (_buildQueryWhere)::

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

Now, a request to the API that has a category_id set will yield only those results:

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

### Sorting

Sorting results is a snap. Like the “Indexable” behavior above, each model derived from KModelDatabase gets a sortable behavior added to it. That means with no effort, two state variables get added to your models:

The `sort` variable gives us the column by which to sort our results.
The `direction` is either the familiar `asc` for ascending (which is the default) and `desc` for descending. In combination, they get used in the Model to build the `order by` part of your query. A request in our example with `&sort=category_id&direction=desc` will produce a query against database that has the following:

    ORDER BY tbl.category_id DESC


### Pagination on Resource Collections

In keeping with good API design, additional pagination information is provided. The limit, offset, & total are sent along with the response document as part of the meta property in the top level object.

    "offset": 0,
    "limit": 2,
    "total": 7,

The **limit** variable is included as a state variable by default in the model objects. It tells the query building part of the model what the LIMIT on the query should be.
The **offset** is an integer multiple of the limit and represents the first record in that corresponding page. In our example, if the limit is 2 and we want to see the third page of results our offset would be **page - 1** multiplied by the **limit** which equals 4, then the offset should be 4.

The **total** is a count of all the records available in the data that fit the current request vars without the limit applied to them.

