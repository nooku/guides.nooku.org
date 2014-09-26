# Entities

Nooku gives you [BREAD](/concepts/BREAD.html) out of the box. Since we’re talking about the structure of our JSON documents we are interested in the Browse and Read operations, which are both GET requests.

Internally, the result of these actions are built into either Rowset or a singular Row object, respectively. In terms of the API and the document, a Row can be thought of as a Resource and a Rowset as a Collection of Resources.

The default JSON document response returns an array of entities, even if there is only one as a result of a Read operation.

#### A Resource and Resource Collection

When a client issues a GET request that has some identifying part that limits the result to one record, its considered a Read request in our BREAD paradigm.

In the default document structure, we’ve chosen to use the word “entities” as our key to store a Resource representation in the document, i.e.
```javascript
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
```
Note the array in the "entities" property.

>Did you know that you can use a Table Behaviors to add the created_on and/or created_by to all of
your records?  You just need the created_on or created_by column in the table and the `array(‘behaviors’ => ‘createable’) in your
table class’ \_initialize method.

Similarly, we send a Collection of Resources as JSON array of resource objects on “entities” property of the response document:
```javascript
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
```
You may also notice that each entity also has its own "links" property, with the “self” relationship.
