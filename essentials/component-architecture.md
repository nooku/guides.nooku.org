#Component Architecture

At the very heart of Nooku is the design principle:

<pre><h3>Favor Composition over Inheritance</h3></pre>

To summarize that principle:

> "you should aim to compose (or add up) the functionality of multiple objects to create some
> sort of composite object, rather than relying on subclassing."

This statement embodies Nooku's Component Architecture and development philosophy. Components aren’t the most important concept in the framework,
but they are likely where you will spend most of your time developing. Understanding how they work, can help you leverage the work you do now against other projects.

Components currently fall into two categories:

* Dispatchable components (called extensions)
* Non-dispatchable components

## Dispatchable Components

Dispatchable components can be thought of as applications, that can be accessed through an HTTP request, and can take the form of a blog, shopping cart, user manager, or any other end user application you can dream up, etc.

## Non-Dispatchable Components

Non-dispatchable components are components that are used as building blocks while making other extensions. Examples of this might be a category manager, tagging system, version control, comments, etc.

## HMVC

Components are typically built in the MVC pattern of _Models, Views_ and _Controllers_ and of course, as we'll see later, can contain of other structures.
By using HMVC (more later on the **H**) in Nooku, your components can easily be built up from other components.  This in turn,
allows you to maximize code reuse, and speed up your development lifecycle.

**This is important**

<pre>Nooku's component architecture allows you to use other Components inside Components.
<b>Write once use everywhere!</b></pre>

We talk a good deal about HMVC in later chapters.

## BREAD

We all need BREAD, but not the food.
The BREAD we seek is an important action pattern that Nooku powered components follow for interacting with and managing our data.

