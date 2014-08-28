# Concepts

## Components

Whilst components aren’t the most important concept, they are pivotal to understanding how Nooku works. Think of Nooku as providing the architecture to build an SDK for the web, a component being akin to a package. Components play a very important role in how Nooku is built now, and how it will develop in the future. Components currently fall into two categories

* Dispatchable components (called extensions)
* Non-dispatchable components

Dispatchable components can be thought of as applications, that can be accessed through an HTTP request, and will generally take the form of a blog, shopping cart, user manager, etc.

Non-dispatchable components are components that are used as building blocks while making other extensions. Examples of this might be a category manager, tagging system, version control, comments, etc.

Components are typically built up of controllers, models and views, but can contain a wide array of parts.

Using the HMVC (more later on the **H**) concepts of Nooku, components can easily be built up from other components, to allow for a great amount of code reuse, and speed up your development lifecycle.


## Service Location

Service location decouples an object’s class name from its identifier.

But hold on, an object’s class name is its identifier, it is what PHP uses to identify (and in cases locate) that object. Well that doesn’t have to be the case, and therein lies the power of service location. By separating the identification of an object from its class name, one can use a unique identifier to instantiate the object, and not know or care about what the class name is. 

If you think about it, how important is the class name? It is not, it is only used to infer its application within the system and perhaps for location and auto loading purposes. What you care about is the resulting object that you are actually going to use; not its class name.

So if you’re not using the class name for instantiating an object, what can you use? Well, Tim Berners Lee came up with a concept in the 80’s, and no I don’t mean the internet, the URI. So we take advantage of a well established principle that has worked well for over a quarter century.

![URI](../resources/images/uri.jpg)

Above is an example of a URI. As you can see, each part of the URI has a meaning, and we can use this meaning to our advantage, to dynamically create a class name, because at the end of the day, we still need a class name at some point to instantiate our object.

Let's see how this works in practice.

In Nooku, we use scheme://authority/path - we don’t need query or fragment. A typical identifier may look like this:

	com://site/foo.controller.bar

Each part of this identifier has a meaning:

* **scheme**: com - The scheme defines the type of resource you are requesting, there are some predefined scheme types that exist in Nooku by default, and you can, if you so wish, define your own.

* **authority**: site - The authority defines the domain being used. Nooku separates components into domains, site and admin and koowa. If you omit this, it will be koowa.

* **path**: foo.controller.bar - The path is the real meat of the identifier, and really defines the resulting object, it is split into 3 separate parts:

  * **package**: foo - Remember components from the introduction? Well this part of the identifier defines the component or “package” being requested

  * **path**: controller - The path within the component to the type of resource you are requesting. This can be multiple levels deep, separated by periods (.)

  * **name**: The specific name of the resource you are requesting

In the above case, we are requesting the bar controller, within the foo component, in the frontend.

Some example identifiers are as follows:

	com://site/foo.controller.bar
	com://site/autos.model.vehicles
	com://admin/autos.view.dealers.html

By using identifiers instead of class names Nooku can perform a lot of magic. You don’t need to create the file components/com_foo/controllers/bar.php or the class ComFooControllerBar because Nooku will work out and include the relevant file and instantiate the relevant class for you, based on the identifier. And you will always know that the resource that a particular identifier refers to will be the same resource, regardless of where it is used.

In fact, the minimum you may need to do when creating a Nooku component, is to declare a database table (more on that later). You can then access and manipulate the data in that table using Nooku’s built in REST API.

So how does one use this identifier?

Well there are 2 ways of creating an object using an identifier:

`$object = KObjectManager::getInstance()->getObject('identifier')` - If the resource is being requested from outside a Nooku class (anything that doesn’t extend KObject)

`$object = $this->getObject('identifier')` - If the resource is being requested from inside a Nooku class (anything that extends KObject)

You will almost always use the second method as you will be working within a Nooku object.

