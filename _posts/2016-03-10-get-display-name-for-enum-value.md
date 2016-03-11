---
layout: post
title: "Get the Display Name for an Enum Value"
date: 2016-03-10
tags: [csharp, aspnet]
---

Grabbing the display name from an `Enum` value in order to display it on an ASP.NET view is a no-brainer, but as of ASP.NET Core 1.0 RC1, there doesn't appear to be a built-in way to easily retrieve the data annotations from enum values. So... Reflection to the rescue!

```cs
using System;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Reflection;

public static class EnumExtensions
{
    /// <summary>
    /// Retrieves the <see cref="DisplayAttribute.Name" /> property on the <see cref="DisplayAttribute" />
    /// of the current enum value, or the enum's member name if the <see cref="DisplayAttribute" /> is not present.
    /// </summary>
    /// <param name="val">This enum member to get the name for.</param>
    /// <returns>The <see cref="DisplayAttribute.Name" /> property on the <see cref="DisplayAttribute" /> attribute, if present.</returns>
    public static string GetDisplayName(this Enum val)
    {
        return val.GetType()
                  .GetMember(val.ToString())
                  .FirstOrDefault()
                  ?.GetCustomAttribute<DisplayAttribute>(false)
                  ?.Name
                  ?? val.ToString();
    }
}
```

A nice, clean one-liner using new C# 6.0 language features to retrieve a `DisplayAttribute` if it exists, and return its `Name` property, otherwise, fallback to the enum's member name instead.

Usage:

```html
<span>@Model.MyEnumProperty.GetDisplayName()</span>
```
