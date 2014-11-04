# Editing Data

What good is a management level component if you can't edit the data. Let get into the steps you need to follow to
get that working.

## Backend form view

When you click a Title link in the previous admin view example, an 500 error should be generated.

If you put debugging on in Configuration a lot more info is available.

    Exception 'KTemplateException' with message 'Template "/components/com_blog/views/post/default.html.php" in ...'  not found.

Remember, in our "Display Data in the Template" already mentioned before in the naming conventions, a default list view goes into views/posts/tmpl/default.html.php and
to edit a single post we need to create a form in `/administrator/components/com_blog/views/post/tmpl/default.html.php`.

Enter the code below in form.php
```php
    <form action="<?= @route('id='.$post->id) ?>" method="post" class="-koowa-form">

    <div>
    	<div>
		<input type="text" name="title" id="title" size="40" maxlength="255" value="<?= $post->title; ?>" placeholder="<?= @text( 'Title' ); ?>" />

	</div>
	<?= helper('editor.display', array('name' => 'text',
			'id' => 'text',
			'width' => '100%',
			'height' => '300',
			'cols' => '60',
			'rows' => '20')
		)
	?>
    </div>
    </form>
```

This code will get and render all data from the selected post in a new form, or create an empty form when the New button was clicked, with a title input and editor field and the default Save, Apply and Cancel buttons.

BTW, have you seen the placeholder tag? Nooku fully supports HTML5 and CSS3.

