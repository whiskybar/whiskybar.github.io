---
layout: page
title: "Jekyll Bootstrap"
description: ""
---
{% include JB/setup %}

Screw it! [Jekyll Bootstrap](http://jekyllbootstrap.com) to write content 
instead of blogging about writing a blog. [Github](http://github.com) to host 
the result -- which is a collection of static pages!

Now to customize styles a bit and find out how to embed images and specify 
permalinks.

Obstacles
=========

Not so fast! Why could not I see this post at first?

### 1. A Post? How?

Oh okay,

    rake page name="Jekyll-Bootstrap.md"

filled in some content, went to http://localhost:4000, but where is it? 
Aaaaw, it has to go to `_posts/` and has to follow the naming `Y-M-D-title.md`.

    mv Jekyll-Bootstrap.md _posts/2013-11-03-Jekyll-Bootstrap.md

There you go. It is in the list `Here’s a sample “posts list”.` Good!

