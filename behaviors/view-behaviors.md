
# View Behaviors

There is one major action in the view and that's the `render` method. Consequently, we get an opportunity to affect change in the data before it gets rendered into a view, or change output after that rendering.

There are no view behaviors included in the Framework, but that doesn't mean that they aren't useful. Here are some potential example uses:

+ change the template of a view due to a model state
+ for a given situation pull in other data from other MVC triads
+ register filters with the Template engne before it renders
+ changing the template engine from [`koowa` to `mustache`, `markdown` or `twig`](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/template/engine) to complile some output, and then switching back to render the rest
+ effecting the schema used in CSV or JSON return.

Maybe you can think of more. If you do, the pattern for creating and adding a behavior to a view is the same as for the other types in this section. Simply make a new directory in your component's view folder called `behavior` and place your code there. After that, make sure you've added the name of the behavior to the `$config` variable in your view's `_initialize` method.   
