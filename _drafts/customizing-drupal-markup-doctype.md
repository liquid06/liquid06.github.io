---
layout: article
title: Altering every bit of markup in a Drupal 7 site
byline: Drupal is kind of a strange place
summary: You can still think Drupal is OK even if you're super-specific about how you like your markup.
---

I saw a good talk at DrupalCamp Phoenix one time about how to alter markup in Drupal. It recommended some modules at a high level; like display suite, using custom templates and using theme functions to override stuff.

In this series, I'll take a Drupal 7 base site and alter every piece of markup that comes out of core. It'll be fun!

I'm starting with a basic Drupal 7 site using the Stark theme and the minimal install profile. I'll cover all the Drupal-specific parts of the front-side of the site and how to customize them.

The first thing a modern developer might want to change is the doctype. Drupal core comes with a doctype that looks like this

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.0//EN"
  "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-1.dtd">
  
Using the stark theme out-of-the-box, this validates. But the html5 doctype is so much simpler though.

<!DOCTYPE html>

The way to override this first simple part is by having the theme override html.tpl.php. Copy it from your-drupal-root/modules/system/html.tpl.php into your custom theme.

Running the resulting markup through the regular w3c validator using the experimental html5 support, I got a valid result by changing:

1. the doctype. Just to the nice simple one
2. Removing the grddl profile from the head - seems that's outdated now (removed the comment line describing what it's for from the docblock too)
3. Adding lang to the html element with the same value as xml:lang
4. Taking version off the htmle element

I like approaching things from the inside-out, which is also a good method if you're working with an already-defined style guide, but changing the doctype first gives you the opportunity to use html5 elements when marking up those things.
