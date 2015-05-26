# Overrides

## Template and Layout Considerations

When you look at a component view your are seeing the output of a template. When working in Joomla the template system follows the same rules
as the Joomla CMS does, but does a good deal of extra work to give our templates extra flexibility and power. We discuss some of
the more Joomla-centric implications here.

Its important that we make a distinction between a `layout` and a `template` with regard to component views. A `layout` in Joomla
normally references a the entire component level view and will have an `xml` file of the same root file name (everything before the first period '.' in the file name)
associated with it.

This is what lets use choose a `layout` for a menu item. A `layout` is also a `template`, and can have contain other templates. You can
use the following to remember the distinction:

>All Layouts are Templates, but not all Templates are Layouts

It is because of this that you will often see the terms used interchangeably.

## Template Overrides

Template overrides allow you to change how a component's output is rendered without having to modify the original
template files. The main benefit of this is that component upgrades won't overwrite your modified files.

The template engine we use is similar to that of Joomla, but turbo charged! All Framework templates can be overridden
from within the active application template, and follow the same folder structure as standard Joomla template overrides.

In order to override a component view layout, you must create a few folders and a PHP file within the active application template folder.

The folder structure is as follows:

	/templates
		-> template_name
			-> html
				-> com_component_name
					-> view_name
						-> layout_name.php

* **template_name** - this is the name of the active application template
* **html** - all template overrides go in an **html** folder
* **com_component_name** - this is the name of the component, e.g. com_docman, com_fileman, etc
* **view_name** - the name name of the view whose template is being overridden
* **layout_name** - the specific layout being overridden, e.g. default.html.php, list.html.php

An example override might be `/templates/beez/html/com_docman/documents/list.html.php`, which overrides the `list` template, in
the `documents` view, in the `com_docman` component for pages when the application is using the `Beez` template. That file's existence
tells the application that you want to use it, as opposed to the original file `components/com_docman/views/documents/tmpl/list.html.php`.

Again, this allows you to modify the output of a Framework component without changing the core files.
