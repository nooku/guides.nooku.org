# Behaviors

The Framework encourages us to **Separate what Varies** by giving us tools to encapsulate different strategies. It provides the architecture for us so we don't have to do it ourselves. The structure that you will and should use most in customizing your applications is the [Behavior](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/behavior/abstract.php#L19).

## Background

These behaviors are essentially strategies for customization classes that let you control what happens before and after all of the major actions in the Dispatcher, MVC, and Database layer of the Framework.

The Framework supports composition at its core. Most classes extend from KObject and as such, inherit the mixin method. This method takes an object and **mixes** in that objects mixable methods into its own interface.

> For more background on the mixin checkout [The Mixin](http://guides.nooku.org/essentials/mixin.html) section of these guides.

What's important here is that the classes that get mixed are child classes of KMixinAbstract, and KBehaviorAbstract falls into this category. That means by adding a behavior to an object you also get the opportunity to mixin methods into that object's interface.

