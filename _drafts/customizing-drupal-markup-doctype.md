---
layout: article
title: Altering (every bit) of markup in html.tpl.php
byline: Drupal is kind of a strange place
summary: Drupal 7 custom theming for fun and ultimate markup control.
---

I don't know anyone who likes Drupal's markup. Neither understanding why it's done the way it is nor a reassuring *it's better in Drupal 8* erase the extra cruft in the markup coming out of a Drupal 7 site. I've seen display suite, custom templates, and theme functions recommended as good methods for overriding Drupal's output. I've seen these recommended quite a bit, but I'd like to do a more in-depth exploration, not of each technique, but of altering all the markup. I saw a good talk at DrupalCamp Phoenix one time about how to alter markup in Drupal. It recommended some modules at a high level; like display suite, using custom templates and using theme functions to override stuff.

In this series, I'll take a Drupal 7 base site and alter every piece of markup that comes out of core. It'll be fun!

I'm starting with a basic Drupal 7 site using the Stark theme and the minimal install profile. I'll cover all the Drupal-specific parts of the front-side of the site and how to customize them.

The first thing a modern developer might want to change is the doctype. Drupal core comes with a doctype that looks like this

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.0//EN"
  "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-1.dtd">
```

Using the stark theme out-of-the-box, this validates. But the html5 doctype is so much simpler though.

```html
<!DOCTYPE html>
```

The way to override this first simple part is by having the theme override html.tpl.php. Copy it from your-drupal-root/modules/system/html.tpl.php into your custom theme.

Running the resulting markup through the regular w3c validator using the experimental html5 support, I got a valid result by changing:

1. the doctype. Just to the nice simple one
2. Removing the grddl profile from the head - seems that's outdated now (removed the comment line describing what it's for from the docblock too)
3. Adding lang to the html element with the same value as xml:lang
4. Taking version off the htmle element

I like approaching things from the inside-out, which is also a good method if you're working with an already-defined style guide, but changing the doctype first gives you the opportunity to use html5 elements when marking up those .

Other things to customize in the html.tpl.php template

* What's output for the <title> element ($head_title, which includes the site name, title and slogan)
* The markup for the "Skip to main content" link. This link is hidden until focused, and it sends to user to #main-content which is part of page.tpl.php. The styling for the link is controlled by system.base.css, so if you remove that stylesheet, don't forget to style the skiplink

(official doc page)[https://api.drupal.org/api/drupal/modules%21system%21html.tpl.php/7]
implement template_preprocess_html to change what's available here.

Another thing that could be altered in the template preprocess function are what body classes are output to the page. Sometimes Drupal themers use the class on the body element to scope styling to a page, or section. The Zen theme provides additional classes that identify a page and section, by including a template_preprocess_html

Normal classes output: html front logged-in one-sidebar sidebar-first page-node admin-menu plus there are more for the node type (if you're on a node) and for all the theme suggestions. So instead of making an individual tpl.php file to theme a page, modifications to the styling could still be made with CSS.

Modules add to the list of classes as well; for example, admin-menu is used by the admin menu module to add some visual space to the top of the page so the menu doesn't obscure the content. Using template preprocess, extra classes you don't want to use can be stripped while still allowing contribs to add the ones they need.

```php
THEMENAME_preprocess_html {
  // Classes for body element. Allows advanced theming based on context
  // (home page, node of certain type, etc.)
  if (!$variables['is_front']) {
    // Add unique class for each page.
    $path = drupal_get_path_alias($_GET['q']);
    // Add unique class for each website section.
    list($section, ) = explode('/', $path, 2);
    $arg = explode('/', $_GET['q']);
    if ($arg[0] == 'node' && isset($arg[1])) {
      if ($arg[1] == 'add') {
        $section = 'node-add';
      }
      elseif (isset($arg[2]) && is_numeric($arg[1]) && ($arg[2] == 'edit' || $arg[2] == 'delete')) {
        $section = 'node-' . $arg[2];
      }
    }
    $variables['classes_array'][] = drupal_html_class('section-' . $section);
  }
}
//from zen
```
