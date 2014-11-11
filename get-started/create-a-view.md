# Create a View

Let's recreate (or _not create_ as the case may be) the Items View as we did in the Front End version, but this time use a table to display the data. Just create the file

    /administrator/components/com_todo/view/items/tmpl/default.html.php

And add the following did before

```php
    <table>
        <thead>
            <th><?=translate('ID') ?></th>
            <th><?=translate('Title') ?></th>
            <th><?=translate('Description') ?></th>
        </thead>
      <? foreach($items as $item) : ?>
            <tr>
                <td>
                    <?= $item->id?>
                </td>
                <td>
                    <a href="<?= route('view=item&id='. $item->id ) ?>">
                                <?= $item->title ?>
                       </a>
                </td>
                 <td>
                    <?= $item->description?>
                </td>
             </tr>
        <? endforeach; ?>
    </table>
```

With the this template file in place and point the browser to

http://joomla.dev/todo_tutorial/administrator/index.php?option=com_todo

_we still get an error "View: todos not found"_

### Don't get discouraged! All of this has a point!

The Framework is still trying to load the same "todos" view, because we haven't told it to do any different.
