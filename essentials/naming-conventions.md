# Naming Conventions

There are some specific naming conventions that Nooku uses for its default "Magic" implementation.

<pre><h3>Understanding these is the key to getting things working!</h3></pre>

Because the Framework relies HEAVILY on the proper placement of class files and naming of class and naming of tables, if you
get them wrong, you may end up scratching your head saying “why isn’t this class being included?” or "Why is my View not being shown".
Your best, first stop in figuring out what happened to you class is to check these conventions.

When we talk about Naming Conventions for classes, really, what we are describing is the work being done by the Object Manager
in combination with the Class Locator packages of the Framework. These structures help locate and load the appropriate files that
your application is trying to use. Step through them to really get a sense of what is happening.

### Library Classes

Classes in the framework (located in koowa/libraries/) follow a very simple naming convention. They are the camelcase of
 each part of their directory path with the file name appended to the end.

	koowa/libraries/controller/model.php -> K + Controller + Model = KControllerModel

>the 'K' is interpreted as the koowa/libraries/ folder.

and conversely:

	KViewHtml -> koowa/libraries/view/html.php

There is one exception, a file can go into a subfolder as long as it has the exact same name with the folder. e.g:

	KCommandChain -> koowa/libraries/command/chain/chain.php

### Components

Components are currently located in three places:

	administrator/components/com_(package)
	site/components/com_(package)
	libraries/koowa/components/com_(package)

> We should note that Components in libraries/koowa/components folder are non-dispatchable and serve as a building block for extensions.

Class names for Components are very similar to Library classes in how they relate to the above directories, but always take the `Com` prefix.
Depending on which domain or application you are working in (site, administrator or something of your own creation) the `Com` part will
be interpreted as your components directory.

For example:

	site/components/com_foo/controller/bar.php -> ComFoo + Controller + Bar -> ComFooControllerBar
	site/components/com_foo/model/bars.php -> ComFooModelBars

the same in reverse.

	ComFooControllerBar -> site/components/com_foo/controller/bar.php
	ComFooModelBars -> site/components/com_foo/model/bars.php
	
Using underscores in the name:

	site/components/com_foo/controller/baz_bar.php -> ComFoo + Controller + Baz_bar -> ComFooControllerBaz_bar
	site/components/com_foo/model/baz_bars.php -> ComFooModelBaz_bars
	
the same in reverse.

	ComFooControllerBaz_bar -> site/components/com_foo/controller/baz_bar.php
	ComFooModelBaz_bars -> site/components/com_foo/model/baz_bars.php

#### Controllers

Controllers are always singular, this is due in part to the fact that the [BREAD](BREAD.md) actions (more on that later) refer to a
single resource type, eg, an article, or a post.

Your controller classes always go in the in the `./controller` directory of your component; NOT the plural <span style="color:red">./controllers</span>.

    ComFooControllerBar -> site/components/com_foo/controller/bar.php
       
Using an unserscore in the name:

    ComFooControllerBaz_bar -> site/components/com_foo/controller/baz_bar.php
#### Models

Your Model classes go in the `./model` folder of your component's directory.

For the Magic to work Models names are always plural. The rationale being that they map to an associated database table by default, and that database table always has more than one
row.

	com_foo/model/bars.php -> ComFoo + Model + Bars ->  ComFooModelBars

Using an unserscore in the name:

	com_foo/model/baz_bars.php -> ComFoo + Model + Baz_bars ->  ComFooModelBaz_bars

#### Views

The View naming conventions are slightly different in how the Class names are constructed in relation and how the files are named and names
of the views themselves.

First though, lets summarize their major characteristics:

* singular or plural depending upon the view being requested.
* generally map to a database table
* either return multiple rows or a single row (The "B" and the "R" in [BREAD](BREAD.md), respectively ).
* have several possible format types: HTML, JSON, CSV, RSS or any others that you create yourself.

Your component views go into their own folder in the  `./views` folder. The file names of the actual view classes correspond directly to the
format they are meant to represent. Also, singular and plural views (and their directories) are separated and named accordingly, so if we keep running with our current `com_foo` example:

    com_foo/views/bar/html.php, is our singular HTML view .
    com_foo/views/bars/html.php, is our plural or list HTML view .

If we wanted to specify our own `json` for `bars` we would do so as follows:

    com_foo/views/bars/json.php

The Difference: In our other Component parts, the file name and object name match up. In the case of a view, the object name matches its directory.
This is what allows your component to have different format representations.

> Singular item views require the model state to be unique.
> Plural views refer to multiple items, and may be filtered by states defined in the model.
> We'll talk more about Model States in later sections of the Guide.

###Databases

The Database table naming conventions are also a central piece to making Magic with Nooku. Using them properly frees you from the need
to write one line of code related to models or tables if you are doing nothing out of the ordinary.

The table name convention is quite simple:

`#__` + `component name` + `_` + `plural entity name`

where `#__` is replaced with your already defined ````php JConfig::$dbprefix```, and the `component name` is without the `com_` prefix.

Following our Foo component example:

	#__foo_bars
	#__foo_documents
	#__foo_cars
	#__foo_baz_bars

There is a related convention for the primary keys for your tables as well:

 `component name` + `_` + `singular entity name` + `_id`

So that's the component name (again no, `com_`), followed by an underscore, and the singular of the table name. Respectively, of Foo component
tables get the following primary keys:

	foo_bar_id
	foo_document_id
	foo_car_id
	foo_baz_bar_id

> Note: Nooku will automatically translate the primary key into a property of the name ‘id’. So from your code point of
view, you can just refer to it as $object->id;

When data is requested by the model from the database, data will be returned as an entity object.

####Special Case: Underscores in File Names

You can use underscores in the file name as well. In this context, Nooku treats them exactly the same as a lowercase letter.

An example mapping could look like

site/components/com_foo/model/baz_bars.php -> ComFoo + Model + Baz_bars -> ComFooModelBaz_bars

####KModelEntityRow

This is a single object that represents an instance of a row from the database. Entities can be saved or deleted and hold
the data from the database internally. Columns are accessible as if they were public variables. The name of the entity
within the view will be the name of the singular view by default.

####KModelEntityRowset

This is a collection of entities from the database. You can iterate through the object for specific entities.

	$rowset->find($id);

The name of rowsets within the view will be the name of the plural view.