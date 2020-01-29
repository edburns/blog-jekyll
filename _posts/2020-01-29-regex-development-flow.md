---
layout: post
title:  "Flow for developing regular expressions (regex) of moderate complexity"
date:   2020-01-29 10:40-0500
comments: true
---

I recently had to add a feature to the UI for the [Azure Marketplace
Offers for Oracle WebLogic
Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=weblogic%2012.2.1.3&page=1).
The UI for marketplace entries is written in JSON.  As I stated in [my
previous post](/blog{% post_url
2020-01-25-arm-development-faster-iteration-time %}), JSON is an awful
choice for a DSL for deploying things to Azure.  It is also an awful
choice for declaring a wizard-style UI, but at least the particular use case of [building the UI for an Azure Marketplace offer](https://docs.microsoft.com/en-us/azure/azure-resource-manager/managed-applications/create-uidefinition-overview) allows easy [incorporation of regular expressions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/managed-applications/microsoft-common-textbox#remarks).

This post shows a technique for iteratively developing a moderately
complex regular expression (regex).  

In my case, I want a regex that validates that a string is a comma
separated list of numbers or hyphen separated numbers.  The list must
have no whitespace and not have a trailing comma.  For example,
`80,443,7001,7002,8000-8100` is valid while
`80,443,7001,7002,8000-8100,` is not.

Spoiler: here is the final regex.

```
^((([0-9]+-[0-9]+)|([0-9]+))[,]?)+[^,]$
```

I developed this moderately complex regex using this process.

1. Use [https://regexr.com/](https://regexr.com/) to iteratively develop.

2. Write an English language statement of the regex: "I want a regex
that validates that a string is a comma separated list of numbers or
hyphen separated numbers.  The list must have no whitespace and not have
a trailing comma."

3. Decompose the English into its parts.

   a. no whitespace
   
   b. numbers
   
   c. hyphen separated numbers
   
   d. comma separated list
   
   e. not have a trailing comma
   
4. Write a regex for each part.

   a. `no whitespace`.  You get this for free with regex by not putting
      any whitespace in the regex.
      
   b. `numbers`. `^([0-9]+)$`
   
   c. `hyphen separated numbers`. `^([0-9]+-[0-9]+)$`
   
   d. `comma separated list`. `^((.*)[,]?)+$`
   
   e. `not have a trailing comma`. `^(.*)[^,]$`
   
5. Combine the parts in a way that works.  Here it was vitally important to make correct use of 
parenthesis ("capturing groups" in regex lingo), and the "or" operator `|`.

   Using the parts above, a pseudo regex looks like:
   
   ```
   ^(((c.)|(b.))d.)d.'e$
   ```

   I used "d." and "d.'" (d and d prime) in my pseudo regex because the
   comma separated list needed to be expressed that way.  Also note that
   "a." is met by the absence of any whitespace in the regex, so it is
   not present in the regex itself.
   
   Carefully making the necessary substitutions, the following
   unreadable mess is a regex that matches a bunch of number ranges or
   numbers, separated by commas, disallowing a trailing comma.

   ```
   ^((([0-9]+-[0-9]+)|([0-9]+))[,]?)+[^,]$
   ```
   
This expression successfully validates strings like `80,443,7001,7002,8000-8100`.

## Summary

Developing a moderately complex regular expression is easier when you break it down and iteratively develop each part using [https://regexr.com/](https://regexr.com/) by [gskinner](https://twitter.com/gskinner_team).

## Addendum

If you need your regexs as part of an azure `createUiDefinition.json`,
save time by testing the whole thing using the [Create UI Definition
Sandbox](https://portal.azure.com/?feature.customPortal=false#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).
