---
layout: post
title: How to configure an automated test run with TFS 2010 and Test Manager
date: 2011-01-27 22:14:00.000000000 +01:00
tags:
  - Associated Automation
  - Step by Step
  - Team Build
  - Team Foundation Server
  - Test Manager
permalink: "/2011/01/27/how-to-configure-an-automated-test-run-with-tfs-2010-and-test-manager/"
---

Some of you may have read my earlier post on the associated automation in [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") and [Test Manager](http://msdn.microsoft.com/en-us/library/ms182409.aspx "Test Manager") [here]({{ site.baseurl }}{% link _posts/2011-01-08-how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager.md %} "how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager"). I got some comments that made me realize that more people could benefit from a complete step by step guide on this subject. As such this post will be very long but with a lot of pictures so I hope you stick with me.

### Environment

First of all you’ll need a TFS environment, I downloaded my environment from [Brian Keller](http://blogs.msdn.com/b/briankel/ "Brian Keller") [here](http://blogs.msdn.com/b/briankel/archive/2010/06/25/now-available-visual-studio-2010-rtm-virtual-machine-with-sample-data-and-hands-on-labs.aspx "now-available-visual-studio-2010-rtm-virtual-machine-with-sample-data-and-hands-on-labs") and all the screenshots you’ll see will be from Brian’s image.

### Test Controller

You’ll need to start a Test Controller and register team project collection controller like so:

![Test Controller]({{ site.baseurl }}/assets/images/2011/01/image_thumb.png "Test Controller")

### Configuring an automated Test Environment

To be able to run automated test runs we need to configure an automated test environment first. Startup your Test Manager and change to the Lab Center by clicking on the dropdown:

![Testing Center - Lab Center]({{ site.baseurl }}/assets/images/2011/01/image_thumb1.png "Testing Center - Lab Center")

In the Lab Center click on the Controllers tab marked in red:  
 ![Controllers Tab]({{ site.baseurl }}/assets/images/2011/01/image_thumb2.png "Controllers Tab")

Make sure that your Test Controller shows up and has the status Ready:  
 ![Controllers Tab 2]({{ site.baseurl }}/assets/images/2011/01/image_thumb3.png "Controllers Tab 2")

Next step is to return to the Lab Tab marked in red and configure an Environment:

![Lab Tab]({{ site.baseurl }}/assets/images/2011/01/image_thumb4.png "Lab Tab")

On the New dropdown choose “New physical environment” like such:

![New physical environment]({{ site.baseurl }}/assets/images/2011/01/image_thumb5.png "New physical environment")

Give your Environment a good name following your specific naming conventions and make sure that the Controller is correctly assigned. Click the button “Next” when you’re ready.

![New physical environment - Name and location]({{ site.baseurl }}/assets/images/2011/01/image_thumb6.png "New physical environment - Name and location")

On the “Machines” page click on your controller and then click on the button “Add to environment” marked in red.

![New physical environment - Add to environment]({{ site.baseurl }}/assets/images/2011/01/image_thumb7.png "New physical environment - Add to environment")

As you do that your machine will appear on the left side of the screen. To continue you need to give the machine a specific role to make it easier for people to understand the role of the machine. I gave the machine the “Server” role like the picture below. When you’re ready should be able to click the next button.

![New physical environment - Edit Role]({{ site.baseurl }}/assets/images/2011/01/image_thumb8.png "New physical environment - Edit Role")

On the “Machine Properties” page you could fill in more details regarding your machine but I simply skipped this step for now and left it clean.

![New physical environment - Machine Properties]({{ site.baseurl }}/assets/images/2011/01/image_thumb9.png "New physical environment - Machine Properties")

On the summary page I just clicked the Finished button and got on with next step.

![New physical environment - Summary]({{ site.baseurl }}/assets/images/2011/01/image_thumb10.png "New physical environment - Summary")

When you’re finished make sure you see a green ready symbol like so:

![Environments - Ready]({{ site.baseurl }}/assets/images/2011/01/image_thumb11.png "Environments - Ready")

Now we need to configure our automated test settings by clicking on the “Test Settings” tab marked in red below:

![Test Settings Tab]({{ site.baseurl }}/assets/images/2011/01/image_thumb12.png "Test Settings Tab")

Click the “New” button to create the automated test settings:

![Test Settings Manager - New]({{ site.baseurl }}/assets/images/2011/01/image_thumb13.png "Test Settings Manager - New")

Give the test settings a name, description and make sure that you choose automated in the selection section. Click the “Next” button when you’re finished.

![image]({{ site.baseurl }}/assets/images/2011/01/image_thumb14.png "image")

On the Roles page make sure you click the role you choose earlier when you created your environment, for me that would be the “Server” role, otherwise you will not be able to click the “Next” button. ![Automated Test Run - Roles]({{ site.baseurl }}/assets/images/2011/01/image_thumb15.png "Automated Test Run - Roles")

If you have several roles you’ll need to chose which one you’re going to use for your automated tests, In my case I didn’t need to change this as I have only one role.

![Automated Test Run - Select Roles]({{ site.baseurl }}/assets/images/2011/01/image_thumb16.png "Automated Test Run - Select Roles")

Click the “Next” button when you’re done.

On the “Data and Diagnostics” page configure anything you need and click the “Next” button, In my case I left all the defaults and clicked the “Next” button.

On the “Advanced” page you can configure even more advanced configurations, in my case I just clicked the “Next” button until the Summary page appeared.

![image]({{ site.baseurl }}/assets/images/2011/01/image_thumb17.png "image")

So click the “Finish” button and lets go on with this adventure. Your result should be another entry in your Test Settings list:

![Test Settings Manager - Automated]({{ site.baseurl }}/assets/images/2011/01/image_thumb18.png "Test Settings Manager - Automated")

### Turning the manual test into automated in Testing Center

Lets go back to the Testing Center by clicking the dropdown like so:

![Switching back to Testing Center]({{ site.baseurl }}/assets/images/2011/01/image_thumb19.png "Switching back to Testing Center")

Make sure you’re on the “Plan” tab and click the “Properties” link marked in red below. Here I’m assuming that you already have created a new suite, if you haven’t you should read this [post]({{ site.baseurl }}{% link _posts/2011-01-08-how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager.md %} "how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager").

![Properties]({{ site.baseurl }}/assets/images/2011/01/image_thumb20.png "Properties")

On the somewhat huge properties page (you can easily get overwhelmed with all that information but I’ll lead the way) you should make sure that you choose your test settings in the Automated runs section, in my case I chose “Automated Test Run” created earlier.

![Test settings - Automated Test Run]({{ site.baseurl }}/assets/images/2011/01/image_thumb21.png "Test settings - Automated Test Run")

Next you choose your Test environment, in my case I chose “Automated Test Environment” created earlier.

![Test environment - Automated Test Environment]({{ site.baseurl }}/assets/images/2011/01/image_thumb22.png "Test environment - Automated Test Environment")

Next you’ll have to assign a build for the automated tests. Assuming that you already created a build in this step otherwise you’ll need to create one. The section you’re looking for should look like so:

![Builds]({{ site.baseurl }}/assets/images/2011/01/image_thumb23.png "Builds")

That’s it now click the Save and Close button and lets test this baby…

Change to the “Test” tab and start your automated test cases with the “Run” button:

![Run Tests]({{ site.baseurl }}/assets/images/2011/01/image_thumb24.png "Run Tests")

If you’ve done your job right you should see the following very nice summary page:

![Test Run Summary]({{ site.baseurl }}/assets/images/2011/01/image_thumb25.png "Test Run Summary")

Hope this will help someone out there with this rather straightforward but long blog post.

Cheers!

Hugo
