# Your Blog Views

Your component Views are how you present your data to the world. We go you started off in the [Front End](front-end.md) part of the
 tutorial by creating a file called:

    /components/com_blog/views/posts/tmpl/default.html.php

Open it up and lets show our blog posts!

> Remember, we're using http://joomla.dev/tutorials/index.php?option=com_blog&view=posts

## Display Data in the Template

Writing templates in Nooku Framework is like writing regular HTML and PHP, but simpler. Add the following code to your open template
file:

```php
    <ul>
    <? foreach($posts as $post) : ?>
        <li>
            <?=$post->id?>.
            <?=$post->title?>
            <?=$post->text?>
        </li>
    <? endforeach; ?>
    </ul>
```

This code will render an unordered list and for each post in `$posts` it will render a list item with the post id, title, and the text of the post.

Some important notes about our code:

1. We are using PHP short tags:

    * <? instead of <?php
    * <?= instead of <?php echo

    If short tags are disabled in your php.ini file, Nooku will automatically rewrite them as normal tags. So you don't
    have to worry about compatibility issues with some server setups. Again, you can use normal tags as well, but this is more compact.

2. We are using the [PHP alternative syntax](http://php.net/manual/en/control-structures.alternative-syntax.php) for control
structures such as the foreach statement, once again for readability.

3. We can simply use `$posts` in the above example instead of `$this->posts`. Nooku will by default assign posts data to the
posts view if you don't tell it otherwise..._More Magic_

4. Note that we have only added a template here; no View class. This is because Nooku will fall back on default classes
whenever your component does not contain a specific class that it's looking for, in this case the posts HTML view. So
when `ComBlogViewPostsHtml` is not found, it will simply fall back on `ComKoowaViewHtml`, and assume that since you're requesting
the posts view, that you want to view a list of posts.

<b>Tip:</b>You can also add a 'default' view class for your component, `ComBlogViewHtml`, if you want to use that as a fallback
instead of `ComKoowaViewHtml`.

Again, take note of the following: "posts" implies multiple posts, while "post" implies a single post. Everywhere in Nooku, it's very
important to use singular and plural forms correctly. Thanks to KStringInflector, Nooku translates back and forth between most English words.

It even knows words like "person" is singular, where "people" is its plural form. Similarily,  "index" is the singular form of the plural "indices".

## Using Template Shortcuts

The Framework provides some handy shortcuts are available for use in our views templates:

<pre>
  @helper(	expands to:	$this->loadHelper(
  @date(	expands to:	$this->loadHelper(\'date.format\',
  @overlay(	expands to:	$this->loadHelper(\'behavior.overlay\',
  @text(	expands to:	JText::_(
  @import(	expands to:	$this->loadIdentifier(
  @route(	expands to:	$this->getView()->createRoute(
  @escape(	expands to:	$this->getView()->escape(
</pre>

Let try some in our example. Change the `ul` list in above example to:

```php
    <ul>
    <? foreach($posts as $post) : ?>
        <li>
            <?=@text('ID').$post->id?>.
            <?=@text('Title').$post->title?>
            <?=@text('Text').$post->text?>
        </li>
    <? endforeach; ?>
    </ul>
```
If we had some date information for each post, i.e. $post->created_on we could use the `@date` short cut.

```php  <?= @date(array('date' => $post->created_on, 'format' => '%d %B %Y'));?> ```

Also, when we have a singular Post view ready we could use the `@route` shortcode to help us build our urls.
```php
  <a href="<?= @route('view=post&id='. $post->id ) ?>">
            <?= $post->title ?>
   </a>
```

All of the shortcuts are there to help you **"Write Less Code"**. Mastering them will help you produce templates faster that
  are ultimately easier to read.
