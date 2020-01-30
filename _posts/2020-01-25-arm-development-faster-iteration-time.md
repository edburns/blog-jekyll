---
layout: post
title:  "Tips for faster iteration times developing ARM templates"
date:   2020-01-25 14:40-0500
comments: true
---

## UPDATE: 2020-01-30 export with https://resources.azure.com/

If you found your way to this post and your first reaction is 
"[well duh](https://www.urbandictionary.com/define.php?term=Thank%20you%20Captain%20Obvious)",
please leave a comment, because I'd love to know how you came upon your
knowledge which caused you to respond this way.  This was hard-won knowledge
for me, but I suspect I'm missing out on some fundamental bit of
training in the art of efficient ARM template development.

An ARM template encodes instructions that the [Azure Resource
Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/)
executes which result in some stuff being deployed to Azure.  As such,
an ARM template is a
[DSL](https://en.wikipedia.org/wiki/Domain-specific_language) for the
domain of Azure.  Unfortunately, as DSLs go, it's awful.  It's a bigass
JSON file, with all of the pitfalls that entails, such as:

* It's a challenge to manage complexity.  Things can spiral out of
  control very quickly unless you are very disciplined while authoring.
  
* The instructions in an ARM template execute over time, in a sequence,
  imperatively, but JSON is absolutely not an imperative syntax.
  
* It's very easy to make stupid mistakes at authoring time.  You can
  opt-in to using [some
  tooling](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/use-vs-code-to-create-template)
  to catch some stupid mistakes at authoring time, but a proper DSL
  would do a better job of it.
  
* You [can't even put comments in there](https://github.com/Azure/azure-resource-manager-schemas/issues/851).

As a DSL for Azure, I think Terraform may be a better fit.  Indeed
[Terraform is well supported by
Azure](https://docs.microsoft.com/en-us/azure/terraform/terraform-overview),
but you can't use it when authoring for the Azure marketplace.  Accept
JSON and move on.  Here are some tips to ease your acceptance.

## Accept JSON

This blog post shares some tips I've developed to get faster development
cycle times when iteratively developing ARM templates.  The cycle looks
like this:

1. Get your `mainTemplate.json` somehow.  Write it yourself, export it
   from the Azure portal, start with a quickstart, etc.
   
2. Deploy it to Azure with the Azure CLI.

3. Look at what happened.

I want to minimize the time between steps 2 and 3.

## Tips for step 1

### Use the "export template" feature as a jump start

Create the system you want to have in the portal, then use the 
[export template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/export-template-portal) 
feature of the portal to download the ARM template JSON file.  You may
want to do this for each big chunk of functionality in your system, then
stitch them together.  Iterate at the command line using the technique
in the next tip.

### Use the resources.azure.com

I have observed that the "export template" feature does not always give
you the full JSON.  For example, in the case of Application Gateway, the
`backendAddresses` was empty, even though it had a pool and targets.  A
colleague pointed out this enormously useful resource
[https://resources.azure.com/](https://resources.azure.com/).

Here is a quick sketch of how I used it to get the full JSON of a
deployed Application Gateway.  Note, this is not an ARM template, but it
can help a lot.

1. Visit https://resources.azure.com/

2. Type in the resource group in which the resource is deployed: ejb012803c

3. Maybe make a choice. 

   a. If offered with an autosuggest, pick the resource and you are done.
   
   b. If not offered with an autosuggest.  In the left pane, expand nodes like this
   
   ```
   subscriptions > Your subscription > resourceGroups > ejb012803c > Providers >
     Microsoft.Network > applicationGateways > ejb012803c
   ```

### Start with one of the templates from `azure-quickstart-templates`

My extremely helpful colleague [Brian
Moore](https://github.com/bmoore-msft) leads a team that curates this
[vast resource of
templates](https://github.com/Azure/azure-quickstart-templates), and
[tests for
templates](https://github.com/Azure/azure-quickstart-templates/tree/master/test/arm-ttk).
The templates are organized by the kind of thing you want to deploy to
Azure.  For example, here is one for [Azure Application
Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf). 

## Tips for step 2

### Always use the `--debug` argument to commands for which you need to iterate.  Like this

```
az group deployment create --debug --resource-group ejb012405c --parameters @20200124-dd-1052162-gateway-parameters.json --template-file arm-oraclelinux-wls-cluster/arm-oraclelinux-wls-cluster/src/main/arm/mainTemplate.json > azure.out 2>&1 &
```

This also causes the output to be redirected to a file, in the
background, capturing stdout and stderr.

### A tale of two tails

Use some tails to observe the output as the command runs.


1. Grep through the outfile for some low frequency string.

   ```
   tail -f azure.out | grep "INFO: Accepted"
   ```

2. In a separate terminal window, just tail the output for everything,
   so you can see if the command simply failed.

   ```
   tail -f azure.out
   ```
   
### Extract the `{"properties": {"template": ` JSON object from `azure.out`, so you can interpret column offsets correctly

The template you pass to `az group deployment create` will have all
formatting removed before Azure sees it, so when Azure gives you error
messages, they are usually expressed like "error on line 1 column
60299".  You need to translate that to "line N" in your human readable
JSON.  If you save aside the `{"properties": {"template": ` JSON object
from the `azure.out` file, you can seek to that column, and then derive
where the error is in your human readable JSON file.

### Even though you ultimately need to produce `mainTemplate.json` iterate with smaller, disjoint files

If you are building a template that ultimately deploys an entire
datacenter, such as with an [Azure
Application](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-solution-templates)
you should consider breaking the template into smaller nested templates,
as described in [Using linked and nested templates when deploying Azure
resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/linked-templates)

## Summary

Developing ARM templates can be very frustrating, especilaly if you are
used to the rapid compile-edit-debug cycle that many actual programming
languages offer.  
