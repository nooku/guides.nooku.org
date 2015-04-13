
## Controller Behaviors

The Framework controller package does a great job with handling the standard `browse`, `read`, `edit`, `add` and `delete` (BREAD) requests and the action of rendering of results of those requests where there is one. Each of those actions are exposed to before and after command chains, and we use a number of behaviors out of the box to augment Controller functionality and interface.

As with all behaviors we've discussed, they allow us to separate out specialized controller logic and compose those pieces of logic into a queue to be run in succession. Again, they can also add methods to our controller interface allowing us to expose new, more specific controller actions.

## Example

A good example of adding new actions to a controller can be seen by looking at the [`Editable`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/editable.php#L16) behavior. It defines three new actions that help dictate the user's flow through the act of editing a record in your component.

When in use, an authorized user can **apply** (`_actionApply($context)`) changes to the record. This initiates a write request, but keeps them on the editing page. If they **save** (`_actionSave($context)`) a record, the same write request gets initiated, but they are redirected to the location they were just before they requested the editing page. Lastly, if they **cancel** (`_actionCancel($context)`) they are just redirected to their previous location in the application.

Imagine trying to fit this class into a set of controllers by inheritance, but include other pieces of functionality from other classes into that hierarchy.

Adding a behavior to a controller is easy, simply add the `name` to the `behaviors` property of the `$config` variable in the controller's `_initialize` method:

```php
class ComAcmeControllerBar extends KControllerModel
{

    protected function _initialize(KObjectConfig $config)
    {
        $config->append(array(
            'behaviors' => array('editable')
        ));

        parent::_initialize($config);
    }

}
```
If you wanted to write a new behavior for your component, simply place it in a new directory under the `controller` folder of your component. For example, a new behavior called `ComAcmeControllerBehaviorFooable` would get placed in:

`/components/com_acme/controller/behavior/fooable.php`

Also, make sure that it extends, directly or indirectly, from [`KControllerBehaviorAbstract`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/abstract.php#L16).

## The List

There is a lot of work done for you out of the box, and you should take the time to play with each of these structures. Doing so will let you write (or not have to write) the controller layer of your application much more quickly.

+ [Findable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/findable.php) - Special Joomla-centric behavior that handles updating the search index in Joomla when an entity is updated itself
+ [Cacheable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/controller/behavior/cacheable.php) - Another Joomla-centric behavior to handle the caching of requests based on state and the invalidation of that cache when an entity changes.
+ [Persistable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/persistable.php#L16) - Persist the state of a request to the session and load them back into a current state of a subsequent request where the view, model type, layout and action are the same. Helps with browsing.
+ [Editable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/editable.php#L16) - Places three additional action methods in a controller's interface: `save`, `add` and `apply` and handles setting and retrieving the redirect URL based on the `referrer` cookie information. Used in heavy conjuction with the [Toolbar](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/controller/toolbar) package.
+ [Permissible](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/behavior/permissible.php) - Provides the interface for handling whether of not a specific BREAD or `render` action can be performed.

It should be noted that the Dispatcher object is a special case controller and has access to the same behavior architecture.

