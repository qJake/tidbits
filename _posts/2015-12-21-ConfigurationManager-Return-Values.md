---
layout: post
title: "AppSettings Return Values using ConfigurationManager"
date: 2015-12-21
---
`ConfigurationManager` uses the following rules to determine what to return when indexing into the `AppSettings` collection:

1. If there is a value present, return it as a string.
2. If there is no value present (empty string), return an empty string.
3. If there is no value attribute defined, return an empty string.
4. If there is no matching key defined, return `null`.

## Example

Given these settings:

{% highlight xml linenos %}
<appSettings>
  <add key="Key1" value="abc123" />
  <add key="Key2" value="" />
  <add key="Key3" />
</appSettings>
{% endhighlight %}

And the following code:

{% highlight csharp linenos %}
Console.WriteLine($"[{ConfigurationManager.AppSettings["Key1"] ?? "NULL"}]");
Console.WriteLine($"[{ConfigurationManager.AppSettings["Key2"] ?? "NULL"}]");
Console.WriteLine($"[{ConfigurationManager.AppSettings["Key3"] ?? "NULL"}]");
Console.WriteLine($"[{ConfigurationManager.AppSettings["Key4"] ?? "NULL"}]");
{% endhighlight %}

The following output is produced:

    [abc123]
    []
    []
    [NULL]
