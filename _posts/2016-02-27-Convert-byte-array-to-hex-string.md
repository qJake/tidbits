---
layout: post
title: "Convert a Byte Array to a Hex String"
date: 2016-02-27
tags: [csharp]
---
Often times (most notably during hashing/encrypting), you'll have a `byte[]` but want a string of hex values.

There are [a lot of ways to solve this problem](http://stackoverflow.com/questions/311165/how-do-you-convert-byte-array-to-hexadecimal-string-and-vice-versa), some much more performant than others. If performance isn't a huge issue, I always prefer this one-liner, which is mentioned in the post. I also like to make it an extension method for easier use.

```csharp
public static string ToHexString(this byte[] bytes)
{
    return BitConverter.ToString(bytes).Replace("-", "");
}
```