E.g. 

	// Get the bar controller in the foo component within the site domain
	$this->getObject(‘com://site/foo.controller.bar’)

	// Get the mysql adapter from the database package in framework
	$this->getObject(‘lib:database.adapter.mysql’)

Nooku loops through various class names using a fallback hierarchy system to try and find the appropriate class name to be used. The default hierarchy for components is as follows:

    Com<Package><Path><File>  <- E.g. ComFooControllerBar
	ComKoowa<Path><File>	   <- E.g. ComKoowaControllerBar
	ComKoowa<Path><Default>	   <- E.g. ComKoowaControllerDefault
	K<Path><Name> 			   <- E.g. KControllerBar
	K<Path><Default>		   <- E.g. KControllerDefault\ControllerDefault
	
The class loader in turn deals with locating these classes on the filesystem, if the relevant file is found, then that is the class name used.

Thus, so long as the class exists within the framework, you can create an object off the identifier, and Nooku will do the legwork (and cache the result in APC for performance)

And that's pretty much it. There is plenty more you can do with service identifiers that I will cover later on, like setting aliases or config information, but for now, I will leave it at that.
## HMVC

The H of HMVC stands for hierarchical. The principle is that software applications can be built up hierarchically, through the use of composition of other self contained MVC triads. Each MVC triad is only aware of a single incoming request, to a controller, and the request is dispatched as appropriate. The following diagram should help solidify the concept:

![HMVC Triads](../resources/images/hmvc.png)

The power of this concept lies in the simplicity of its implementation, small reusable blocks that make up a larger more complex system. By developing stand alone, self contained MVC triads and components one can build up a system out of predefined parts, in much the same way as you build a house out of lego. It also makes testing a great deal easier, and components can be developed independently and concurrently, thus speeding up development again.
## Naming Conventions

There are some specific naming conventions that Nooku uses, understanding these is imperative to getting things working, as due to a bit of “magic” if you get the names wrong, you will end up scratching your head saying “why isn’t this class being included?”

Classes in the framework (located in koowa/libraries/) follow a very simple naming convention. They take the singular of the folder structure from the root of the koowa folder as the class name, e.g:

	koowa/controller/model.php -> KControllerModel

and conversely:

	KViewHtml -> koowa/libraries/view/html.php

There is one exception, a file can go into a subfolder as long as it has the exact same name with the folder. e.g:
	
	KCommandChain -> koowa/libraries/command/chain/chain.php
	
### Components

Components are currently located in three places:

	administrator/components/com_(package)
	site/components/com_(package)
	libraries/koowa/components/com_(package)

Components in libraries/koowa folder are non-dispatchable and serve as a building block for extensions.

Class names for components always take the `Com` prefix.

For example:

	site/components/com_foo/controllers/bar.php -> ComFooControllerBar
	site/components/com_foo/models/bars.php -> ComFooModelBars

and the same in reverse.

	ComFooControllerBar -> site/components/com_foo/controllers/bar.php
	ComFooModelBars -> site/components/com_foo/models/bars.php

#### Controllers

Controllers are always singular, this is due in part to the fact that the BREAD actions (more on that later) refer to a single resource, eg, an article, or a post.

#### Models

Models are always plural, and map to an associated database table by default.

#### Views

Views are singular or plural depending upon the view being requested. Views generally map to a database table and either return multiple rows or a single row.
Views a further sub divided by response type (HTML/JSON/CSV/RSS). 

Singular views refer to a single item and require the model state to be unique (more on models later). 

Plural views refer to multiple items, and may be filtered by states defined in the model (more on models later)

One important thing to note; if a view does not have an associated database table, it will not be accessible by default. In this case, you will need to create a specialised controller by the singular name of the view in the controller folder, and have it extend ComKoowaControllerView. More on this later, e.g. 

	site/components/com_foo/controller/test.php -> 
	ComFooControllerTest extends ComKoowaControllerView {}

View identifiers must contain the response format required, eg:

	com://site/foo.view.bars.html	-> site/components/com_foo/views/bars/html.php
	com://site/foo.view.bars.json -> site/components/com_foo/views/bars/json.php

The view requests data from the model, and holds that data, along with any other data that you see fit to store (along with the model state), and decides how to render that data. HTML Views use a template system, all other views will render their own output.

View layouts are located in a tmpl folder within the view `(views/viewname/tmpl/layoutname.php)` and can be selected by specifying the layout to the view. E.g.

	echo $this->getObject(‘com://site/foo.view.bars.html’)
		->layout('list')
		->limit(10);

Alternatively the layout can be specified as a $_GET parameter
index.php?option=com_foo&view=bars&layout=list&limit=10

This will cause the list layout to be rendered with the first 10 rows fetched by the view from the model. Views also implement a “fluent interface” for setting model states (again, more on that later) that allows you to chain method calls together, in much the same way as well written javascript frameworks. (see http://martinfowler.com/bliki/FluentInterface.html)

###Databases

Database tables are all prefixed with a system prefix, this is to allow shared used of a database with other systems/CMS’s etc. 

The prefix constant in nooku is `#__` 

This constant is rarely used in your code, so it is not something that needs to be worried about.

After the prefix, tables have the component/package name added, followed by an underscore. The last part of the table name defines its actual name, and should always be plural.

For example:

	#__foo_bars
	#__foo_documents
	#__foo_cars

There is also a convention of the primary key being the component name, followed by an underscore, and the singular of the table name, e.g.

	foo_bar_id
	foo_document_id
	foo_car_id

Note that, Nooku will automatically translate the primary key into a property of the name ‘id’. So from your code point of view, you can just refer to it as $object->id;

When data is requested by the model from the database, data will be returned as an entity object

####KModelEntityRow

This is a single object that represents an instance of a row from the database. Entities can be saved or deleted and hold the data from the database internally. Columns are accessible as if they were public variables. The name of the entity within the view will be the name of the singular view by default.

####KModelEntityRowset

This is a collection of entities from the database. You can iterate through the object for specific entities.

	$rowset->find($id);
	
The name of rowsets within the view will be the name of the plural view.