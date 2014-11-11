# The Controller Package

Lastly, a little bit about the Controller package. Aside from the example for a Dashboard above there is no need for us to create
a Controller for the Todo Items. The behavior provided by the `ComKoowaControllerModel` is robust and fulfills all of our needs,
and this is the class that the Framework falls back to.

**Because we have defined a database table that conforms to the naming conventions that the Framework expects, we don't need to define a class.**

### Controller Toolbars

One of the more important pieces in the backend is the Toolbar. The administrator toolbar is the series of command buttons at the
top of the screen that you are all familiar with. For our component to work properly and be user friendly, we need to
be able to create, edit and delete Todo items. And guess what, if you don't need anything special, you don't have to create these either as Nooku
give you a nice implementation out of the box.

We touch on the Toolbars a little more in the next section.
