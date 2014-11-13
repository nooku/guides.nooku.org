# Add a Toolbar to Your Item View

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
folder, which in our case is `http://joomla.dev/todo/media/` ... _**That is handy**_

If you refresh your page you should see something similar to

![Todos Item With Toolbar](/resources/images/todotutorial/todo-form-with-toolbar.png)
