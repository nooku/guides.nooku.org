# Using the Joomlatools Vagrant Box

To help you get moving we suggest (and assume for the rest of the tutorial) that you have the Vagrant box from
[developer.joomlatools.com](http://developer.joomlatools.com/tools/vagrant/introduction.html) on your local machine.

After you get your vagrant box set up and running, `ssh` into it

    $ vagrant ssh

When inside the box, use the `joomla` command line tool to create a new Joomla site

    $ joomla site:create todo

After that, make sure Nooku is installed with the following line

    $ composer require nooku/nooku-framework:2.*

That's it! Those two commands have installed Joomla and the Framework, and your tutorial sandbox
should be available at [http://joomla.dev/todo/](http://joomla.dev/todo/).

>At the very least you need Joomla with Nooku Framework installed in your preferred development environment.
