---
title: Nancy and the issue with Enums as strings
date: 2015-12-18 11:00:12.000000000 +01:00
tags:
  - ".Net 4.5"
  - Nancy
  - Resolution
permalink: "/2015/12/18/nancy-and-the-issue-with-enums-as-strings/"
---

![enum_as_string]({{ site.baseurl }}/assets/images/2015/12/enum_as_string-300x254.png)

## Bakground

The other day I was trying to show an enum property as a string in an Nancy based API.

And I just couldn't do it at first but later on I found a way. Just so I remember until next time I thought I'd write a step by step instruction.

## The initial Code

I've created an Empty ASP.Net project and added Nancy.Hosting.Aspnet Nuget package with the following command:

```bash
Install-Package Nancy.Hosting.Aspnet
```

In this project I've created a very simple NancyModule that returns a list of a class called TeamMember. Each TeamMember has a Name and an Avatar property that is an enum.

```csharp
public class Index : NancyModule
{
  public Index()
  {
    Get["/"] = _ =>
    {
      var teamMembers = new List()
      {
        new TeamMember { Name = "Marcus", Avatar = StarWarsCharacter.Luke },
        new TeamMember { Name = "Hugo", Avatar = StarWarsCharacter.Chewbacca }
      };

      return Response.AsJson(teamMembers);
    };
  }
}
```

Above shows my very basic NancyModule. And the code below shows the TeamMember class and the enum called StarWarsCharacter.

```csharp
public class TeamMember
{
  public string Name { get; set; }
  public StarWarsCharacter Avatar { get; set; }
}
```

```csharp
public enum StarWarsCharacter
{
  Leia,
  Luke,
  Chewbacca
}
```

Above the enum used in my example. This generates the output as shown below.

```json
[
  {
    "name": "Marcus",
    "avatar": 2
  },
  {
    "name": "Hugo",
    "avatar": 6
  }
]
```

As you can see the enum is represented by an integer in the json-output.

## First solution

If you do a google search for Enum as string then you probably will find people mentioning that you need to decorate your enum properties with the [JsonConverter(typeof(StringEnumConverter))] attribute:

```csharp
  [JsonConverter(typeof(StringEnumConverter))]
  public StarWarsCharacter Avatar { get; set; }
```

So now I can't build because I'm missing some references. I Install the Newtonsoft.Json Nuget package with this command:

```bash
Install-Package Newtonsoft.Json
```

and I hit the rebuild.

Still the json-output is representing the enum as an integer.

Why? I still haven't figured out that part yet but if you install the Nuget package called Nancy.Serialization.JsonNet with this command:

```bash
Install-Package Nancy.Serialization.JsonNet
```

Shabaam, presto!

```json
[
  {
    "Name": "Marcus",
    "Avatar": "Luke"
  },
  {
    "Name": "Hugo",
    "Avatar": "Chewbacca"
  }
]
```

## Next challange

This is awesome! But wait a minute, now my json properties aren't formatted as I like. The properties start with a capital letter, not like I want.

## Solution continued

Don't panic Hugo, you'll only need to add a CustomSerializer and a Bootstraper and then it will be alright.

Below is the CustomSerializer class.

```csharp
public class CustomSerializer : JsonSerializer
{
  public CustomSerializer()
  {
    ContractResolver = new CamelCasePropertyNamesContractResolver();
  }
}
```

And below you can see the BootStraper.

```csharp
public class BootStraper : DefaultNancyBootstrapper
{
  protected override void ConfigureApplicationContainer(TinyIoCContainer container)
  {
    base.ConfigureApplicationContainer(container);
    container.Register(typeof(JsonSerializer), typeof(CustomSerializer));
  }
}
```

## The final solution

For a final touch you can remove the attribute JsonConverter all together and configure everything in the CustomSerializer by adding a StringEnumConverter to the Converters.

```csharp
public class CustomSerializer : JsonSerializer
{
  public CustomSerializer()
  {
    ContractResolver = new CamelCasePropertyNamesContractResolver();
    Converters.Add(new StringEnumConverter
    {
      CamelCaseText = false
    });
  }
}
```

And lastly remove the attribute from the TeamMember class as all enums will be converted to strings as defined in the BootStraper above.

```csharp
public class TeamMember
{
  public string Name { get; set; }
  public StarWarsCharacter Avatar { get; set; }
}
```

## Conclusion

It's always easy when you know how to solve issues, so here goes the perfect recipe for solving the enum as string issue on Nancy.

The [code is as always here](https://github.com/hugohaggmark/HugoHaggmark.Blog/blob/master/HugoHaggmark.Nancy.Enums.As.Strings/Modules/Index.cs). You can find the different solutions as different branches.

Cheers,

Hugo
