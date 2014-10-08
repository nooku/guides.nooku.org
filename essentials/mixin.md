# The Mixin

An extremely important piece of the Framework is the [Mixin](http://api.nooku.org/package-Koowa.Library.Object.Mixin.html).
Before PHP 5.4, the language had no concept of Traits. These are class-like structures that, when ‘used’ by an object, provide
some type of functionality (similar to Multiple Inheritance). But they still can't be introduced into  Nooku has been solving this problem for years (since PHP 5.2) with the Mixin.


The [Mixin](http://api.nooku.org/package-Koowa.Library.Object.Mixin.html) not only lets you compose objects together,
but it also adds each mixed object’s methods to the composite object’s interface.
The object using the mixin seems to ‘inherit’ the mixed in object’s methods.

Almost all objects in the Framework have this ability because they extend the base class [KObject](http://api.nooku.org/class-KObject.html)
that defines the mixin() method.

**Lets kook at Nooku's Controller Architecture to see how this works**

The major classes in Nooku’s controller architecture are also descended from [KObject](http://api.nooku.org/class-KObject.html).
The abstract controller is the KControllerAbstract class and by inspection you can see that it takes advantage of the Mixing
ability right away. Whenever an instance of this class is constructed, the [KMixinCommand](http://api.nooku.org/package-Koowa.Library.Command.Mixin.html)
and [KMixinBehavior](http://api.nooku.org/class-KBehaviorMixin.html) functionality is immediately added to its interface.
Consequently, each controller in Nooku is composed with Command Chain and Behavior management functionality through the respective objects.

The [KMixinBehavior](http://api.nooku.org/class-KBehaviorMixin.html) class holds all the pieces to give KControllerAbstract the ability to
load specific Behaviors at runtime. Behavioral strategies are classes that describe a process or logic that can be separated out and used
by other classes (e.g., editable, orderable). KMixinBehavior is fairly simple and has only four 'interface' methods: **getBehavior,
hasBehavior, addBehavior and getBehaviors**. And that's all we need to give an object the ability to handle and encapsulate
different behavioral strategies.

Similarly, [KMixinCommand](http://api.nooku.org/package-Koowa.Library.Command.Mixin.html) has only three methods:
**getCommandContext, getCommandChain, setCommandChain**. If you hadn’t guessed, these three methods provide KControllerAbstract
with the ability to implement a command chain (or [Chain of Responsibility design pattern](http://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)),
but also lets it do so at runtime.

You can think of this mixing as a simple arithmetic addition:

![Class Addition Images](http://../resources/images/arithmetic.png)

Gives us an interface that looks like this:

![Class Addition Images](http://../resources/images/arithmetic.png)

By definition Abstract classes are meant to be extended and so by the magic of inheritance all objects that are children
or instances of KControllerAbstract also gain the ability to add behaviors and a command chain at runtime.

<pre><h3>Great...But what does all that actually mean?</h3></pre>

In short, Nooku provides componentized functionality; that is, Nooku allows you
to modularize your functionality and compose functionalities across modules at runtime.

These two examples serve to demonstrate composition. They also serve to demonstrate the Nooku framework’s support for further
composition at its core. This is an important advantage. The methods added to KControllerAbstract above support the “Strategy Design Pattern”
by giving developers the tools to encapsulate what varies before one line of code has been written. The fact that the mixin()
method is part of every extension of [KObject](http://api.nooku.org/class-KObject.html) means that you can readily define and add other behavioral management interfaces
to most objects at runtime.
