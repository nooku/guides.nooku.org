# Your First Todo List View

Your component Views are how you present your data to the world.

We got you started off in the [Front End](front-end.md) part of the tutorial by creating a file called

    /components/com_todo/view/items/tmpl/default.html.php

Open it up and lets get to work showing our todo items!

> Remember, we're using http://joomla.dev/todo/index.php?option=com_todo&view=items

## Display Data in the Template

Writing templates in Nooku Framework is like writing regular HTML and PHP, but simpler. Add the following code to your open template
file:

```php
    <ul>
    <? foreach($items as $item) : ?>
        <li>
            <?=$item->id?>.
            <?=$item->title?>
            <?=$item->text?>
        </li>
    <? endforeach; ?>
    </ul>
```

This code will render an unordered list and for each entity in `$items` object it will render a `<li>` element with the Todo Item's id, title, and its description.

![My Nooku Powered Todo List](/resources/images/todotutorial/front-end-view.png)

Some important notes about our code:

1. We are using PHP short tags:

    * <? instead of <?php
    * <?= instead of <?php echo

    If short tags are disabled in your php.ini file, Nooku will automatically rewrite them as normal tags. So you don't
    have to worry about compatibility issues with some server setups. Again, you can use normal tags as well, but this is more compact.

2. We are using the [PHP alternative syntax](http://php.net/manual/en/control-structures.alternative-syntax.php) for control
structures such as the foreach statement, once again for readability.

3. We can simply use `$items` in the above example instead of `$this->items`. Nooku will by default assign items data to the
`items view` if you don't tell it otherwise..._More Magic_

4. Note that we have only added a template here; no `View` class. This is because Nooku will fallback on default classes
whenever your component does not contain a specific class that it's looking for, in this case the `items` HTML view. So
when `ComTodoViewItemsHtml` is not found, it will simply fall back on `ComKoowaViewHtml`, and assume that since you're requesting
the `items view`, that you want to view a list of Todo items.

<b>Tip:</b> You can also add a 'default' view class for your component, `ComTodoViewHtml`, if you want to use that as a fallback
instead of `ComKoowaViewHtml`. For HTML format requests your new class still needs to descend from `KViewHtml`.

Again, take note of the following: "item**s**" implies multiple items, while "item" implies a single post. Everywhere in Nooku, it's very
important to use singular and plural forms correctly. Thanks to `KStringInflector`, Nooku translates back and forth between most English words.

_It even knows words like "person" is singular, where "people" is its plural form. Similarly, "index" is the singular form of the plural "indices"._

## Using Template Shortcuts

The Framework provides some handy shortcuts and syntactical sugar for use in our view templates. They
help keep templates clean and function names a little more relevant to the designer. Here are some of them:

* `object()` => `KObject::getObject()`
* `translate()` => `KObject::getObject('translator')->translate()'`
* `route()` => `KViewTemplate::getRoute()`
* `json()` => `json_encode()`
* `format()` => `sprintf()`
* `replace()` => `strtr()`
* `escape()` => `KTemplate::escape()`
* `helper()` => `KTemplate::invoke()`
* `import()` => `KTemplateEngineKoowa::_import()`
* `parameters()` => `KTemplate::getParameters()`

Let try some in our example. Change the `ul` list in above example to:

```php
    <ul>
    <? foreach($items as $item) : ?>
        <li>
            <?=translate('ID'). ': ' . $item->id?>.
            <?=translate('Title'). ': ' . $item->title?>
            <?=translate('Text'). ': ' . $item->text?>
        </li>
    <? endforeach; ?>
    </ul>
```
If we had some date information defined in our database for each item, i.e. $item->created_on we could use the `helper` shortcut to load
the date helper and make that information easier to read for us humans.

```php
<?= helper('date.humanize', array('date' => $activity->created_on )); ?>
```

Also, when we have a singular Item view ready we could use the `route` shortcut to help us build our urls.
```php
  <a href="<?= route('view=item&id='. $item->id ) ?>">
            <?= $item->title ?>
   </a>
```

All of the shortcuts are there to help you **"Write Less Code"**. Mastering them will help you produce templates faster that
are ultimately easier to read.
