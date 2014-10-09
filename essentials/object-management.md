# Object Management

By using centralized Object Management, Nooku decouples an object’s class name from its identifier. This is a central piece
in helping you **"Write Less Code"**. This decoupling allows for the use of _Fallback Classes_, which you can think of default
implementations of functionality that you are trying to achieve.

But hold on, an object’s class name is its identifier, it is what PHP uses to identify (and in cases locate) that object.
Well that doesn’t have to be the case. By separating the identification of an object from its class name, one can use a
unique identifier to instantiate the object, and not know or care about what the class name is.

If you think about it, how important is the class name? It is not, it is only used to infer its application within the
system and perhaps for location and auto loading purposes. What you care about is the resulting object that you are actually
going to use; not its class name.

So if you’re not using the class name for instantiating an object, what can you use? Well, Tim Berners Lee came up with a
concept in the 80’s, and no we don’t mean the internet, the URI. So we take advantage of a well established principle that
has worked well for over a quarter century.

![URI](../resources/images/uri.jpg)

Above is an example of a URI. Each part of the URI has a meaning, and we can use this meaning to our advantage.
We can use it to dynamically create a class name, because at the end of the day, we still need a class name at some point
to instantiate our object.

### Let's see how this works in practice.

In Nooku, we use scheme://authority/path - we don’t need query or fragment. A typical identifier may look like this:

	com://site/foo.controller.bar

Each part of this identifier has a meaning:

* **scheme**: com - The scheme defines the type of resource you are requesting, there are some predefined scheme types that
exist in Nooku by default, and you can, if you so wish, define your own.

* **authority**: site - The authority defines the domain being used. Nooku separates components into domains, for example,
site, admin and koowa. If you omit this, it will be koowa.

* **path**: foo.controller.bar - The path is the real meat of the identifier, and really defines the resulting object,
it is split into 3 separate parts:

  * **package**: foo - Remember components from the introduction? Well this part of the identifier defines the component
  or “package” being requested

  * **path**: controller - The path within the component to the type of resource you are requesting. This can be multiple
  levels deep, separated by periods (.)

  * **name**: The specific name of the resource you are requesting

In the above case, we are requesting the bar controller, within the foo component, in the frontend.

Some example identifiers are as follows:

	com://site/foo.controller.bar
	com://site/autos.model.vehicles
	com://admin/autos.view.dealers.html

** Using identifiers instead of class names allows Nooku to perform a lot of it's Magic. **

You don’t need to create the file components/com_foo/controllers/bar.php or the class ComFooControllerBar because Nooku will work out and include the relevant file and instantiate the relevant class for you, based on the identifier.
And you will always know that the resource that a particular identifier refers to will be the same resource, regardless of where it is used.

### So how does one use this identifier?

There are 2 ways of creating an object using an identifier:

`$object = KObjectManager::getInstance()->getObject('identifier')` - If the resource is being requested from outside a
Nooku class (anything that doesn’t extend KObject)

`$object = $this->getObject('identifier')` - If the resource is being requested from inside a Nooku class (anything
that extends KObject)

You will almost always use the second method as you will be working within a Nooku object most of the time, for example:

```php
	// Get the bar controller in the foo component within the site domain
	$this->getObject('com://site/foo.controller.bar')

	// Get the mysql adapter from the database package in framework
	$this->getObject('lib:database.adapter.mysql')
```
### Which Class?

An object identifier can actually correspond to a number of class names. When you call the [getObject](http://api.nooku.org/source-class-KObject.html#_getObject) method Nooku loops
through various class names using a _fallback class_ hierarchy system to find the first class name that exists in the application.
Using our `com://site/foo.controller.bar` identifier the default hierarchy for components is as follows:

    Com<Package><Path><File>  <=>   ComFooControllerBar
	ComKoowa<Path><File>	  <=>   ComKoowaControllerBar
	ComKoowa<Path><Default>	  <=>   ComKoowaControllerDefault
	K<Path><Name> 			  <=>   KControllerBar
	K<Path><Default>		  <=>   KControllerDefault

This hierarchy is important.

+ It looks for the class first inside your component's ./controller folder, by the **name** (which is the `<File>`) part of the identifier.
+ If found, it uses that class.
+ If not, **it means that you haven't written one** and the Framework will then cycle through the rest of the hierarchy to find the first class that exists,
which in the above example would be `ComKoowaControllerDefault`.

That's part of the Magic.

>    Checkout [Naming Conventions](naming-conventions.md) to really get a handle on where your classes go.

So long as the class exists within the Framework, you can create an object from the identifier, and Nooku will do the
legwork _(and cache the result in APC for performance)_.

And that's pretty much it. There is plenty more you can do with object identifiers that we will cover later on, like setting aliases or
config information, but for now, we'll leave it at that.
