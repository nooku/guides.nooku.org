#Components

Whilst components aren’t the most important concept, they are pivotal to understanding how Nooku works. Think of Nooku as providing the architecture to build an SDK for the web, a component being akin to a package. Components play a very important role in how Nooku is built now, and how it will develop in the future. Components currently fall into two categories

* Dispatchable components (called extensions)
* Non-dispatchable components

Dispatchable components can be thought of as applications, that can be accessed through an HTTP request, and will generally take the form of a blog, shopping cart, user manager, etc.

Non-dispatchable components are components that are used as building blocks while making other extensions. Examples of this might be a category manager, tagging system, version control, comments, etc.

Components are typically built up of controllers, models and views, but can contain a wide array of parts.

Using the HMVC (more later on the **H**) concepts of Nooku, components can easily be built up from other components, to allow for a great amount of code reuse, and speed up your development lifecycle.

