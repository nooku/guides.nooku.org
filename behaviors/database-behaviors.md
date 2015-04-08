# Database Behaviors

Utilizing database behaviors lets us create [Trigger](http://en.wikipedia.org/wiki/Database_trigger) like procedures in our applications, without having to store them with the database. They provide a means to contextually affect change both before and after `select`, `insert`, `update` and `delete` actions performed in the database layer, specifically in the KDatabaseTableAbstract.

Behaviors are strategies, and because they are separated out we can use them in many other table objects. Each one extends from `KDatabaseBehaviorAbstract` and thus, `KMixinAbstract`. Because they are [mixins](/essentials/mixin.md) they add methods and functionality to the row level objects (See getAuthor in the [Example](#example) below).

You can build your own database behaviors in your own applications, or adjust the logic of currently ones to better match your needs by simply extending them.

If you choose to build new behaviors, simply place them in the `/database/behavior/` folder of your component. To get some inspiration look at some of the [Example](#example) below.

## Example

One of the easiest examples to understand is the [`KDatabaseBehaviorCreatable`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/creatable.php) database behavior. Its purpose is to simply make sure that when we insert a record into a table, it adds the creation date (`created_on`), and the user id number (`created_by`) of the logged in user.

Additionally, the behavior provides a `getAuthor` method to the row object and which grabs the `created_by` of the object and returns a system level user object, so you don't have to for example, get the user id and then create the user object yourself.

### Benefits

+ We never have to re-write the code that makes sure that we record those values in another table.
+ There is no question: "Where do we write that code?" In the controller, model or table classes? We are defining these actions at the table level and the logic is cleaner for each of those structures is cleaner.
+ It helps promote standardization of column names across tables for this information. In the `creatable` behavior user id is always saved as `created_by` and the date is always stored in `created_on`.

### The Complete List of Database Behaviors

Let's list all of the framework level behaviors and give some background about each.

+ [Creatable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/creatable.php) - Strategy for storing information about the creator of a record and the date.
+ [Hittable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/hittable.php) - Provides the row object with the interface to increment the  hit count, usually for counting views. Requires column name `hits` and most appropriately of  **int** type.
+ [Identifiable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/identifiable.php) - Handle storing a [Universal Unique Identifier]() string along with the object when the object is first stored. Requires a **unique** column called `uuid`.
+ [Lockable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/lockable.php) - Locking a row record precludes it from being edited by some other user while another user is editing it.  When a row is lockable, it gets all the methods it needs to control that lock. Applicable columns include `locked_by` and `locked_on`.
+ [Modifiable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/modifiable.php) - Handles the automatic recording of the last user to modifying a record.
+ [Orderable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/orderable.php) - Provides all that's required to maintain a database table's  `ordering` column when a row is inserted or updated.
+ [Parameterizable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/parameterizable.php) - Handles the storing and retrieval of serialized data from a specified column in a table. The default column is `parameters`, but others can be specified. The formats are PHP, INI, JSON, Yaml and XML.
+ [Sluggable](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/database/behavior/sluggable.php) - Builds a unique token string based on column values specified in its configuration. The default column is `title` but others can be added. An example outcome: if a row object has a `title` property set to "Red Dog", the `slug` property will become "red-dog".
