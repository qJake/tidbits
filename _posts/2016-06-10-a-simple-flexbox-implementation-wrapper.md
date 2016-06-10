---
layout: post
title: "A Simple LESS Flexbox Implementation / Wrapper"
date: 2016-06-10
tags: [css, less, html]
---

I've piggy-backed off a Flexbox LESS Mixin snippet from [@jayj](https://gist.github.com/jayj/) to create a simple Flexbox wrapper wth easy-to-remember syntax.

## Example 1

A simple column-based container, your standard Flexbox implementation:

```html
<div class="f">
  <div></div>
  <div></div>
  <div></div>
</div>
```

## Example 2

A vertical flexbox ("rows") with wrapping, and where the column container is centered in the parent container.

```html
<div class="fr f-wrap fr-align-container-center">
  <div></div>
  <div></div>
  <div></div>
</div>
```

## Example 3

An otherwise-tricky thing to do in CSS, made easy with flexboxes - vertically and horizontally centering a div inside a container:

```html
<div class="f f-align-center f-valign-middle">
  <div>I'm centered!</div>
</div>
```

## Example 4

A vertically-centered toolbar with left and right content areas, and a "flexible space" in-between:

```html
<div id="toolbar" class="f f-valign-middle">
  <div>Left-aligned Content Area</div>
  <div class="flex"></div>
  <div>Right-aligned Content Area</div>
</div>
```

## The Code

<script src="https://gist.github.com/qJake/e4d26a0eede24d3768e53e3f9b3c2665.js"></script>
