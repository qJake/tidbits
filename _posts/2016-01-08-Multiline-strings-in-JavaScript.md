---
layout: post
title: "Multiline strings in JavaScript"
date: 2016-01-08
tags: [javascript]
---
Multiline strings in JavaScript, especially if newline characters (`\r\n`) should be preserved, can be achieved by appending `\r\n\` to the end of each line.

~~~ javascript
var multiline = "This is a\r\n\
multiline string\r\n\
which will preserve things like\r\n\
    indentation, and other special\r\n\
s  p  a  c  i  n  g,\r\n\
especially if used in conjunction with\r\n\
a <pre> tag.";
~~~

This works because ordinarily the newline character is forbidden in a JavaScript string, but if the line ends with a `\` character, the newline character immediately after it is escaped, so JavaScript ignores it and continues on to the next character.

Another technique (a little uglier, in my opinion) is to simply concatenate multiple lines together:

~~~ javascript
var multiline = "This is also\r\n" +
"a multiline string\r\n" +
"using string concatenation.";
~~~

The downside to this technique is that you could not simply take a string, append `\r\n\` to the end of each line, and paste the contents between `""`, like you can with the previous example.

----

If the newline characters do not need to be preserved (for example, you are writing an HTML template inline), you do not need to re-enter the newline characters:

~~~ javascript
var template = "<div>\
  <ul>\
    <li>Item 1</li>\
    <li>Item 2</li>\
  </ul>\
</div>";
~~~

----

In ECMAScript 6, [Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings) will become a feature, so all of these will be valid:

~~~ javascript
var stringA = `a string`
var stringB = `also
a
string`;
var stringC = `Hello, ${data.name}!
You live in ${data.city}, ${data.state}.`;
~~~
