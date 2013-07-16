---
layout: default
title: You got your JSON in my HTML
---

I wanted to create a JavaScript version of an object I already had
on the Ruby side.  I computed `@search` in the controller to represent
a pretty complex search query.  I needed a JavaScript Object with the same
data so I could update the page.

The trick is to also do the `gsub` on the resulting JSON, or else any crafty request containing
script tags will invite an XSS attack

<script src="https://gist.github.com/zachad/6005528.js"></script>
