---
layout: post
title: My very own Taskmanager in HTML 5, SignalR and Nancy
date: 2013-12-19 16:22:31.000000000 +01:00
categories:
  - ".Net 4.5"
  - AngularJS
  - HTML 5
  - Nancy
  - SignalR
tags:
  - ".Net 4.5"
  - AngularJS
  - HTML 5
  - Nancy
  - SignalR
permalink: "/2013/12/19/my-own-taskmanager-in-html-5-signalr-nancy/"
---

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb14.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image14.png)

Last week I had the crazy idea to create my own Single Page Application (SPA) HTML page that could show the Total CPU on my machine much like the traditional Taskmanger in Windows. I went through a couple of different blogs to get started with like this excellent one [Tutorial: Server Broadcast with SignalR 2.0](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/tutorial-server-broadcast-with-signalr-20).

I knew I wanted to use:

- HTML 5
- some simple and nice looking chart JavaScript library
- SignalR, to push notifications to my client without refreshing the webpage
- Nancy, the simplest way to create an API that I could ask for current CPU utilization
- AngularJS, seems like a very cool JavaScript library that I wanted to learn more of
- Visual Studio 2013

The end result I was aiming for was something like in the picture below:

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image.png)

### The Web Application

First of all I created a new ASP.NET Web Application project.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb1.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image1.png)

I choose the Empty template because I didn’t need any unnecessary files clogging my solution.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb2.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image2.png)

Next thing I did was to set the Web Application port to 8080 so that I know how to call my Nancy api later on.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb3.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image3.png)

&nbsp;

### In with SignalR

To get started with SignalR in Visual Studio 2013 you just add a SignalR Hub Class to your project.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb4.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image4.png)

This will immediately pull down and install A LOT of NuGet packages as you can see below…

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb5.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image5.png)

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb6.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image6.png)

and some helpful JavaScript files are downloaded and neatly placed in the Scripts folder, thanks! There’s also a hub class created that we’ll look at in a while.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb7.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image7.png)

Next I added a OWIN Startup class.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb8.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image8.png)

And then I added the usual app.MapSignalR(); statement and went on to the next task.

[csharp highlight="12"]using Microsoft.Owin;  
using Owin;

[assembly: OwinStartup(typeof(HugoHaggmark.Taskmanager.Startup))]

namespace HugoHaggmark.Taskmanager  
{  
 public class Startup  
 {  
 public void Configuration(IAppBuilder app)  
 {  
 app.MapSignalR();  
 }  
 }  
}[/csharp]

### The CPU API with Nancy

The fantastic people behind [Nancy](http://nancyfx.org/) had already made sure there was an [OWIN](http://owin.org/) package available for me so using Install-Package Nancy.Owin I pulled down some more awesome code.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb9.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image9.png)

Now I’m using my Nancy module in the same Web Application, but that’s not necessary you could call an existing Nancy module somewhere else but in the purpose of keeping it simple and clean I use the same Web Application here. Add the UseNancy() statement in the startup class to host Nancy in OWIN and let’s move on.

[csharp 1="Microsoft.Owin;" 2="using" 3="Owin;" 4="[assembly:" 5="OwinStartup(typeof(HugoHaggmark.Taskmanager.Startup))" language="14"]

namespace HugoHaggmark.Taskmanager  
{  
 public class Startup  
 {  
 public void Configuration(IAppBuilder app)  
 {  
 app  
 .MapSignalR()  
 .UseNancy();  
 }  
 }  
}  
[/csharp]

Next step is to add the actual Nancy module which is a plain class that inherits from NancyModule.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb10.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image10.png)

```csharp; highlight: [12, 14, 31]
using Nancy; using System; using System.Diagnostics; using System.Threading; namespace HugoHaggmark.Taskmanager.Modules { public class CpuModule : NancyModule { PerformanceCounter cpuCounter; public CpuModule() : base("api/cpu") { InitializePerformanceCounter(); Get["/"] = x =\> { int cpu = (int)Math.Ceiling(cpuCounter.NextValue()); return Response.AsText(cpu.ToString()); }; } private void InitializePerformanceCounter() { cpuCounter = new PerformanceCounter(); cpuCounter.CategoryName = "Processor"; cpuCounter.CounterName = "% Processor Time"; cpuCounter.InstanceName = "\_Total"; cpuCounter.NextValue(); Thread.Sleep(1000); } } }
```

Let’s go through this piece of code. Firstly the constructor tells Nancy that it will respond to requests to “api/cpu”. Next thing is to initialize a performance counter for Total CPU and then sleep for a second which according to a lot of blog posts out there is crucial or the NextValue() method will return 0;

So now we have a very lightweight and simple api that will return the total CPU on a machine.

### The Broadcasting class

