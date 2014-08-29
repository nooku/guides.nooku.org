#Introduction

##What is Nooku?

In short, Nooku is more of a web development kit than just a framework.

Nooku is component based, a component is a re-usable building block that offers a PHP API, a REST API and perhaps a user interface. Components can be connected together using an HMVC approach to create a complete web application.

Nooku is built to allow using other PHP libraries, like Zend or Symfony when needed. You can look at Nooku as the higher level glue to hold everything together. Out of the box Nooku will do 80% of the work for you, if you need specialised libraries you can of course always do so.

##Why Nooku?

Believe it or not, there is a lot of bad code out there on the internet. We’ve all seen it, we’ve all blasphemed at our computer screens as we’ve encountered countless instances of vulnerabilities, 10+ nested if statements, 10’000 line classes, stupid variable names, stupid class names, etc etc.

So how does Nooku fit into this? Merely by the simple fact that Nooku is based upon industry standard design patterns and concepts, it that will end up making your life a whole lot easier.

So, first things first, put aside any pre-assumptions you may have about how to write code, and take a step back to think about why you are writing what you are writing, the bigger picture, the concepts, the design patterns, etc. Are you writing something to “just get it done”? Thinking it will save you time in the short term and you will “refactor that later”. Then come back 2 months later to find the comment:

	//I know this is hacky, but I’m doing it now because my boss is on my back, it’s 5.15 on a friday evening and I want to go to the pub!`

Using Nooku should, through well structured, well written and design pattern driven code itself, encourage you to write better code and subsequent applications.

##1 - Composition over inheritance

Nooku focuses on composition over inheritance, a fairly new concept to PHP. This one point is more important than perhaps any other concept in Nooku. The reason for this is that most other frameworks out there are based on inheritance to extend functionality, causing bloated classes that often end up containing way more inherited methods than they actually need, and poor code reuse due to PHP’s lack of multiple inheritance (prior to 5.4) that leads to copy/paste programming. The end result is code that is hard to manage, poor resource usage, higher memory footprint, little to no code reuse and general miserability amongst developers.

##2 - Component architecture

Nooku employs a component based architecture, where components are self contained libraries or applications, some of which are dispatchable (you can execute them from an HTTP request for example). Think of components like the packages of an SDK and you should get the picture. This ties in with the composition principle as mentioned above, but more on that later. A component architecture also allows for a great amount of code re-use amongst and across projects. Nooku comes with 15+ ready to use components out of the box, from basic article and category management, to versioning, tagging, and activity logs.

##3 - Service location

Nooku uses a service location architecture, which is a type of inversion of control (IOC) and allows separation of object instances from their class names. Service location allows the creation of objects to be handled by a service locator, and this service locator is used by all nooku objects to instantiate other objects. This may not seem like much right now, but is incredibly powerful as you will come to see

##4 - Write less code

Nooku allows you to write less code, I mean a lot less code. What you do with that free time you gain is up to you :) With built in REST level 3 support out of the box, reusable components, reusable UI widgets, JSON/CSV/RSS output for ANY view and so on, Nooku focuses you on what you should be doing, writing your application, not the same things over and over again.

##So what next?

So by now you may be thinking “Crikey, this sounds awesome, I want in, how do I get started?”.
Well these documents will try and outline some of the basic principles used in Nooku that will get you started on your way to developing your first application.

So, principles first, Nooku is an HMVC framework based on existing design patterns and programming concepts, that (even though they have fancy sounding names), make a developer's life easier: Object Relational Mapping, MVC, Inflection, Chain of Command, Factory, Object Store, Dependency Injection, Mixins, Decorators, Adapters, Observers and so on.

Still with me? O.K, move onto the next page.