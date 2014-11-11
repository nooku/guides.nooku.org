# Load a Form to Edit Todos

What good is a management level component if you can't edit the data. Let get into the steps you need to follow to
get that working.

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


