
# View Behaviors

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
