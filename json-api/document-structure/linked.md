# Linked

http://jsonapi.org/format/#document-structure-compound-documents
http://jsonapi.org/format/#fetching-includes

In keeping with the specification a “linked” property is included in its default JSON response document. It is meant for you to be able to produce compound documents for your client applications.

If our Todo records were to include a category_id, we may wish to load a list of category resource objects into "linked" property of our response:
```javascript
    "linked": {
        "categories": [{
          "id": "9",
          "name": "dog related"
        }, {
        "id": "15",
          "name": "yard work"

    }]
```