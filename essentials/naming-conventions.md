# Naming Conventions

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