---
layout: post
title:  "Java EE in Azure Marketplace: What's Currently Available?"
date:   2019-07-07 12:00 -0400
comments: false
---

As mentioned in my [previous post]({{ site.url
}}/blog/2019/05/23/joining-microsoft.html), I joined Microsoft "to
enable Java EE developers on Azure."  The team I joined at Microsoft has
already been working on this goal for some time, so naturally I need to
take stock of the current offerings, whatever they may be.  As African
American tennis great Arthur Ashe said, "[Start where you are. Use what
you have. Do what you
can.](https://www.brainyquote.com/quotes/arthur_ashe_371527)" It turns
out there is an existing template in the [Azure
Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/)
for [Oracle WebLogic Server
12.1.2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Oracle.OracleWebLogicServer12cEnterprise?tab=Overview).
Now, this is admittedly ancient, supporting only Java EE 6 ([Servlet
3.0, JSF 2.1, CDI 1.0,
etc](https://docs.oracle.com/middleware/1212/wls/NOTES/whatsnew.htm#BGGGHCJD)).
We will be doing **much** better than this very soon, but this post
documents how to get WLS 12.1.2 up and running on Azure as it stands
today.

## Creating the IaaS VM Within the Azure Portal

#### 1. Log in to the portal 

Visit [portal.azure.com](https://portal.azure.com/) and either sign
up or log in.  This is your portal to all of the features of Azure to
which you have a subscription.  Everything you do within Azure
happens within a subscription.  If you are just getting started, you
can get a free trial subscription with [Visual Studio Dev
Essentials](https://visualstudio.microsoft.com/dev-essentials/).

#### 2. Filter to your subscription
   
It is possible to have multiple subscriptions within your Azure
account.  If your Azure account is associated with multiple
subscriptions, you'll need to adjust the portal so it only takes
action on resources within that subscription.  Use this this control
to apply the filter.  ![Azure subscription filter]({{ site.url }}/blog/assets/20190707-subscription-filter.png "Azure subscription filter")
   
#### 3. Create a resource group

The left side of the Azure portal is a handy parking lot for actions
you can take.  It's kinda like the dock in macOS or the task bar in
Windows.  Let's create a "Resource Group".  You can think of a
"Resource Group" as a box for grouping related Azure things together.
In this post, we'll create a resource group to hold our WLS 12.1.2
related resources.  Click the "Resource Groups" icon:  

![Resource Group
Icon]({{ site.url }}/blog/assets/20190707-resource-group-icon.png
"Resource Group Icon")&nbsp;&nbsp;  

Then the "+ Add" button at the top.  Fill in "Resource group with a
descriptive name.  Such as "20190707-wls1212".  Leave "Subscription" and
"Region" as default, as shown here.

![Azure subscription filter]({{ site.url
}}/blog/assets/20190707-resource-group-pane.png "Azure subscription
filter")


Click "Review + Create", then "Create".  You will see a "Resource Group
Created" notification, which will quickly disappear.  You can get this
and all other notifications back by clicking on the "bell" icon, next to
the "Filter your subscription" icon you clicked on in step 2.  Do this
now and you'll see something like this.

![Resource Group Created]({{ site.url }}/blog/assets/20190707-resource-group-created.png "Resource Group Created")  

Click on the "Go to resource group".  Take some time to play around with
the resource group view.  As we create things, they will show up here.

#### 4. Create the WebLogic 12.1.2 Service

Click on the friendly green plus icon at the top of the left navigation
pane.  This will expose the "Azure Marketplace", a huge library of
things that run on Azure.  Eventually there will be more Java EE related
things in here, but for now place the cursor in the "Search the
Marketplace" box and type `Oracle WebLogic Server 12.1.2` and press
Enter.  

![wls1212]({{ site.url }}/blog/assets/20190707-wls-1212.png "wls1212")

Under the covers, this is an Azure Resource Manager (ARM) template,
created by Oracle and inserted into the Azure Marketplace way back
in 2013.  You can create your own ARM templates.  Check out my colleague Rick Claus's [blog about ARM templates](https://regularitguy.com/2016/06/06/how-to-create-an-azure-resource-manager-arm-template-from-scratch/) for more.

##### A. Create a Virtual Machine

Fill in this form with the following values.

**Resource group**: `The resource group you created in step 3.`

**Virtual Machine Name**: `The same as the resource group.`

Within the `Administrator account` section, change the `Authentication
type` to be "Password".  Set the values as follows.

**Username**: `adminadmin`

**Password and Confirm Password**: `3hu6Lfomu4#&`

Naturally you can use whatever you like for password.

Within the `Inbound Port Rules` section, leave "None" selected, we will
tune the rules later.  

Click "Next: Disks >".  This is important because the default is to
create the VM with expensive premium SSD disk.  This post is
optimized for cheapness, not speed!

Set the "OS disk type" to be "Standard HDD".  Then click the "Review +
create", then "Create".  You will see "Your deployment is underway".

