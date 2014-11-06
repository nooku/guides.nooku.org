# Tada Component Backend

Now we're going to create the back-end for our component, com_tada.
In other words we will create the environment for an administrator to manage all the Todo Items in the system: create/modify/delete Items.

## The Entry-Point aka "The Component Loader"

The entry-point contains the very first code that gets executed when you visit

http://joomla.dev/tutorials/administrator/index.php?option=com_tada

To get going just like the Front End component create the following file

    /administrator/components/com_tada/tada.php

Then place the following code in that file:

```php
<?php echo KObjectManager::getInstance()->getObject('com://admin/tada.dispatcher')->dispatch();
```

**Tip:** This file is the exact same as in the Front End version of the com_tada entry point except that the dispatcher
identifier is for `admin` instead of `site`.

This line simply loads the dispatcher and calls the dispatch action.

Now, try pointing your browser to

http://joomla.dev/tutorials/administrator/index.php?option=com_tada.

*You will get an error*.

When there is no `view` parameter in the request Nooku tries to load the pluralized name of the component as its view, in our case 'tadas' and
redirects your browser to http://joomla.dev/tutorials/administrator/index.php?option=com_tada&view=tadas. But this won't work because we haven't defined the view.

The `view` is all important. It tells the component dispatcher which `Controller` to load, which loads the `Model`
 and the actual `View` object. The `view` in a Nooku Framework component is fundamental. This is why that at least the folder
 structure and the template of the view needs to be defined. The Framework needs to know how you want that information rendered.

## Create a View

Let's recreate (or _not create_ as the case may be) the Items View as we did in the Front End version. Just create the file

    /administrator/components/com_tada/views/items/tmpl/default.html.php

And add the same code we did before:

```php
    <ul>
    <? foreach($items as $item) : ?>
        <li>
            <?=$item->id?>
            <?=$item->title?>
            <?=$item->text?>
        </li>
    <? endforeach; ?>
    </ul>
```

With the this template file in place and point the browser to

http://joomla.dev/tutorials/administrator/index.php?option=com_tada

_we still get an error "View: tadas not found"_

### Don't get discouraged! All of this has a point!

The Framework is still trying to load the same "tadas" view, because we haven't told it to do any different.

## The Dispatcher

In our case we don't want this to happen since we don't want to have any view named "tadas", it simply doesn't make
sense. Instead, we would like our default view to be a list of our Todo "items". We have to create a specialized
dispatcher that will override the Framework's default.

First, create the file

`/administrator/components/com_tada/dispatcher/http.php`

and insert the following code

```php
    <?php
    class ComTadaDispatcher extends ComKoowaDispatcherHttp
    {
        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'controller'	=> 'item'
            ));
            parent::_initialize($config);
        }
    }
```

Looking at the first line we can already notice that we're extending the `ComKoowaDispatcherHttp`. This is the class which gets loaded
in response to an HTTP request if we don't have our own dispatcher class defined.

>For reference, its located at `/libraries/koowa/components/com_koowa/dispatcher/http.php`.

This way, we're telling our component to load the "item" controller as the default. Which is the same as saying:

>When there is now view param in the request, we want to see items

If we point our browser to

http://joomla.dev/tutorials/administrator/index.php?option=com_tada

the Framework will redirect the request to

http://joomla.dev/tutorials/administrator/index.php?option=com_tada&view=items

_which is great!_

We already defined our default layout for a list of our items.

#### Optional Exercise: Create a Dashboard

The above is the simplest way to get your backend going. However, there is often a desire to create some kind of Dashboard
in the administrator application side of a component. Maybe you want some statistics related to your Todos, maybe some instructions
for other admins using your component, or even data from another part of the system that you think is relevant.

Note: If you haven't read about the MVC architecture you are strongly advised to do so. Being familiar with MVC is required
to understand the Framework.

In this case a Dashboard
A) is singular
B) won't have a single table or model associated with it

To implement this view and have it be our default screen we'll need to do a few things.

1) As we did in the Front End we need to define a controller for the dashboard that extends `ComKoowaControllerView`

We create the file

`/administrator/components/com_tada/controllers/dashboard.php`

And add the following code

```php
<?php  class ComTadaControllerDashboard extends ComKoowaControllerView{}
```

2) Then create a template layout

`/administrator/components/com_tada/views/dashboard/tmpl/default.html.php`

And add the following code

```php
<h1>Tada Dashboard</h1>
```

3) Update the `ComTadaDispatcher` and make `dashboard` the default controller instead

```php
    <?php
    class ComTadaDispatcher extends ComKoowaDispatcherHttp
    {
        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'controller'	=> 'dashboard'
            ));
            parent::_initialize($config);
        }
    }
```

That's it. Pointing your browser to http://joomla.dev/tutorials/administrator/index.php?option=com_tada will now load your new Dashboard.

## 3. The Controller

Lastly, a little bit about the Controller. Aside from the example for a Dashboard above there is no need for us to create
a Controller for the Tada Items. The behavior provided by the `ComKoowaControllerModel` is robust and fulfills all of our needs,
and this is the class that the Framework falls back to.

Because we have defined a database table that conforms to the naming conventions that the Framework expects, we don't need to define a class.

For the sake of illustration though, lets say that we wanted to load a different layout as the default instead of the default.html.php file
we defined above. We could do this in the new `ComTadaControllerDashboard` by making sure to specify the layout in the request part of the config.

With all controllers extending the [`KControllerAbstract`](http://api.nooku.org/class-KControllerAbstract.html) class, we can define
default `request` variables in the `_initialize` method.

```php
    <?php

    class ComTadaControllerDashboard extends ComKoowaControllerView{

        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'request'	=> array('layout' => 'custom')
            ));
            parent::_initialize($config);
        }

    }
```

As long as we have created that `custom` layout in the right place, it will get loaded if the layout parameter is not
set in the url for this request.

We can set other request variables in this manner that will have the potential to affect how and how much of our data
 gets represented. For example, if we wished to change the default maximum number of items returned in a collection, we
 would do so by creating `ComTadaControllerItem` and  setting the `limit` request variable in the `_initialize` method

```php
    <?php
    # file: /administrator/components/com_tada/controllers/item.php
    class ComTadaControllerItem extends ComKoowaControllerModel
    {

        protected function _initialize(KObjectConfig $config)
        {
            $config->append(array(
                    'request'	=> array('limit' => '100')
            ));
            parent::_initialize($config);
        }

    }
```