The next thing I wanted to create was a class that broadcasts to all connected clients the current total CPU. So from the the Tutorial post mentioned earlier I found a way to do this. Add a simple class.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb11.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image11.png)

As I’m going to call the api in Nancy I want to use the HttpClient that is found in the System.Net.Http assemblies so I added those.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb12.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image12.png)

Then I just added the example from the Tutorial mentioned above and refactored the code to suite my needs.

```csharp; highlight: [44, 47]
using HugoHaggmark.Taskmanager.Hubs; using Microsoft.AspNet.SignalR; using Microsoft.AspNet.SignalR.Hubs; using System; using System.Net.Http; using System.Threading; namespace HugoHaggmark.Taskmanager { public class Broadcaster { private readonly static Lazy\<Broadcaster\> instance = new Lazy\<Broadcaster\>(() =\> new Broadcaster(GlobalHost.ConnectionManager.GetHubContext\<CpuHub\>().Clients) ); private readonly TimeSpan updateInterval = TimeSpan.FromMilliseconds(500); private readonly Timer timer; private Broadcaster(IHubConnectionContext clients) { Clients = clients; timer = new Timer(BroadcastCpuUsage, null, updateInterval, updateInterval); } public static Broadcaster Instance { get { return instance.Value; } } private IHubConnectionContext Clients { get; set; } private void BroadcastCpuUsage(object state) { string cpu = GetCurrentCpu(); Clients.All.cpuInfo(Environment.MachineName, cpu.ToString()); } private string GetCurrentCpu() { string currentCpu = "0"; HttpClient client = new HttpClient(); client.BaseAddress = new Uri("http://localhost:8080"); var response = client.GetAsync("api/cpu").Result; if (response.IsSuccessStatusCode) { currentCpu = response.Content.ReadAsStringAsync().Result.ToString(); } return currentCpu; } } }
```

Code specific to my Broadcaster implementation is the GetCurrentCpu method that calls our Nancy api and broadcasts the result to all connected clients. All other code is ripped from the SignalR tutorial mentioned earlier.

### Returning to the Hub

Our Hub will be really simple to implement at this point.

```csharp
using Microsoft.AspNet.SignalR; namespace HugoHaggmark.Taskmanager.Hubs { public class CpuHub : Hub { private readonly Broadcaster broadCaster; public CpuHub() : this(Broadcaster.Instance) { } public CpuHub(Broadcaster broadCaster) { this.broadCaster = broadCaster; } } }
```

The only thing we need to do is to instantiate the Broadcaster Singleton and of we go!

### Putting it all together in the HTML

Create a simple HTML Page.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb13.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image13.png)

I found this awesome JavaScript library for Charts called [ChartJS](http://www.chartjs.org/) that adds awesome chart capabilities to your HTML. So start of with downloading and adding every JavaScript file to the body like below.

[xml highlight="7, 9, 10, 11, 12"]\<!DOCTYPE html\>  
\<html xmlns="http://www.w3.org/1999/xhtml"\>  
\<head\>  
 \<title\>Taskmanager\</title\>  
\</head\>  
\<body\>  
 \<canvas id="canvas" height="450" width="600"\>\</canvas\>

\<script src="Scripts/jquery-1.10.2.min.js"\>\</script\>  
 \<script src="Scripts/jquery.signalR-2.0.0.min.js"\>\</script\>  
 \<script src="Scripts/chart.min.js"\>\</script\>  
 \<script src="/signalr/hubs"\>\</script\>[/xml]

And for the chart we need a canvas so I went ahead and created one as well.

Next off is to add the mandatory SignalR hub stuff in a inline JavaScript.

[xml highlight="2, 4, 8"]\<script type="text/javascript"\>  
 var cpuHub = $.connection.cpuHub;

cpuHub.client.cpuInfo = function (machineName, cpu) {  
 \<!-- Insert stuff here to do when server broadcasts --\>  
 }

$.connection.hub.start();  
\</script\>[/xml]

There is 2 very important things to note with the JavaScript above:

1. Firstly the <font face="Courier New"><strong>$.connection.cpuHub </strong><font face="Calibri">statement must match the name of the hub class you’re connecting to</font></font>
2. In the Broadcaster class we’re calling&nbsp; **Clients.All.cpuInfo(Environment.MachineName, cpu.ToString());** so we need to register the same method in our client like the **<font face="Courier New">cpuHub.client.cpuInfo</font>** <font face="Calibri">statement above does.</font>

<font face="Calibri">The rest is boilerplate code to make the chart work and look nicely and you can find all the code <a href="https://github.com/hugohaggmark/HugoHaggmark.Blog" target="_blank">here</a>.</font>

<font face="Calibri">I didn’t have to time yet to finish of the Angular part of this but I’m sure I’ll write another post soon about it.</font>

<font face="Calibri">Hope you enjoyed it,</font>

<font face="Calibri">Hugo</font>
