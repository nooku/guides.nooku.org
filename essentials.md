# Nooku Essentials

##1 - Composition Over Inheritance

Nooku focuses on _favoring composition over inheritance_, a fairly new concept to PHP. This one point is more important than perhaps
any other concept in Nooku. The reason for this is that most other frameworks out there are based on inheritance to extend
functionality, causing bloated classes that often end up containing way more inherited methods than they actually need,
and poor code reuse due to PHP’s lack of multiple inheritance (prior to 5.4) that leads to copy/paste programming. The end
result is code that is hard to manage, poor resource usage, higher memory footprint, little to no code reuse and general
'miserability' amongst developers.

##2 - Component Architecture

Nooku employs a component based architecture, where components are self contained libraries or applications, some of which
are dispatchable (you can execute them from an HTTP request for example). Think of components like the packages of an SDK and
you should get the picture. This ties in with the composition principle as mentioned above, but more on that later. A component
architecture allows for a greater amount of code re-use amongst and across projects. [Nooku Platform](https://github.com/nooku/nooku-platform)
comes with 15+ ready to use components out of the box, from basic article and category management, to versioning, tagging, and activity logs.

##3 - Object Management

Nooku uses a centralized Object Management architecture. That structure provides a type of inversion of control (IOC) and allows separation
of object instances from their class names. The Object Management package allows the creation of objects to be handled consistently,
in one place, and provides a mechanism for the Framework's use of fallback classes. This part of the architecture is centrally important to
making the "Writing Less Code" possible (See #5 below).

##4 Convention Over Configuration

As you will see in the coming chapters, the Framework relies heavily on a set of strict conventions to provide predictability for both you (the developer)
and the other objects in the system. Get to know the rules _(the [Naming Conventions](essentials/naming-conventions.md) section gives an excellent overview)_...
they are the "Key" to unlocking the power of Nooku.

##5 - Write Less Code

Nooku allows you to write less code, I mean a lot less code. What you do with that free time you gain is up to you :) .

With built in REST level 3 support out of the box, reusable components, reusable UI widgets, JSON/CSV/RSS output for ANY view and so on,
Nooku focuses you on what you should be doing, writing your application, not the same things over and over again.

##So what next?

So by now you may be thinking “Crikey, this sounds awesome, I want in, how do I get started?”.
Well these documents will try and outline some of the basic principles used in Nooku that will get you started on your
way to developing your first application.

So, principles first:

> Nooku is an HMVC framework based on existing design patterns and programming concepts, that (even
>though they have fancy sounding names), make a developer's life easier.
>
>**Object Relational Mapping, MVC, Inflection, Chain of Command, Factory, Object Store, Dependency Injection, Mixins, Decorators, Adapters, Observers and so on.**

Still with me? O.K, move onto the next page.