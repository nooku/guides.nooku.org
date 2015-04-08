
## Controller Behaviors

The Framework controller package does a great job with handling the standard `browse`, `read`, `edit`, `add` and `delete` (BREAD) requests and the action of rendering of results of those requests where there is one. Each of those actions are exposed to before and after command chains, and we use a number of behaviors out of the box to augment Controller functionality and interface.

We are able to separate out specialized controller logic and compose them as needed.

```php
class ComAcmeControllerBar extends KControllerModel
{

    protected function _initialize(KObjectConfig $config)
    {
        $config->append(array(
            'behaviors' => array('editable', 'persistable')
        ));

        parent::_initialize($config);
    }

}
```

+ [Findable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/findable.php) - Special Joomla-centric behavior that handles updating the search index in Joomla when an entity is updated itself
+ [Cacheable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/cacheable.php) - Another Joomla-centric behavior to handle the caching of requests based on state and the invalidation of that cache when an entity changes.
+ [Persistable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/persistable.php#L16) - Persist the state of a request to the session and load them back into a current state of a subsequent request where the view, model type, layout and action are the same. Helps with browsing.
+ [Editable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/editable.php#L16) - Places three additional action methods in a controller's interface: `save`, `add` and `apply` and handles setting and retrieving the redirect URL based on the `referrer` cookie information. Used in heavy conjuction with the [Toolbar](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/controller/toolbar) package.
+ [Permissible](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/permissible.php) - Provides the interface for handling whether of not a specific BREAD or `render` action can be performed.

It should be noted that the Dispatcher object is a special case controller and has access to the same behavior architecture.

