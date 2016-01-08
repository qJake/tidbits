---
layout: post
title: "Make an HTML Container Full-Height"
date: 2016-01-07
tags: [html, javascript]
---
Simply set the height of the container to the height of the window (viewport), and wrap it in the `window.resize` event.

~~~ javascript
$(window).resize(function()
{
    $('.full-height').height($(window).height());
});

// Fire the event so the container's height is set on page load
$(window).resize();
~~~
