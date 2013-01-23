# Concepts

## 2.1 Components

Whilst components aren’t the most important concept, they are pivotal to understanding how Nooku works. Think of Nooku as providing the architecture to build an SKD for the web, a component being akin to a package. Components play a very important role in how Nooku is built now, and how it will develop in the future. Components currently fall into two categories

Dispatchable components (or extensions as they will be know soon)
Non-dispatchable components

Dispatchable components can be thought of as applications, that can be accessed through an HTTP request, and will generally take the form of a blog, shopping cart, user manager, directory, etc.

The HTTP request takes the following form:

```
http://hostname/index.php?option=com_package&view=viewname
```

**option**: defines the component being dispatched
view: defines the view (and subsequently the controller) being requested
Other keys can be added that will alter the model state, but more on that later.

Non-dispatchable components are components that are used to make other components/extensions. Examples of this might be a category manager, tagging system, version control, comments, etc.

Components are typically built up of controllers,models and views, but can contain a wide array of parts.

Using the HMVC (more later on the **H**) concepts of Nooku, components can easily be built up from other components, to allow for a great amount of code reuse, and speed up your development lifecycle.
##2.2 Service Location

This is probably the most important development concept that you may not be familiar with:

Service location de-couples an object’s class name from its identifier.

But hold on, an object’s class name is its identifier, it is what PHP uses to identify (and in cases locate) that object. Well that doesn’t have to be the case, and therein lies the power of service location. By separating the identification of an object from its class name, one can use a unique identifier to instantiate the object, and not know or care about what the class name is. If you think about it, how important is the class name? It’s not, it is only used to infer its application within the system (sometimes to its detriment, yes Zend, that means you), and perhaps for location and auto loading purposes. What you care about is the resulting object that you’re actually going to use; not its class name.

So if you’re not using the class name for instantiating an object, what can you use. Well, a rather bright spark called Tim Berners Lee came up with a concept in the 80’s, no I don’t mean the internet, but glance up to the top of this page to the address bar and there you have it, the URI. Why reinvent the wheel? Someone way smarter than me came up with not only that, but a lot of other principles that exist within Nooku. So lets take advantage of a well established principle that has worked for well over 15 years now and use it to our advantage.


Above is an example of a URI. As you can see, each part of the URI has a meaning, and we can use this meaning to our advantage, to dynamically create a class name, because at the end of the day, we still need a class name at some point to instantiate our object.

You might be asking by now how this works in practice, so its time to get techy!

