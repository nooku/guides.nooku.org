# Sparse fieldset

### limit the columns you want to return
You can ask for partial results with no extra work. Just add the fields request variable to your request with a comma separated list of fields that correspond to the columns you want to see in your entities.
A request to `http://joomla.dev/component/tada?view=todo&format=json&id=1&fields=id,title,description` yields only the columns we ask for in the returned entity:
```javascript
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
```