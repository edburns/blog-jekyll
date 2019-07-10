---
layout: post
title:  "Java EE in Azure Marketplace: What's Currently Available?"
date:   2019-07-07 12:00 -0400
comments: true
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
Microsoft will be doing **much** better than this very soon, but this post
documents how to get WLS 12.1.2 up and running on Azure as it stands
today.  This post will show how to do the following:

* Create a VM for WLS 12.1.2 from the existing Azure Marketplace
  template provided by Oracle.
  
* Expose the necessary network ports.

* SSH into the machine and create the WLS domain.

* Start the WLS server.

* Access the WLS console from the public Internet.

* Use the console to deploy a simple `.war` file.

* Validate the `.war` works.

* Shut down the server.

* De-allocate the Azure resources.

## Creating and Configuring the IaaS VM Within the Azure Portal
<!-- use H4 because it's smaller -->

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
to apply the filter.  ![Azure subscription filter]({{ site.url }}/blog/assets/20190707-subscription-filter.PNG "Azure subscription filter")
   
#### 3. Create a resource group

The left side of the Azure portal is a handy parking lot for actions
you can take.  It's kinda like the dock in macOS or the task bar in
Windows.  Let's create a "Resource Group".  You can think of a
"Resource Group" as a box for grouping related Azure things together.
In this post, we'll create a resource group to hold our WLS 12.1.2
related resources.  Click the "Resource Groups" icon:  

![Resource Group
Icon]({{ site.url }}/blog/assets/20190707-resource-group-icon.PNG
"Resource Group Icon")&nbsp;&nbsp;  

Then the "+ Add" button at the top.  Fill in "Resource group with a
descriptive name.  Such as "20190707-wls1212".  Leave "Subscription" and
"Region" as default, as shown here.

