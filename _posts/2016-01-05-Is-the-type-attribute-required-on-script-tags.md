---
layout: post
title: "Is the 'type' attribute required on &lt;script&gt; tags?"
date: 2016-01-05
tags: [html, javascript]
---

For HTML 4.01 (and below), [the `<script>` tag specification](http://www.w3.org/TR/html401/interact/scripts.html#h-18.2.1) states that the `type` attribute is **required** (and is most always set to `text/javascript`).

For HTML 5, [the `<script>` tag specification](http://www.w3.org/TR/html5/scripting-1.html#script-processing-prepare) states that the type attribute is **optional**, and defaults to `text/javascript` if not specified.
