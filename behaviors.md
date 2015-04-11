# Behaviors

The Framework encourages us to follow DRY by giving us tools to encapsulate different strategies, and then compose those pieces together. It provides substantial architecture and convention to us to support this, and the Behavior API is a useful end result.

Behaviors are a key element to being able to design and produce components quickly because they eliminate the need to write that code again. They are leveraged in the Dispatcher, Model, View, Contrller and Database implementations of the Framework, and are available for you to use or extend.

The hardest part about using them is just learning a little about what they do, and then getting comfortable with their usage. The sections that follow are meant to shed some light on the areas of use and their general make up.

## Example

Here is a trivial example of a Controller behavior so we can refer to some of the concepts involved.

```php
class ComAcmeControllerBehaviorBarable extends KControllerBehaviorAbstract
{
    public function _initialize( KObjectConfig $config)
    {

        $config->append(array(
            'priority'   => self::PRIORITY_HIGH,
        ));

        parent::_initialize($config);
    }
    /**
     * Example callback to take place before a record is read from the datastore.
     */
    protected function _beforeRead($context)
    {
        if($context->data->foo)
        {
            $this->getModel()->id($context->data->foo);

        }
    }
    /**
     * Example callback for after the browse action is complete.
     */
    protected function _afterBrowse($context)
    {
       if($this->getRequest()->foo)
       {
           foreach($context->result as $row)
           {
                  $row->foo = $this->getRequest()->foo;
           }
       }
    }
    /**
     * Example mixable method.
     */
    public function sendBarNotification()
    {
        mail(
            'you@you.com',
            'New Bar',
            'You have a new Bar!'
        );

    }
}
```
And an example controller that registers that behavior.
```php
class ComAcmeControllerFoo extends KControllerModel
{
    public function _initialize( KObjectConfig $config)
    {
        $config->append(array(
            'behaviors' => array('barable')
        ));

        parent::_initialize($config);
    }
}
```

## Background

Behaviors are strategies for customization that let you affect two areas in an object that uses them. They let you:

1. control the input before and the output after the execution of the `_action` methods of a class.
2. add methods to an object's interface

The result of these two abilities is that you have complete control over the end result of the business logic of an object; and can mixin needed functionality into an object at runtime, respectively.

## Controlling the Input and Output

A behavior is an implementation of the `KCommandCallback` class and is part of the Framework's Command Chain API. When a command is invoked, its command callback queue is processed in the order of the priority. The behaviors that are registered with an object sit in this command chain, and the methods that match up with the invoked command are run as a result of invocation.

### Invocation

To show how the processing of a command queue gets started, here is a stripped down version of the [`KViewAbstract::render()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/view/abstract.php#L113):

```php
public function render()
{
    $context = $this->getContext();
    $context->action  = 'render';
     if ($this->invokeCommand('before.render', $context) !== false)
     {
        $context->result = $this->_actionRender($context);
        $this->invokeCommand('after.render', $context);
     }
     return $context->result;
}
```
Its always the same general logic:

1. a relevant `$context` variable gets set up
2. a `before.[action]` command chain is fired with `invokeCommand`
3. the actual `_action[Action]` method is called, the `result` of which is added to the `context`
4. a `after.[action]` command chain is invoked.

> Some other examples of the same process can be seen in  [`KControllerAbstract::execute()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/abstract.php#L125) and  [`KModelAbstract::fetch()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/abstract.php#L125).

If the behavior has methods that match the invoked commands, namely `_before[Action]` and `_after[Action]`, they are executed. You can see this method pattern in our [`ComAcmeControllerBehaviorBar`](#example) example behavior above.

Its in these methods that we get a chance to change the result of the execution of actions in an object.

## Mixins

> [Read more complete Topical Guide on the Mixin](../essentials/mixin.md)

### KBehaviorAbstract

At the heart of the Behavior API is the Mixin. All behaviors are extensions of the `KObjectMixinAbstract`, and its this fact that lets us add a behavior's `public` methods to an object interface.

Our example exposes one `public` method called `sendBarNotifiation()`. If we create a controller with ComAcmeControllerBehaviorBarable registered as a behavior, e.g.
```php
$controller = KObjectManager::getInstance()
            ->getObject('com://site/acme.controller.foo');
```
then we can call `$controller->sendBarNoticiation();`, even though its not defined in the `ComAcmeControllerFoo` class.

### KBehaviorMixin: Behavior Management

For an object to make use of behaviors it needs the interface to manage them. There needs to be getters and setters and maybe some way to check if a behavior exists in relation to the object. To get that interface into an object it can conveniently **mixin** the `KBehaviorMixin` class at construction. For example, you will see something similar to the following in every abstract class where that object type will use behaviors:

```php
public function __construct( KObjectConfig $config)
{
        parent::__construct($config);
        // Mixin the behavior (and command) interface
        $this->mixin('lib:behavior.mixin', $config);
}
```

With that instruction to `mixin('lib:behavior.mixin', $config);` all the methods that our object needs to work with behaviors get added to that object's interface. There is a little more though: `KBehaviorMixin` extends `KCommandMixin`, and that class also adds the methods needed to `invokeCommand` as we highlight above.