![Smaller icon](https://github.com/oligriffiths/nooku-docs/blob/resources/images/uri.jpg?raw=true)

In Nooku, we use scheme://authority/path - we don’t need query or fragment. A typical identifier may look like this:

	com://site/foo.controller.bar

Each part of this identifier has a meaning:

**scheme**: com - The scheme defines the type of resource you are requesting, there are some predefined scheme types that exist in nooku by default, and you can, if you so wish, define your own.

**authority**: site - The authority defines the application being used. Nooku separates components into 2 applications, site (frontend) and admin (backend) 

**path**: foo.controller.bar - The path is the real meat of the identifier, and really defines the resulting object, it is split into 3 separate parts:

**package**: foo - Remember components from the introduction? Well this part of the identifier defines the component or “package” being requested
path: controller - The path within the component to the type of resource you are requesting. This can be multiple levels deep, separated by periods (.)

**name**: The specific name of the resource your are requesting

In the above case, we are requesting the bar controller, within the foo component, in the frontend.

Some example identifiers are as follows:

	com://site/foo.controller.bar
	com://site/autos.model.vehicals
	com://admin/autos.view.dealers.html

So, thats kind of cool yeah? Well, is it really any benefit over doing something like 
`new ComFooControllerBar`? No, not really, infact its a longer string, however, this is where the power or “magic” of nooku comes into play. You don’t need to create the file components/com_foo/controllers/bar.php or the class ComFooControllerBar because Nooku will work out and include the relevant file and instantiate the relevant class for you, based on the identifier. And you will always know that the resource that a particular identifier refers to will be the same resource, regardless of where it is used.

Infact, the minimum you may need to do when creating a nooku component, is declare a database table (more on that later) and create a view folder by the name of that table. You can then access and manipulate the data in that table using Nooku’s built in REST API, oh did I forget to mention that? Yeah, there is a REST API built in out of the box, freakin’ cool eh? You can make a REST enabled web application without writing a line of code, seriously, you can (although if you don’t write any code, it wont do alot)!

So how does one use this identifier?

Well there are 2 ways of creating an object using an identifier

`$object = KService::get(‘identifier’)` - If the resource is being requested from outside a Nooku class (anything that doesn’t extend KObject)

`$object = $this->getService(‘identifier’)` - If the resource is being requested from inside a Nooku class (anything that extends KObject)

Most times you will use the second method as you will be working within a Nooku object.

E.g. 

	$this->getService(‘com://site/foo.controller.bar’)

The above refers to the bar controller in the foo component within the “site” application

	$this->getService(‘koowa:database.adapter.mysql’)

The above refers to the mysql adapter within the database package in the koowa framework. Packages within the Koowa framework do not have an application. The above is a valid URI, just in the format of a URN.

But how does Nooku know what class name to create? Well it loops through various class names using a fallback hierarchy prefix system to try and find the appropriate class name to be used. The default hierarchy for components is as follows:

	ComPackagePathSpecific 			<- E.g. ComFooControllerBar
	ComPackagePathDefault 			<- E.g. ComFooControllerDefault
	ComDefaultControllerSpecific 	<- E.g. ComDefaultControllerBar
	ComDefaultControllerDefault
	KControllerSpecific 			<- E.g. KControllerBar
	KControllerDefault

The KLoader class in turn deals with locating these classes on the filesystem, if the relevant file if found, then that is the class name used.

Thus, so long as the class exists within the Koowa framework, you can create an object off the identifier, and Nooku will do the legwork (all cached in APC for performance :)

And thats pretty much it. There is plenty more you can do with service identifiers that I will cover later on, like setting aliases or config information, but for now, I’ll leave it at that.
##2.3 HMVC

So you’ve heard of MVC I’m sure, if you haven’t, I feel for your code, now go google it and don’t come back until you know it :) There are plenty of descriptions and tutorials around the MVC pattern on the web, I don’t need to cover this.

HMVC however is a slightly newer concept and one that I’ve touched on already, composition. The H of HMVC stands for hierarchical, and is key to the concept of HMVC. The principle is that software applications can be built up hierarchically, through the use of composition of other self contained MVC triads. Each MVC triad is only aware of a single incoming request, to a controller, and the request is dispatched as appropriate. The following diagram should help solidify the concept:

![Smaller icon](https://github.com/oligriffiths/nooku-docs/blob/resources/images/hmvc.png?raw=true)

The power of this concept lies in the simplicity of its implementation, small reusable blocks that make up a larger more complex system. By developing stand alone, self contained MVC triads  and components one can build up a system out of predefined parts, in much the same way as you built a house out of lego as a child! It also makes testing a great deal easier, and components can be developed independently and concurrently, thus speeding up development again. 
##2.4 Naming Conventions


There are some specific naming conventions that Nooku uses, understand these is imperative to getting things working, as due to the “magic” if you get the names wrong, you will end up scratching your head saying “why isn’t this class being included?”

###2.4.1 Koowa

First things first, the Nooku framework library itself. The Nooku framework library is called Koowa (I don’t know why, hopefully Johan will clarify and I will update this).
As a result of this, all of the core Nooku classes are prefixed with the letter “K”. 

Classes in the Koowa library (located in /libraries/koowa) follow a very simple naming convention. They take the singular of the folder structure from the root of the koowa folder as the class name, e.g:

	koowa/controller/service -> KControllerService

and conversely:

	KViewHtml -> koowa/view/html.php

There is one exception, classes contain just 1 stub, like KConfig, or KObject related to a folder with a file by the same name as the folder, e.g.:

	KConfig -> koowa/config/config.php
	KObject -> koowa/object/object.php

###2.4.2 Components

Components are currently located in two places:

	administrator/components/com_(package name)
	site/components/com_(package name)

Folders within a component are always pluralised. Nooku contains an inflector class (KInflector) that is used to detect and convert plural and singular english words. This is used extensively in Nooku to translate identifiers and class names into their respective opposites.

File names depend upon the resource identifier being requested, and take the value of the “name” part of the identifier.

Class names however are always singular, with the exception of the identifier name (the specific resource you are looking for).

For example:

	site/components/com_foo/controllers/bar.php -> ComFooControllerBar
	site/components/com_foo/models/bars.php -> ComFooModelBars

and the same in reverse.

	ComFooControllerBar -> site/components/com_foo/controllers/bar.php
	ComFooModelBars -> site/components/com_foo/models/bars.php

There are some other specific naming conventions depending upon the resource being requested.

####Controllers

Controllers are always singular, this is due in part to the fact that the BREAD actions (more on that later) refer to a single resource, eg, an article, or a post.

####Models

Models are always plural, and map directly to an associated database table by default.

####Views

Views are singular or plural depending upon the view being requested. Views generally map to a database table and either return multiple rows or a single row.
Views a further sub divided by response type (HTML/JSON/CSV/RSS). 
Singular views refer to a single item and require the model state to be unique (more on models later). 
Plural views refer to multiple items, and may be filtered by states defined in the model (more on models later)

N.B. One important thing to note; if a view does not have an associated database table, it will not be accessible. In this case, you will need to create a specialised controller by the singular name of the view in the controllers folder, and have it extend ComDefaultControllerResource. More on this later, e.g. 

	site/components/foo.controller.test.php -> 
	ComFooControllerTest extends ComDefaultControllerResource{}

View identifiers must contain the response format required, eg:

	com://site/foo.view.bars.html	-> site/components/com_foo/views/bars/html.php
	com://site/foo.view.bars.json -> site/components/com_foo/views/bars/json.php

Oh yeah, Nooku has a built in JSON REST API that will return the data from any view in JSON format, nifty eh? Oh, and it also has JSONP support :) 

The concept of a view in many frameworks is often convoluted and mixes both the view and template together, so I ask you to forget what you know about views for now and remember only this. 

The view requests data from the model, and holds that data, along with any other data that you see fit to store (along with the model state), and decides how to render that data. HTML Views use a template system, all other views will render their own output (although arguably they should/should go through specialized templates too).

View layouts are located within a tmpl folder within the view `(views/viewname/tmpl/layoutname.php)` and can be selected by specifying the layout to the view. E.g.

	echo $this->getService(‘com://site/foo.view.bars.html’)
		->layout(‘list’)
		->limit(10);

Alternatively the layout can be specified as a $_GET parameter
index.php?option=com_foo&view=bars&layout=list&limit=10

This will cause the list layout to be rendered with the first 10 rows fetched by the view from the model. Views also implement a “fluent interface” for setting model states (again, more on that later) that allows you to chain method calls together, in much the same way as well written javascript frameworks. (see http://martinfowler.com/bliki/FluentInterface.html)

###Databases

Database tables have a few naming conventions, that make a lot of sense when you think about them. Some of you may use these concepts already, others may not.

Database tables are all prefixed with a system prefix, this is to allow shared used of a database with other systems/CMS’s etc. 

The prefix constant in nooku is `#__` 

This constant is rarely used in your code, so itsn’t something that needs to be worried about, and the value for this can be anything you want when you setup the system. By default, the convention is jos_ (inherited from joomla, may change).

After the prefix, tables have the component/package name added, followed by an underscore.
The last part of the table name defines its actual name, and should always be plural.
For example:

	jos_foo_bars
	jos_foo_documents
	jos_foo_cars

There is also a convention of the primary key being the component name, followed by an underscore, and the singular of the table name, e.g.

	foo_bar_id
	foo_document_id
	foo_car_id

If you think about it, these names make a lot of sense. The table always contains multiple rows, thus should be plural. A row is always singular, and thus its primary key should be the singular of the table, followed by _id. Fret not however, Nooku will automatically translate the primary key (whatever you call it) into a property of the name ‘id’. So from your code point of view, just refer to it as $object->id;

When data is requested by the model from the database, data will be returned as one of two object, a KDatabaseRow or a KDatabaseRowset

#####KDatabaseRow
This is a single object that represents an instance of a row from the database. Rows are saveable and deleteable and hold the data from the DB internally. Columns are accessible as if they were public variables. The name of the row within the view will be the name of the singular view by default.
#####KDatabaseRowset

This is a collection of KDatabaseRows from the database. The object is iterable and specific rows can be requested from the set using 

	$rowset->find($id);
	
The name of rowsets within the view will always be the name of the plural view.