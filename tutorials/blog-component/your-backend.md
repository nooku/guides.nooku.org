# Your Blog Component Backend

Now we're going to create the back-end for our component, com_blog.
In other words we will create the environment for an administrator to manage his blog: create/modify/delete posts & categories.

##1. The Entry-Point aka "The Component Loader"

The entry-point contains the very first code that gets executed when you visit _http://joomla.dev/tutorials/administrator/index.php?option=com_blog_.

Note: From now we'll refer to "http://joomla.dev/tutorials/" with "~" instead, for obvious reasons.

To get going just like the Front End component create the following file

    /administrator/components/com_blog/blog.php

Then place the following code in that file:

```php <?php echo KObjectManager::getInstance()->getObject('com://admin/blog.dispatcher')->dispatch();```

**Tip:** This file is the exact same as in the Front End version of the com_blog entry point except that the dispatcher identifier is for `admin` instead of `site`.

This line simply loads the dispatcher and call the dispatch action.

Now, try pointing your browser to _"~/administrator/index.php?option=com_blog"_. *You will get an error*.

When there is no `view` parameter in the request Nooku tries to load the pluralized name of the component as its view, in our case 'blogs' and
redirects your browser to _~/administrator/index.php?option=com_blog&view=blogs_. But this won't work because we haven't defined the view.

## Create a View

Let's recreate (or _not create_) the Posts View as we did in the Front End version. Just create the file

    /administrator/components/com_blog/views/posts/tmpl/default.html.php

And add the same code we did before:

    <ul>
    <? foreach($posts as $post) : ?>
        <li>
            <?=$post->id?>.
            <?=$post->title?>
            <?=$post->text?>
        </li>
    <? endforeach; ?>
    </ul>

With the view in place and pointing toward the URL _"~/administrator/index.php?option=com_blog"_ we still get an error "View: blogs not found" as Nooku tries to load the corresponding "blogs" view.

<h3>Don't get discouraged! All of this has a point!</h3>

##2. The dispatcher

In our case though, we don't want this to happen since we don't want to have any "blogs" view, it simply doesn't make
sense. Instead, we would like our default view to be let's say "dashboard", where an admin could see an overview of blog posts, maybe comments, users etc. To do this, we have to create a specialized dispatcher that will override the default one.

So we create the file _"~/administrator/components/com_blog/dispatcher.php"_ and insert the following code:

```php
    <?php
    class ComBlogDispatcher extends ComKoowaDispatcher
    {
        protected function _initialize(KConfig $config)
        {
            $config->append(array(
                    'controller'	=> 'dashboard'
            ));
            parent::_initialize($config);
        }
    }
```

Looking at the first line we can already notice that we're actually extending the _ComDefaultDispatcher_ class which lies in the default dispatcher (_~/administrator/components/com_default/dispatcher.php_).  This way, we're telling our component to load "dashboard" as the default controller. If we point to _~/administrator/index.php?option=com_blog_ we 'll see an error as the "dashboards" view will be loaded instead of "dashboard". Nooku does that automatically, because it supposes that we want to view a list of something (remember, plural) since it doesn't finds the controller we defined in the dispatcher.

## 3. The Controller
Note: If you haven't red about the MVC architecture you are strongly adviced to do so. Being familiar with MVC is required to understand the Nooku framework.

We create "/controllers/dashboard.php" with the following code:

    <?php
    class ComBlogControllerDashboard extends ComDefaultControllerResource
    {
        protected function _initialize(KConfig $config)
        {
            $config->append(array(
                'request' => array('layout' => 'default'),
            ));

            parent::_initialize($config);
        }
    }


With this line, we're telling our controller to request the 'layout' to use for our view template and we assign the default value to it if no layout input is detected 'default'. Point to your component's entry-point now (aka ~/administrator/index.php?option=com_blog) and you'll get this result:

View: dashboard not found

So we can see that the framework is looking for the 'default.php' file which is the template of our view 'dashboard'. Just notice the directory structure and you can all figure it out. If we change "default" in our controller code to something like "testing" and refresh the entry-point, you'll get the corresponding error stating that "testing.php" is missing now. In the same way, if you just change the "testing" in the URL in your browser to something else, you'll get another error corresponding to what you've entered.

to be continued