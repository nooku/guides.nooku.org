# Add a Toolbar to Your Item View

You may notice that something seems to be missing. There are none of the important toolbars we discussed in the last section.

Toolbars are **quite easy** to add to any Nooku powered component. We can get them to render simply by adding the following
code anywhere in the template we just created

```html
<?= helper('behavior.koowa'); ?>
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
folder, which in our case is `http://joomla.dev/todo/media/` ... _**That is handy**_

You may have also noticed the `<?= helper('behavior.koowa'); ?>` line. It loads the Javascript required for the toolbar
to work.

If you refresh your page you should see something similar to

![Todos Item With Toolbar](/resources/images/todotutorial/todo-form-with-toolbar.png)

## Saving HTML in the 'description'

By illustration, we showed you how to use the `editor` helper. If you try to save HTML in your description though, you will
 find that your Todo's description gets stripped of that HTML.

Nooku loads predefined column filters when dealing with your data, depending initially on the `type` we set for that column.
For `description` we set the type to `longtext` (back in [Creating the Database](creating-the-database.md) ) and that column
type gets filtered as a `string` (see [KFilterString](http://api.nooku.org/class-KFilterString.html)).

Since we want to allow HTML in our descriptions we'll need to let the Nooku know that its OK to let HTML through.

**We do that through a Table object**

Just create the following file

    /administrator/components/com_todo/database/table/items.php

And add the following code

```php
<?php
class ComTodoDatabaseTableItems extends KDatabaseTableAbstract
{
    protected function _initialize(KObjectConfig $config)
    {
        $config->append(array(
                'filters' => array(
                    'description'  => array('html')
            )
        ));
        parent::_initialize($config);
    }
}
```

With that in place, Nooku knows to let HTML through the filter for the `description` column.

### Try it out!