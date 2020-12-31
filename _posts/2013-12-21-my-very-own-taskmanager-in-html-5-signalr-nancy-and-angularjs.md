---
layout: post
title: My very own Taskmanager in HTML 5, SignalR, Nancy and AngularJS
date: 2013-12-21 17:10:15.000000000 +01:00
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
permalink: "/2013/12/21/my-very-own-taskmanager-in-html-5-signalr-nancy-and-angularjs/"
---

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb15.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image15.png)

> This is part 2 of this blog post [here](http://www.hugohaggmark.com/2013/12/19/my-own-taskmanager-in-html-5-signalr-nancy/) and I really encourage you to read it before continuing reading this blog post.

Anyway the last thing to add to my small Taskmanager was AngularJS. I started to think about how to get SignalR and AngularJS working together and I got some real good pointers from the post “[A Better Way of Using ASP.NET SignalR With Angular JS](http://sravi-kiran.blogspot.se/2013/09/ABetterWayOfUsingAspNetSignalRWithAngularJs.html)”.

I had another challenge with my existing code like the following statement in the JavaScript:

**var chart = new Chart(document.getElementById("canvas").getContext("2d")).Line(lineChartData, options);**

I could just move this code into my AngularJS controller but that seemed very ugly. So I found someone that already wrapped [ChartJS](http://www.chartjs.org/) in [AngularJS](http://angularjs.org/) directives into a small JavaScript library called [Angles](https://github.com/lgsilver/angles). So now when I was done reading others blog posts I felt ready to move on.

###

### The AngularJS magic

Firstly I just pulled down AngularJS.Core and bootstrap NuGet packages.

- Install-Package AngularJS.Core
- Install-Package bootstrap

Then I added my own app.js JavaScript file that would contain my small application.

[![image]({{ site.baseurl }}/assets/images/2013/12/image_thumb16.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2013/12/image16.png)

So far everything is pretty straightforward don’t you think?

### Creating the SignalR connection in AngularJS

From the the post mentioned earlier I concluded that I needed to create a AngularJS service to hook up all my SignalR magic. The reason for this is that AngularJS services are singletons and that sounds perfect for this implementation.

[csharp highlight="1,2,7"]var taskManagerApp = angular.module('taskManagerApp', ["angles"])  
 .service('signalRSvc', function ($rootScope) {  
 var initialize = function () {  
 var cpuHub = $.connection.cpuHub;

cpuHub.client.cpuInfo = function (machineName, cpu) {  
 $rootScope.$emit("cpuInfo", machineName, cpu);  
 }

$.connection.hub.start();  
 };

return {  
 initialize: initialize  
 };  
 })  
[/csharp]

Some important points in the code snippet above:

- [“angles”], tells my AngularJS app to use the Angles library
- .service, tells my AngularJS app that the next thing is a service named ‘signalRSvc’
- the $rootScope.$emit, tells my AngularJS app to broadcast the current machineName and cpu to my controller.

### The AngularJS Controller

Lets move on to the Controller code, this is where we control our view which is my index.html.

[csharp highlight="1,73,75,89"].controller('ChartController', function ($scope, signalRSvc, $rootScope) {  
 $scope.machineName = "localhost";  
 $scope.cpuChartLabel = "Total % Processor Time";  
 $scope.lineChartData = {  
 labels: [""],  
 datasets: [  
 {  
 fillColor: "rgba(241,246,250,0.5)",  
 strokeColor: "rgba(17,125,187,1)",  
 pointColor: "rgba(17,125,187,1)",  
 pointStrokeColor: "#fff",  
 data: [0]  
 }  
 ]  
 };

$scope.options = {

//Boolean - If we show the scale above the chart data  
 scaleOverlay: false,

//Boolean - If we want to override with a hard coded scale  
 scaleOverride: true,

//\*\* Required if scaleOverride is true \*\*  
 //Number - The number of steps in a hard coded scale  
 scaleSteps: 10,  
 //Number - The value jump in the hard coded scale  
 scaleStepWidth: 10,  
 //Number - The scale starting value  
 scaleStartValue: 0,

//String - Colour of the scale line  
 scaleLineColor: "rgba(0,0,0,.1)",

//Number - Pixel width of the scale line  
 scaleLineWidth: 1,

//Boolean - Whether to show labels on the scale  
 scaleShowLabels: true,

//Interpolated JS string - can access value  
 scaleLabel: "\<%=value%\>",

//String - Scale label font declaration for the scale label  
 scaleFontFamily: "'Arial'",

//Number - Scale label font size in pixels  
 scaleFontSize: 12,

//String - Scale label font weight style  
 scaleFontStyle: "normal",

//String - Scale label font colour  
 scaleFontColor: "#666",

///Boolean - Whether grid lines are shown across the chart  
 scaleShowGridLines: true,

//String - Colour of the grid lines  
 scaleGridLineColor: "rgba(0,0,0,.05)",

//Boolean - Whether the line is curved between points  
 bezierCurve: false,

//Boolean - Whether to show a dot for each point  
 pointDot: false,

//Boolean - Whether to animate the chart  
 animation: false,  
 };

signalRSvc.initialize();

var updateChartData = function (machineName, cpu) {  
 if ($scope.lineChartData.labels.length &gt; 20) {  
 $scope.lineChartData.labels.shift();  
 }

$scope.lineChartData.labels.push("");

if ($scope.lineChartData.datasets[0].data.length &gt; 20) {  
 $scope.lineChartData.datasets[0].data.shift();  
 }

$scope.lineChartData.datasets[0].data.push(cpu);  
 }

$scope.$parent.$on("cpuInfo", function (e, machineName, cpu) {  
 $scope.$apply(function () {  
 $scope.machineName = machineName;  
 updateChartData(machineName, cpu)  
 });  
 });  
 });  
[/csharp]

Some important points in the code snippet above:

- .controller, tells my AngularJS app that the next thing is a controller named ‘ChartController’ and it uses a function that takes our service signalRSvc as a parameter.
- signalRSvc.initialize();, tells my AngularJS app to initiate the SignalR connection.
- $scope.$parent.$on("cpuInfo", function (e, machineName, cpu), tells my AngularJS to listen to calls from “cpuInfo” and this will then call into the updateChartData

### The view binding it all together

Having done all the hard lifting from index.html to app.js file we end up with a very simple index.html like so

[xml highlight="7"] \<div class="container"\>  
 \<div ng-app="taskManagerApp"\>  
 \<div class="jumbotron" ng-controller="ChartController"\>  
 \<h1\>{{machineName}}\</h1\>  
 \<div class="span" %\>  
 \<h3\>{{cpuChartLabel}}\</h3\>  
 \<canvas id="lineChart" data="lineChartData" options="options" linechart\>\</canvas\>  
 \</div\>  
 \</div\>  
 \</div\>

\<!-- Placed at the end of the document so the pages load faster --\>  
 \<script src="Scripts/jquery-1.10.2.min.js"\>\</script\>  
 \<script src="Scripts/jquery.signalR-2.0.0.min.js"\>\</script\>  
 \<script src="Scripts/angular.min.js"\>\</script\>  
 \<script src="Scripts/bootstrap.min.js"\>\</script\>  
 \<script src="Scripts/chart.min.js"\>\</script\>  
 \<script src="Scripts/angles.js"\>\</script\>  
 \<script src="Scripts/app/app.js"\>\</script\>  
 \<script src="/signalr/hubs"\>\</script\>  
 \</div\>

[/xml]

I sure learned a lot from this very small SPA and I hope that some of you have too.

Cheers,

Hugo
