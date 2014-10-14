# Write a Tutorial

Writing a tutorial for the Framework Guides is a great way to contribute to the community and solidify your own knowledge.
We encourage you to share any accomplishments that you are proud of in Tutorial form. To help with that, we've provided the
following formatting guide. It should help keep your submissions consistent with the rest of the tutorials and make sure
your message gets across.

<!-- toc -->

### Tutorial Title

The title should be clear and concise. It should contain the principal subject and the task at hand.
For example:

<pre><b>"Building 'Permissable' Behaviors for Your Component"</b></pre>

### Author (optional)

You can provide your name, email and one url.

### Filename

The guides and tutorials are written in markdown and published using Gitbook. The filename will therefore needs the '.md'
file extension. It should be based on the Tutorial Title and be no more than four (4) words connected to each other with
a hyphen '-'. Also,  the filename should not collide with any class names in the Framework, i.e. _kcontrollermodel.md_ .

Here’s a good one based on our [Tutorial Title](#tutorial-title) from above: _building-permissable-behaviors.md_

### Level

These are beginner, intermediate, advanced (most will be intermediate, advanced).

### Relevant Sections and Classes

Provide the user a class list (and link to the appropriate page in http://api.nooku.org if possible) and areas of concern or relevant Framework sections.

For example:

**classes:** [KControllerModel](http://api.nooku.org/class-KControllerModel.html), [KControllerView](http://api.nooku.org/class-KControllerView.html), [KControllerBehaviorPermissable](http://api.nooku.org/class-KControllerBehaviorPermissible.html)

**areas of concern:** Naming Conventions, Behaviors, Controllers

### Task

State the objective of the tutorial here. Should not be more than one (1) or two (2) sentences. This statement should be reflective of the title.

### Solution

This is the meat of the entire tutorial. It should be a set of step by step instructions on how to get the task done.
All code examples should be triple backtick enclosed with the appropriate language label. For example:

    ```php
    function ask(){
        echo "Do you Nooku?";
    }
    ```

Images and screenshots are also a welcome addition and can help crystallize the information you are trying impart to your reader.
Note, image file names should be descriptive and non-generic (not screenshoot-20141007.png), and should go into the
    ```./resources/images/tutorials/[filename]/[imagename]```, where the [filename] is the same as you use for your
tutorial. For example:

    ./resources/images/tutorials/building-permissable-behaviors/after-denied-read-view.png

### Discussion (optional, but recommended)

This should be a deeper discussion of the theory and the background behind the solution. Potentially some rationale for
the need to do this, the benefits, and unintended consequences (the gotchas) of the technique.

### References

Give credit where credit is do and share the wealth when it comes to great resources.
List any relevant references your reader may find useful.