![Azure subscription filter]({{ site.url
}}/blog/assets/20190707-resource-group-pane.PNG "Azure subscription
filter")


Click "Review + Create", then "Create".  You will see a "Resource Group
Created" notification, which will quickly disappear.  You can get this
and all other notifications back by clicking on the "bell" icon, next to
the "Filter your subscription" icon you clicked on in step 2.  Do this
now and you'll see something like this.

![Resource Group Created]({{ site.url }}/blog/assets/20190707-resource-group-created.PNG "Resource Group Created")  

Click on the "Go to resource group".  Take some time to play around with
the resource group view.  As we create things, they will show up here.

#### 4. Create the WebLogic 12.1.2 Service

Click on the friendly green plus icon at the top of the left navigation
pane.  This will expose the "Azure Marketplace", a huge library of
things that run on Azure.  Eventually there will be more Java EE related
things in here, but for now place the cursor in the "Search the
Marketplace" box and type `Oracle WebLogic Server 12.1.2` and press
Enter.  

![wls1212]({{ site.url }}/blog/assets/20190707-wls-1212.PNG "wls1212")

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
This should only take a couple minutes to complete.

##### B. Tune the Network Settings To Allow SSH and Console Access

Click on "Resource Groups" then select the Resource group you created in
step 3.  This will show you the many different resources that were
created by the ARM template for WLS 12.1.2.

![wls-1212-resources]({{ site.url }}/blog/assets/20190707-wls-1212-resources.PNG "wls-1212-resources")

We are going to allow SSH and WLS Console access.  Securing an Internet
facing resource is a huge and important topic, but far beyond the scope
of this simple blog post.  Just know that we are doing some things here
that almost certainly are not security best practices.

Within the items in the Resource group, click on the "Network security
group", `20190707-wls1212-nsg`.  In the "Settings" section of the middle
pane, select "Inbound security rules".  

![Network Security Groups]({{ site.url }}/blog/assets/20190707-wls-1212-nsg.PNG "Network Security Groups")

This exposes the panel where we can customize the allowable network
connections **into** the VM (known as "inbound" connections).  There is
an analogous panel for configuring network connections **out** of the VM
("outbound" connections).  You declare whether or not a particular
connection is allowed or dis-allowed by writing "rules".  This exercise
is going to allow just enough to accomplish the goals of the blog post.
In the table below, rules can be added by clicking the "+ Add" button.
They can be edited by single clicking the row in the table.  When
adding or editing a rule make sure to scroll up and down to make sure
you haven't missed any fields.  Before you add any rules, let's examine
the pre-configured ones.  Note that the rows are sorted by increasing
priority.  When Azure detects an inbound connection to the VM, Azure
runs through each row of the inbound rules, in order of priority, to see
if the connection matches the rule.  If it does, the rule is applied and
traversal of the rule table stops.  If it does not, Azure keeps going
through the rows until either a match is found or the table ends.  Note
that Azure has pre-added a `DenyAllInbound` rule with the lowest
priority.  This effectively means "if nothing else, deny the inbound
connection".  Use the UI to make it so the rules look like the table
shown next.  The rows in the red box are the rows you need to add.

![Desired Inbound Rules]({{ site.url }}/blog/assets/20190707-wls-1212-nsg-modified.PNG "Desired Inbound Rules")

Digital Ocean has a great primer on networking terminology at
[https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking](https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking).

##### C. (Optional) Disable the Auto Shutdown

As a nod to the U.S. Government's National Institute of Standards
(NIST) offical definition of cloud computing (which [everyone should
read](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf))
your VM is pre-configured to auto shut-down at 19:00 UTC.  To disable
this potentially inconvenient setting, go to the Resource Group, click
on the "Virtual machine" row, then type "Auto-shutdown" in the search
box and click on the "Auto-shutdown" hit, as shown in this diagram.

![wls-1212-auto-shutdown]({{ site.url }}/blog/assets/20190707-wls-1212-auto-shutdown.PNG "wls-1212-auto-shutdown")

Toggle the enabled to "Off".  

#### 5. SSH Into the Machine to Complete the Configuration

Because we enabled port 22 [in step B
above](#b-tune-the-network-settings-to-allow-ssh-and-console-access), we
can SSH into the host, assuming we know it's IP address.  This can be
discovered by going to the Resource Group, clicking on the "Virtual
machine" resource.  If you mouse over the IP address, shown in the red
box next, you will see a button that copies the IP address to the
clipboard.

![IP Address]({{ site.url }}/blog/assets/20190707-wls-1212-ip-address.PNG "IP Address")

Open a "Cloud Shell" by clicking on the "Cloud Shell" icon on the top
menu bar of the panel.  

![Cloud Shell]({{ site.url }}/blog/assets/20190707-wls-1212-cloud-shell.PNG "Cloud Shell")

Tip: This action opens up a new shell at the bottom of the current
portal browser tab.  I prefer to have a full tab for the shell.  Click
on the "Open new session" button to get one.  You may close the bottom
shell if you like.  In the cloud shell, SSH into the machine as shown
next.

![Cloud Shell]({{ site.url }}/blog/assets/20190707-wls-1212-cloud-shell-02.PNG "Cloud Shell")

The remaining actions are documented in more detail [at
Oracle](https://docs.oracle.com/middleware/1212/wls/WLAZU/toc.htm), but
that documentation references a version of the Azure console that has
long since ceased to be.  Therefore, this section will update that
content for the simple case of a single WLS domain node.  After doing
these steps, it should be simple to use the out-of-date Oracle
documentation to perform the more advanced configuration, such as
[Creating a Multi-Machine Virtual Network for WebLogic
Server](https://docs.oracle.com/middleware/1212/wls/WLAZU/toc.htm#CBABAADH).

##### A. Set the ENV Vars

In the Azure Cloud Shell, ensure the `adminadmin` user owns the
necessary directories and files:

```
sudo chown -R adminadmin /opt/oracle
```

Enter `cat > wls-env.sh` then paste the following lines, followed by
control-D.

```
export ORACLE_HOME=/opt/oracle/products/Middleware
export WL_HOME=/opt/oracle/products/Middleware/wlserver
export JAVA_HOME=/opt/oracle/products/jdk1.7.0_25
${WL_HOME}/common/derby/bin/startNetworkServer.sh || true
```

The `|| true` causes the script to continue even if the Derby server
doesn't start, for example because it's already started.

Execute the script:

```
. ./wls-env.sh
```

This will set the necessary environment variables, and also start the
built-in Derby database.  This should cause someting like the following
to appear in the Azure Cloud Shell:

```
Mon Jul 08 15:06:33 EDT 2019 : Security manager installed using the Basic server security policy.
Mon Jul 08 15:06:34 EDT 2019 : Apache Derby Network Server - 10.8.2.2 -
(1181258) started and ready to accept connections on port 1527
```

Note that this port is not accessible to the public Internet because you
have not created an inbound rule for it.  It is, however, accessible
within the Virtual Network (vnet) created by the ARM template for Oracle
WLS 12.1.2.

##### B. Create the Domain

We will modify the included `basicWLSDomain.py` WLST script to make to
create a domain to which we can log in to the admin console.

```
cd /opt/oracle/products/Middleware/wlserver/common/templates/scripts/wlst
```

This directory contains some built-in WebLogic Scripting Template (WLST)
scripts provided by Oracle.  Copy the `basicWLSDomain.py` so we can
safely edit it.

```
cp basicWLSDomain.py 20190709_basicWLSDomain.py
```

Find the line with `# Please set password here before using this script,
e.g. cmo.setPassword('value')` and insert this line before it.

```
cmo.setPassword('3hu6Lfomu4#&')
```

Note, you can use any password you like.  I'm using the same password as
earlier for convenience.  Save the file.

Change to the directory containing the `wlst.sh` tool and execute it:

```
cd /opt/oracle/products/Middleware/oracle_common/common/bin
./wlst.sh
```

At the prompt, execute the script file you just edited:

```
wls:/offline> execfile('/opt/oracle/products/Middleware/wlserver/common/templates/scripts/wlst/20190709_basicWLSDomain.py')
```

This will cause
`/opt/oracle/products/Middleware/user_projects/domains/basicWLSDomain`
to be created.  Append the following line to your existing
`~/wls-env.sh` file:

```
nohup /opt/oracle/products/Middleware/user_projects/domains/basicWLSDomain/bin/startWebLogic.sh > ~/wls-log.txt & 2>&1
```

If you examine the contents of `~/wls-log.txt` you should see it ends
with output similar to the following.

```
<Jul 9, 2019 11:28:54 AM EDT> <Notice> <Security> <BEA-090171> <Loading the identity certificate and private key stored under the alias DemoIdentity from the jks keystore file /opt/oracle/products/Middleware/user_projects/domains/basicWLSDomain/security/DemoIdentity.jks.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Security> <BEA-090169> <Loading trusted certificates from the jks keystore file /opt/oracle/products/Middleware/wlserver/server/lib/DemoTrust.jks.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Security> <BEA-090169> <Loading trusted certificates from the jks keystore file /opt/oracle/products/jdk1.7.0_25/jre/lib/security/cacerts.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "DefaultSecure[1]" is now listening on fe80:0:0:0:20d:3aff:fe54:2fff:7002 for protocols iiops, t3s, ldaps, https.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "DefaultSecure[3]" is now listening on 127.0.0.1:7002 for protocolsiiops, t3s, ldaps, https.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "Default[1]" is now listening on fe80:0:0:0:20d:3aff:fe54:2fff:7001for protocols iiop, t3, ldap, snmp, http.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "DefaultSecure[2]" is now listening on 0:0:0:0:0:0:0:1:7002 for protocols iiops, t3s, ldaps, https.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "Default" is now listening on 10.0.2.4:7001 for protocols iiop, t3,ldap, snmp, http.>
<Jul 9, 2019 11:28:56 AM EDT> <Warning> <Server> <BEA-002611> <The hostname "localhost", maps to multiple IP addresses: 127.0.0.1, 0:0:0:0:0:0:0:1.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "DefaultSecure" is now listening on 10.0.2.4:7002 for protocols iiops, t3s, ldaps, https.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "Default[3]" is now listening on 127.0.0.1:7001 for protocols iiop,t3, ldap, snmp, http.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <Server> <BEA-002613> <Channel "Default[2]" is now listening on 0:0:0:0:0:0:0:1:7001 for protocolsiiop, t3, ldap, snmp, http.>
<Jul 9, 2019 11:28:56 AM EDT> <Notice> <WebLogicServer> <BEA-000331> <Started the WebLogic Server Administration Server "AdminServer" fordomain "basicWLSDomain" running in development mode.>
<Jul 9, 2019 11:28:57 AM EDT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to RUNNING.>
<Jul 9, 2019 11:28:57 AM EDT> <Notice> <WebLogicServer> <BEA-000360> <The server started in RUNNING mode.>
```

## Access the Admin GUI to deploy a Java EE 6 WAR.

Assuming you see the all important `The server started in RUNNING
mode.`, you may safely exit the SSH shell, and, if desired, disable the
inbound rule for SSH.

Obtain the public IP address for the host as shown
[earlier](5-ssh-into-the-machine-to-complete-the-configuration) and log
in to `http://<ip address>:7001/console`.

If everything worked correctly, you should see the following login UI.
I have filled in `weblogic` as the `Username`, since this is what the
template uses.  Provide the pasword you used when you ran `execfile` and
log in to the console.

![WLS Console]({{ site.url }}/blog/assets/20190707-wls-1212-console.PNG "WLS Console")

#### 1. Install the simplest WAR that will possibly work.

For simplicity, let's install the simple guessNumber 2.0 war into the
running WLS 12.1.2.  You can download the war [from this blog]({{ site.url }}/blog/assets/jsf-guessNumber-2.0.war).  

The following steps will guide you to install the guessNumber war using
the WLS Admin GUI.

* Click on "Deployments" in the "Domain Structure" panel on the left.

* Click on "Install" as shown next.

![WLS Console]({{ site.url }}/blog/assets/20190707-wls-1212-console-install-01.PNG "WLS Console")

* Click on the hyperlink "upload your file(s)" and upload the
  `jsf-guessNumber-2.0.war`.  Click "Browse" and use the file chooser to
  select the WAR.
  
* Click "Next".

* On the "Install Application Assistant", ensure the
  "jsf-guessNumber-2.0.war" is selected, and click "Next".
  
* On the "Choose targeting style" page, select "Install this deployment as an
  application" and click "Next".
  
* On the "Optional Settings" page, click "Finish".

Your console should have the following appearance, especially the green
text in the "Messages" section.

![WLS Console]({{ site.url }}/blog/assets/20190707-wls-1212-console-install-02.PNG "WLS Console")

Note that we are installing the WAR into the `AdminServer` target.  This
is not something one would normally do, but because the `basicWLSDomain`
is set up with just the one server by default, this is what we will use.

#### 2. View the Deployed App

Construct a URL based on the public IP and the context-root of the app.
For example, `http://<ip address>:7001/jsf-guessNumber-2.0/`.  Note that
the port is `7001`, not 8080.  This is because we installed the app in
the `AdminServer` target.  If the deployment was successful, you should
see the simple GuessNumber JSF 2.0 app.

#### 3. Shut Down the WLS Server

* In the "Domain Structure" browser, expand "Environment" and click "Servers".

* In the "Summary of Servers" panel, click the "Control" tab.

* Click the checkbox next to "AdminServer(admin)".

* Pull down the "Shutdown" dropdown and select "when work completes".

* Thiss will shut down the WLS server.

#### 4. Shut down the VM

* Back in the Azure portal click "Resource groups" and select the
  Resource group you created to hold this demo.
  
* Click on the "Virtual machine" type row.

* Click "Stop".

* Click "OK".

##### 5. (Optional) Delete the Resource group.

If you don't need this server any more, or you want to start over and
try some other experiments, you can delete the entire Resource group
with one action.

* In the Azure console, click on Resource groups.

* Check the checkbox next to your Resource group, and click the ellipsis
  at the right end of the row.
  
* Choose "Delete Resource group".

* As a measure to prevent accidental deletion, you have to type the
  Resource group name, then click "Delete"

This will delete the VM, network, network rules, disk, and the resource
group itself.

## Summary

Even though this is very old software, it's running on the most modern
and flexible cloud environment in the world.  In this post we did the
following.

* Create a VM for WLS 12.1.2 from the existing Azure Marketplace
  template provided by Oracle.
  
* Expose the necessary network ports.

* SSH into the machine and create the WLS domain.

* Start the WLS server.

* Access the WLS console from the public Internet.

* Use the console to deploy a simple `.war` file.

* Validate the `.war` works.

* Shut down the server.

* De-allocate the Azure resources.

Watch this space, and the twitters account
[@edburns](https://twitter.com/edburns/) and
[@reza_rahman](https://twitter.com/reza_rahman), for upcoming
announcments about Microsoft's plans for Java EE on Azure.
