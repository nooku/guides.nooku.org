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
```

_Let's explore what's happening here._

When any toolbar is about to be rendered, the `getCommands` method is called. There are no commands registered in the `menubar`
by default and so it is empty until we add them. If the `view` we are looking at matches a command we are adding, it gets
an `'active' => true` and is highlighted as active on the page.

Go ahead and refresh your browser at http://joomla.dev/todo/administrator/index.php?option=com_todo&view=items

![Todos List With Menubar](/resources/images/todotutorial/todo-list-with-menubar.png)
