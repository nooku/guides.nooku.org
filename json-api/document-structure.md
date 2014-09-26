## Document Structure

In accordance with the specification Nooku defines a Top Level JSON object for each document. Have a look at an example of the default JSON:

```javascript
    {
            "version": "1.0",
            "links": {
                "self": {
                    "href": "http://api.awesomeapi.you/chairs/?id=50&var1=y",
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
```

The top level properties of any given document give you and your api consumers an idea of how they can query your resources.

We include 5 properties in this level and in accordance with the specification, no others are included: **version, links, meta, entities** and **linked**. We describe the major characteristics of each in the following sections.
