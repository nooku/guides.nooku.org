## Update/rebuild custom theme

The only thing we're adding right now is the menu bar, nothing else. This small tutorial shows how to quickly rebuild a theme.

1. Rename current 'theme' folder to 'theme_backup'
2. Copy/paste latest theme from [Gitbook repo](https://github.com/GitbookIO/gitbook/tree/master/theme)
3. Copy the `custom.css` and images from 'theme_backup' to 'theme'
4. Add `custom.css` AFTER `style.css` to the proper page to load it (search for `style.css` in 'theme' folder)
5. Add `<link href='http://fonts.googleapis.com/css?family=Roboto:400,300,300italic,700,400italic' rel='stylesheet' type='text/css'>` before `style.css`
6. Add `<link rel="stylesheet" href="http://www.nooku.org/css/header.css">` after `custom.css`
7. Add the following right after `app.js`:
```<script type="text/javascript" src="http://www.nooku.org/js/scripts.js"></script>
   <script>
       responsivemenu.init({
           wrapper: document.querySelector('.header'),
           toggleclass: 'button--hamburger',
           togglecontent: 'menu <span class="lines"></span>'
       });
   </script>
```
8. Add markup to the right file (currently `page.html`). Add it just before `<div class="book"></div>`
9. Add `class="no-js"` to `<body>`
10. Add following right below `<body class="no-js">`:
```<script data-inline type="text/javascript">function hasClass(e,t){return e.className.match(new RegExp("(\\s|^)"+t+"(\\s|$)"))}var el=document.body;var cl="no-js";if(hasClass(el,cl)){var reg=new RegExp("(\\s|^)"+cl+"(\\s|$)");el.className=el.className.replace(reg," js-enabled")}</script>```
11. Run `gitbook serve` and test if menu is working (mobile first)
12. On success remove theme_backup