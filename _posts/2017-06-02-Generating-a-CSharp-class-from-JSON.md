---
layout: post
title: "Generating a Json.NET-powered C# class from a JSON object using PowerShell"
date: 2017-06-02
tags: [csharp, powershell]
---

I didn't see any good JSON converters out there that would also generate the `[JsonProperty("...")]`
attributes that [Json.NET](https://json.net/) uses, so I thought I'd write a quick PowerShell function
to do it for me.

With the help of some RegEx and the [TextInfo.ToTitleCase()](https://msdn.microsoft.com/en-us/library/system.globalization.textinfo.totitlecase(v=vs.110).aspx)
method, we can actually make proper C# property names, so instead of:

```csharp
public string client_alternate_name { get; set; }
```

Or even

```csharp
public string clientAlternateName { get; set; }
```

We generate correct property names that are capitalized ([as is recommended by Microsoft](https://msdn.microsoft.com/en-us/library/ms229043(v=vs.110).aspx)) and use the `[JsonProperty]` attribute for the "real" name, like this:

```csharp
[JsonProperty("client_alternate_name")]
public string ClientAlternateName { get; set; }
```

## Usage

To use the script, have your JSON ready in a file, or [get it using `Invoke-WebRequest`](https://blogs.technet.microsoft.com/heyscriptingguy/2015/10/08/playing-with-json-and-powershell/), 
then pipe in the JSON string to `ConvertTo-CSharpJsonClass`. The only other thing you'll need is to specify a ClassName (or if you don't, it defaults to `"MyJsonDataClass"`), like so:

```powershell
'{"name": "Bob", "count": 5, "amount_received": 12.34, "isFlag": true}' | ConvertTo-CSharpJsonClass -ClassName Person
```

Or, alternately:

```powershell
$myJsonString | ConvertTo-CSharpJsonClass -ClassName Person
```

Or:

```powershell
ConvertTo-CSharpJsonClass -Json $myJsonString -ClassName Person
```
Produces the following output:

```csharp
public class Person
{
    [JsonProperty("amount_received")]
    public decimal AmountReceived { get; set; }

    [JsonProperty("count")]
    public int Count { get; set; }

    [JsonProperty("isFlag")]
    public bool IsFlag { get; set; }

    [JsonProperty("name")]
    public string Name { get; set; }
}
```

If you don't want the `[JsonProperty]` attribute or you aren't using Json.NET, you can specify `-BasicMode` as a switch, 
and it will simply name the C# property the same as the JSON property with no alterations, like so:

```csharp
public class Person
{
    public decimal amount_received { get; set; }

    public int count { get; set; }

    public bool isFlag { get; set; }

    public string name { get; set; }
}
```

## Script

Here is the source to the PowerShell script:

```powershell
function ConvertTo-CSharpJsonClass
{
    [CmdletBinding()]
    param
    (
        [Parameter(ValueFromPipeline = $true, Mandatory = $true)]
        [string] $Json,
        [Parameter()]
        [string] $ClassName = 'MyJsonDataClass',
        [Parameter()]
        [switch] $BasicMode
    )
    process
    {
        "public class $ClassName`r`n{"

        if ($BasicMode)
        {
            $Json | ConvertFrom-Json | gm | select -Skip 4 | % { "    public $($_.Definition.Split(' ')[0]) $($_.Name) { get; set; }`r`n" }
        }
        else
        {
            $Json | ConvertFrom-Json | gm | select -Skip 4 | % {
                $formattedName = [System.Text.RegularExpressions.Regex]::Replace($_.Name.Replace("_", " "), '([A-Z])', ' $1').Trim()
                $properName = [System.Threading.Thread]::CurrentThread.CurrentCulture.TextInfo.ToTitleCase($formattedName).Replace(" ", "");
                "    [JsonProperty(`"$($_.Name)`")]`r`n    public $($_.Definition.Split(' ')[0]) $($properName) { get; set; }`r`n"
            }
        }

        "}"
    }
}
```
