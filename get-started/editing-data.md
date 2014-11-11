# Editing Todo Items

What good is a management level component if you can't edit the data. Let get into the steps you need to follow to
get that working.

## Load a Form to Edit Your Items

When you click the "Todo item one" link in the items view , a <span style="color: red;">500 error</span> should be generated.

    The template "com://admin/todo.item.default.html" cannot be located.

>Note: If you put debugging on in Configuration a lot more info is available.

This error should be _Expected_. Remember, the layout file is the only thing we absolutely need to explicitly write for a given `view`.

We can go ahead and create a new template file

`/administrator/components/com_todo/view/item/tmpl/default.html.php`

And enter the following code

```html
<form action="<?= route('id='.$item->id) ?>" method="post" class="-koowa-form">
    <div>
    	<div>
		<input  type="text" name="title" id="title" size="40" maxlength="255"
		        value="<?= $item->title; ?>"
		        placeholder="<?= translate( 'Title' ); ?>" />
	</div>
	<?= helper('editor.display', array(
	                    'name' => 'description',
                        'id' => 'description',
                        'width' => '100%',
                        'height' => '300',
                        'cols' => '60',
                        'rows' => '20'
			    ))
	?>
    </div>
</form>
```

We should end up with something which is similar to this

![Todos List With Toolbar](/resources/images/todotutorial/todo-form-no-toolbar.png)

## Add a Toolbar to Your Item View

You may notice that something seems to be missing. There are none of the important toolbars we discussed in the last section.

Toolbars are **quite easy** to add to any Nooku powered component. We can get them to render simply by adding the following
code anywhere in the template we just created

```html
<ktml:style src="media://koowa/com_koowa/css/koowa.css" />
<ktml:module position="toolbar">
    <ktml:toolbar type="actionbar" icon="item-add icon-pencil-2">
</ktml:module>
```

#### WHOA! What is all That?

Those are special `ktml` namespace tags. The `ktml` part helps to avoid name collisions with any other tags. More importantly,
the Nooku template engine finds all of these tags when it compiles a given template and performs special processing for us.

In this example, the `<ktml:toolbar>` tag gets filtered and replaced with a real controller toolbar. The `<ktml:module>` tag gets filtered and
anything inside it gets *injected* into the template position as defined in the tag's `position` attribute, which in this case is fittingly `toolbar`.

If you were wondering about the `<ktml:style src="[url]" />` tag, that's a special way of saying

>"I want the stylesheet in the `src` attribute pushed into the `<head />` tag of this page".

And if you happened to noticed `media://` in that url, well that also gets filtered to the URL of your `/media`
folder, which in our case is `http://joomla.dev/todo_tutorial/media/` ... _**That is handy**_

If you refresh your page you should see something similar to

![Todos Item With Toolbar](/resources/images/todotutorial/todo-form-with-toolbar.png)

## Add a Toolbar to Your List View

Now that we know how use some of these tags, we should add something similar to our list view to get an appropriate `toolbar` there as well.
Open up

`/administrator/components/com_todo/view/items/tmpl/default.html.php`

And add the following code

```html
 <ktml:style src="media://koowa/com_koowa/css/koowa.css" />
 <ktml:module position="submenu">
     <ktml:toolbar type="menubar">
 </ktml:module>
 <ktml:module position="toolbar">
     <ktml:toolbar type="actionbar" title="ITEMS" icon="item icon-stack">
 </ktml:module>
```

![Todos List With Toolbar](/resources/images/todotutorial/todo-list-with-toolbar.png)


## Adding the Menu Bar: Optional Exercise

If you were really paying attention you may have noticed we added a little extra module injection in that last example.

```html
 <ktml:module position="submenu">
     <ktml:toolbar type="menubar">
 </ktml:module>
```

We didn't see it do anything special happen because of this code. That's because we have not defined a controller menu bar.

>Unlike our actionbar, the menubar has no default commands in it. That means it won't render automatically.

Let's use the Dashboard view from our last Optional Exercise to illustrate. Again, predictable naming conventions tell us where to put our
`menubar` class.

Let's create a file at

`/administrator/components/com_todo/controller/toolbar/menubar.php`

And in that file place the following class definition

```
<?php
class ComTodoControllerToolbarMenubar extends ComKoowaControllerToolbarMenubar{

    function getCommands()
    {

        $view = KStringInflector::singularize($this->getController()->getView()->getName());

        // add the dashboard view to the menu
        $this->addCommand('Dashboard', array(
            'href' => 'index.php?option=com_todo&view=dashboard',
            'active' => ($view == 'dashboard')
        ));

        // add the items view to the menu
        $this->addCommand('Items', array(
            'href' => 'index.php?option=com_todo&view=items',
            'active' => ($view == 'item')
        ));

        return parent::getCommands();
    }
}
```_Let's explore what's happening here._

When any toolbar is about to be rendered, the `getCommands` method is called. There are no commands registered in the `menubar`
by default and so it is empty until we add them. If the `view` we are looking at matches a command we are adding, it gets
an `'active' => true` and is highlighted as active on the page.

Go ahead and refresh your browser at http://joomla.dev/tada_tutorial/administrator/index.php?option=com_todo&view=items

![Todos List With Menubar](/resources/images/todotutorial/todo-list-with-menubar.png)

UP Next: Permissions