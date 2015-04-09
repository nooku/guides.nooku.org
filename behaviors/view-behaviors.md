
# View Behaviors

There is one major action in the View and that's the `render` method. That means we get an opportunity to affect change in the data before it gets rendered into a view, or change output after that rendering.

There are no View behaviors included in the Framework, but that doesn't mean they aren't useful. Here are some potential example uses:

+ change the template of a view due to a model state
+ for a given situation pull in other data from other MVC triads
+ register filters with the Template engne before it renders
+ changing the template engine from [`koowa` to `mustache`, `markdown` or `twig`](https://github.com/nooku/nooku-framework/tree/master/code/libraries/koowa/libraries/template/engine) to complile some output, and then switching back to render the rest
+ effecting the schema used in CSV or JSON return.

Maybe you can think of more. 
