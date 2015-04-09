# Behaviors

The Framework encourages us to **Separate what Varies** by giving us tools to encapsulate different strategies and then compose those pieces together. It provides substantial architecture and convention to us, and the `Behavior` API is a very useful end result.

Behaviors are a key element to being able to design and produce components quickly. There are already a number of strategies which are ready for you to use. The hardest part about using them is just learning a little about what they do, and then getting comfortable with their usage. The sections that follow are meant to shed some light on the areas of use and their general make up.

## Background

Behaviors are essentially strategies for customization that let you control what happens before and after all of the major actions in the Dispatcher, MVC, and Database layers of the Framework.

In every structure a behavior is invoked in very much the way. It starts as a result of an invocation of a command chain in the action which is to be affected. Such and invokation will look something like:

 ```php
 $this->invokeCommand('before.[SOME ACTION]', $context);`
 ```

The Framework supports composition at its core. Most classes extend from KObject and as such, inherit the mixin method. This method takes an object and **mixes** in that objects mixable methods into its own interface.

> For more background on the mixin checkout [The Mixin](http://guides.nooku.org/essentials/mixin.html) section of these guides.

Each structure that makes use of the Behavior architecture needs to mixin the [`KBehaviorMixin`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/behavior/mixin/mixin.php) interface right way.

What's important here is that the classes that get mixed are child classes of `KMixinAbstract`, and `KBehaviorAbstract` falls into this category. That means by adding a behavior to an object you also get the opportunity to mixin methods into that object's interface.

## How does it work?

All objects in the Framework that provide this functionality  have the same architectural characteristic. Their main action methods get funneled through an 'executing' method and thus centralizes the execution strategy of that respective action.


For illustration, here is a stripped down version of the [`KViewAbstract::render()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/view/abstract.php#L113):

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

Each execution always exhibits the same general logic:

1. a relevant `$context` variable gets set up
2. a `before` command chain is fired
3. the actual `_action[Action]` method is called, the `result` of which is added to the `context`
4. a `after` command chain is invoked.

The `invokeCommand` method is part of the `KCommandMixin` interface, and each object that uses the Behavior API needs to have it mixed in at its creation, in addition to the `KBehaviorMixin`. Such a class will have something like the following in their constructor:

```php
public function __construct( KObjectConfig $config)
{
        parent::__construct($config);
        // Mixin the behavior (and command) interface
        $this->mixin('lib:behavior.mixin', $config);
        // Mixin the event interface
        
}
```

Have a look at the others

* [`KControllerAbstract::execute()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/controller/abstract.php#L125)
* [`KModelAbstract::fetch()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/model/abstract.php#L125)
