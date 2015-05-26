# Template System

The Framework extension templates expose unparalleled flexibility and power. What follows is a high level overview of the structures
we use so that you can better understand what's happening in each layout.

<a name="example-template"></a>
To start lets consider the following example component view template, named `default.html.php`:

```html
<?= helper('behavior.koowa'); ?>
<div id="example-template">
<h1><?= translate('Example Title') ?></h1>
<div class="control">
    <?= helper('listbox.category_id', array(
        'identifier'=>'com://site/acme.model.foos',
        'id' => 'category_select'
    )) ?>
</div>
    <?= import('default_list.html', array(
        'title' => translate('This is my list')
        )) ?>
</div>
<ktml:script src="media://com_acme/js/foo.js" />
<style>
 .control select option:selected { color: red }
</style>
```

>This file gets loaded following the same rules as regular Joomla layouts. Get some more details
 in [Overrides](overrides.md)

This is not a great deal of code, but there is a lot going on. In this template we are making use of [Functions](#functions),
[Helpers](#helpers), [Special Tags](#tags) and [Partials](#partials). We can make use of all of 
these to keep our templates compact, segmented and thus reusable.

## Partials

Partials are a great way of separating layouts into manageable chunks. They are just template files, that can be loaded on
their own, or included within another layout. We do this with the `import` template function. You saw an example of this above
with the line that looks like:

```php
<?= import('default_list.html'); ?>
```
**Note:** `<?=` is short for `<?php echo`, which gets replaced when the template is compiled.

The `default_list.html` in this case is the name of the template file itself without the `php` file extension. The above
method will attempt include a file called `default_name.html.php` from the same folder in which the parent files reside

> The template system also applies [Template Overrides](layout-considerations.md#template-overrides) to partials.

When using `import()`, a second argument can be supplied to pass additional variables to the included partial. For example:

```php
<?= import('default_list.html', array(
        'title' => translate('This is my list')
        )) ?>
```

This will create a variable in the partial called `$title` with a value of `This is my list` in the imported template. In addition,
 any variables that exist in the parent layout will be automatically be passed through to the partial.

## Helpers

Template Helpers are an incredibly useful tool for creating reusable template code. <!--Joomlatools extensions use them throughout
their respective presentation layers.--> These helpers can be used for all sorts of things: loading a javascript library, rendering form controls,
rendering pagination or the needed structure for tabs.

The Framework comes packaged with several helpers, including but not limited to:

* [**Accordion**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/accordion.php#L16):    methods to create an accordion menu
* [**Actionbar**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/actionbar.php#L16):    gathers and renders all the parts of the administrator application action bar for a given component (title and commands).
* [**Behavior**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/behavior.php#L16):     a set of javascript behaviors including, tooltips, overlays, keep alive, validator, autocomplete, sortable and calendar.
* [**Bootstrap**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/bootstrap.php#L16):    optionally loads all the requirements for a page to use Twitter Bootstrap CSS and Javascript components
* [**Date**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/date.php#L16):         date helper functions, including formatting and humanizing dates (e.g. 1 minute ago).
* [**Debug**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/debug.php#L16):    if JConfig 'debug' is enabled, this will provide some nicely formatted information
* [**Editor**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/editor.php#L16):    render `editor` for a given editable text field
* [**Event**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/event.php#L16):    `trigger` method to fire a plugin event
* [**Grid**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/grid.php#L16):         grid table controls, including checkbox, search, enable/disable, order, and access.
* [**Listbox**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/listbox.php#L16):      allows you to create a select list or auto-complete from data returned from a model.
* [**Menubar**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/menubar.php#L16):      gathers and renders all the parts of the administrator application menu bar for a given component (sub-menu links).
* [**Pagination**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/paginator.php#L16):   pagination and limit controls.
* [**Select**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/template/helper/select.php#L16):       select list, radio list, checkbox list and boolean list controls.
* [**Tabs**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/helper/tabs.php#L16):         html & javascript code for creating tab controls.

Helpers are invoked using the `helper` [template function](#functions). That function can take two arguments and a example could look something like:

```php
<?=  helper('com://site/acme.template.helper.foo.bar', array('of' => 'options'));
```

The first argument is a **required** string that is the helper's Object Identifier with a method name concatenated on the end with a period (.)

<!--<div style="font-size: 24pt; margin-bottom:10px;">`com://site/acme.template.helper.foo.bar`</div>-->
![Helper Identifier and Method Part](/resources/images/helper-identifier-and-method.png)

To rephrase, everything before the last period in the string is an Object Identifier and after, is a method that is in
that helper object's interface.

> Almost all Framework classes have an [Object Identifier](http://guides.nooku.org/essentials/object-management.html). They allow for the **Decoupling**
of the class name from how the application refers to an object. We cover them in detail in the [Framework Guides](http://guides.nooku.org).

The second argument is an **optional** array of options that are relevant to the helper method.

Our example helper call looks for the following class and tries to fire the `bar` method, while passing the array of options along.

<a name="myhelper"></a>

```php
/**
 * file would be located at components/com_acme/template/helper/foo.php
 */
class ComAcmeTemplateHelperFoo extends KTemplateHelperAbstract
{
    function bar($config = array()){
    {
          $config = new KObjectConfig($config);
          $config->append(array(
                   'of' => 'methods'
          ));
           return '<div>'. $config->of .'</div>';
    }
}
```
If that class is not found the system looks for an appropriately named substitute in the `template/helper` fallback hierarchy:

 1. `com_acme/template/helper/foo.php` => `ComAcmeTemplateHelperFoo`
 2. `libraries/koowa/components/com_koowa/template/helper/foo.php` => `ComKoowaTemplateHelperFoo`
 3. `libraries/koowa/library/template/helper/foo.php` => `KTemplateHelperFoo`

A helper identifier can take on the form of a fully qualified identifier as above, or a more convenient abbreviated form where just the file name
of the helper is used with the concatenated method. Hence, if we are working on a template in `com_acme` the following command will
produce the same result as the previous example:

```php
<?=  helper('foo.bar', array('of' => 'options')); // Write Less Code
```
The template engine assumes that the helper's identifier is `com://site/acme.template.helper.foo`.

In this case the system assumes that your helper classes are located somewhere in the `template/helper` fallback
hierarchy:

1. `com_acme/template/helper`
2. `libraries/koowa/components/com_koowa/template/helper`
3. `libraries/koowa/library/template/helper`

In our [example template](#example-template) in the introduction you saw us use this line of code in our example:
```php
<?= helper('listbox.category_id', array(
        'identifier'=>'com://site/acme.model.foos',
        'id' => 'category_select'
    )) ?>
```
That call looks for the `listbox` helper and tries to invoke the `category_id` method of its interface. If there is not
a `com_acme/template/helper/listbox.php` the system will load `libraries/koowa/components/com_koowa/template/helper/listbox.php`
instead and then pass along the array of configuration options to the method.


## Functions

There are a number of 'core' template functions of which our extensions make use. We've already introduced `helper` and `import` above.
Template functions let us shorten what can be lengthy object method calls, alias more obscure function names and generally help
keep the layouts clean.

Here are some of the mappings:


* `object()` => [`KObject::getObject()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/object/object.php#L275)
* `translate()` => [`KObject::getObject('translator')->translate()'`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/translator/abstract.php#L111)
* `route()` => [`KViewTemplate::getRoute()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/view/template.php#L243)
* `json()` => `json_encode()`
* `format()` => `sprintf()`
* `replace()` => `strtr()`
* `escape()` => [`KTemplate::escape()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/template/template.php#L217)
* `helper()` => [`KTemplate::invoke()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/template/template.php#L234)
* `import()` => [`KTemplateEngineKoowa::_import()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/template/engine/koowa.php#L364)
* `parameters()` => [`KTemplate::getParameters()`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/libraries/template/template.php#L286)


When a particular layout gets compiled the above mappings are applied and the corresponding calls evaluated. What that means
for example is that calling `object()` inside a template file is the same as calling `KObject::getObject()`.

## Tags

Tags are one more important part of the template dialect that the templating system uses. The engine finds the
[`style`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/style.php),
[`script`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/script.php),
[`meta`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/meta.php),
[`link`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/link.php)
and
[`title`](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/title.php) tags
in a template, filters them out, and adds them to the head document. The `style` and `script` tags
can optionally be given the `data-inline` attribute, which lets them stay exactly where there are in the layout.

Special `ktml` namespace tags (i.e.`<ktml:toolbar></ktml:toolbar>`) also get filtered and replaced with specialized dynamic
output. We use the `ktml` namespace to avoid name collisions with other tags.

* `<ktml:script src=[url]>` - Render a script tag with specified source url and place that tag in the head.
* `<ktml:style src=[url]>`  - Render the appropriate `link` tag, with `href` from the `src`, and add it to the head.
* `<ktml:module position=[position]>` - Take the content contained inside the tag, and **inject** it into the specified module `position`.
* `<ktml:toolbar>` - Find the controller toolbar which is currently active and render its output. Often used inside a `<ktml:module>` tag.
* `<ktml:content>` - Gets the currently rendered content from the template object. Allows the current layout to `decorate` that content,
and replace it back into the template object.
* `<ktml:message>` - Render the response flash messages.

### Other Special Filters

[**Assets**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/asset.php)

You may also see special [scheme](http://en.wikipedia.org/wiki/URI_scheme) information in the URLs that our templates use to load resources.
For URLs in a layout that follow this form, the template system relies on the use of another filter that replaces them with the appropriate
`http://` scheme, relevant domain and path information for that resource.

Above, we used the following to get a javascript file into the head of the page.
```html
  <ktml:script src="media://com_acme/js/foo.js" />
  ```

That `media://` scheme specification, gets replaced with the current URL for the media folder, i.e. `http://joomla.dev/media/`. In
combination with the `ktml:script` tag, the final result gets added to the head in the form:

```html
<script type="text/javascript" src="http://joomla.dev/media/com_acme/js/foo.js"></script>
```
There also `base://` and `root://` url schemes which load the `base` url and `root` url of your application, respectively.

[**Version**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/version.php)

If enabled, this filter will run at the beginning of rendering, before the Assets filter is run. It finds all those `media://` urls we were just talking about and provides a `versioned` fingerprinting
of the file url, based on the version of the component. This ensures that the right version of the media file gets cached in the client browser.

Our `<ktml:script src="media://com_acme/js/foo.js" />`<br>
gets updated to<br>
`<ktml:script src="media://com_acme/js/foo.js?c81e728d" />`

[**Document**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/document.php)

This filter feeds information from JDocument into the template to be used in page rendering. It is a special case, because it will
only run when we want to bypass the application template, for example in a modal.

[**Chrome**](https://github.com/nooku/nooku-framework/blob/master/code/libraries/koowa/components/com_koowa/template/filter/chrome.php)

This is a _Module_ focused filter. When a module built using the Framework is rendered, this filter allows for an appropriate [`chrome`](http://docs.joomla.org/Module_chrome) style to be applied to the
module content.

## Summing Up

We've taken a high level look at the Nooku Framework template system. With a solid understanding of these fundamental pieces you can
easily gain insight into what's happening in those templates.  More importantly, you can also customize all of the views of extensions built with the Frameowrk and
add information that you want to see.



