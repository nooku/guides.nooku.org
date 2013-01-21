#Nooku

##Introduction

Believe it or not, there is a lot of bad code out there on the big scary interweb. We’ve all seen it, we’ve all blasphemed at our computer screens as we’ve encountered countless instances of vulnerabilities; 10+ nested if statements, 10’000 line classes, stupid variable names, stupid class names, etc etc.

So how does Nooku fit into this? Merely by the simple fact that Nooku is based upon some fairly fundamental concepts that will end up making your life a whole lot easier.

So, first things first, forget most of what you know about writing code (I’m serious), and take a step back to think about why you are writing what you are writing, the bigger picture, the concepts, the design patterns, etc. Are you writing something to “just get it done”? Thinking it will save you time in the short term and you will “refactor that later”. Then come back 2 months later to find the comment:

	//I know this is hacky, but I’m doing it now because my boss is on my back, it’s 5.15 on a friday evening and I want to go to the pub!

Using nooku, should, through great code itself, force you to write better code and subsequent applications.

I digress, back to Nooku. Nooku is like no other development framework that you will have encountered, certainly not in the PHP context (feel free to correct me on this). Why? Several reasons:

###1 - Composition
Nooku focuses on composition over inheritance, a fairly new concept to PHP. This one point is more important than perhaps any other concept in Nooku. The reason for this is that most other frameworks out there are based on inheritance to extend functionality, causing bloated classes that often end up containing way more inherited methods than they actually need, and poor code reuse due to PHP’s lack of multiple inheritance (prior to 5.4) that leads to copy/paste programming. The end result is code that is hard to manage, poor resource usage, higher memory footprint, little to no code reuse and general miserability amongst developers.

###2 - Component architecture
Nooku employs a component based architecture, where components are self contained libraries or applications, some of which are dispatchable. Think of components like the packages of an SDK and you should get the picture. This ties in with the composition principle as mentioned above, but more on that later.

###3 - Service location
Nooku uses a service location architecture that is quite unique to Nooku, and allows separation of object instances from their class names, which may not seem like much right now, but is incredibly powerful as you will come to see.

###4 - Write less code
Nooku allows you to write less code, I mean a lot less code. What you do with that free time you gain is up to you :) It focuses you on what you should be doing, writing your application.

So by now you may be thinking “Crikey, this sounds awesome, I want in, how do I get started?”.
Well I will try and outline some of the basic principles used in Nooku that will get you started on your way to developing your first application.

So, principles first, Nooku is an HMVC framework based on existing design patterns and programming concepts, that (even though they have fancy sounding names), make a developer's life easier: Object Relational Mapping, Active Record, MVC, Inflection, Chain of Command, Factory, Object Store, Dependency Injection, Mixins, Decorators, Adapters, Observers

Still with me? O.K, move onto the next page.