---
layout: post
title:  Azure DevOps cross organization daily task wrangling
date:   2023-11-24 12:10+0800
comments: true
---

## Problem statement

My employer has standardized on Azure DevOps Boards as the issue tracking system of record. I have become fond of this system and immensely respect its capabilities.  Due to my role, I end up with a very large number of work items assigned to me across a very diverse number of different Azure DevOps organizations. While an extension exists to support [Cross Account Query](https://marketplace.visualstudio.com/items?itemName=ms-eswm.crossaccountquery&targetId=5a766efb-e568-41d7-a401-fc09ace9b116&utm_source=vstsproduct&utm_medium=ExtHubManageList), it has a significant and insurmountable limitation that the accounts being queried must have a uniform "schema" for Iteration Path and Area Path. This limitation is a fact of life in my role.

This blog post shows my workaround that gives me the ability:

- To see a comprehensive dashboard across the many different Azure DevOps organizations that contain work items assigned to me, so I can know which ones I need to work on in any given day.
- To check them off task by task when I have done all I intend to do on that particular task today.
- To reset the "done for today" status so I get a fresh dashboard the next day.

It is not very elegant, but works for me. I'm open to suggestions for improvements. Please leave a comment if you have any ideas.

## Solution

1. For each Azure DevOps organization, write and save a query that shows the work items assigned to me.
   Here are a screen grabs of two such queries.
   
   <img src="{{ site.url }}/blog/assets/20231124-dd-query.png" alt="Azure DevOps query in the dd board" />
   
   <img src="{{ site.url }}/blog/assets/20231124-em-query.png" alt="Azure DevOps query in the em board" />
   
   Some things to note about these queries:
   
   - The **Area Path** values vary and are specific to the organization.
   - The **Iteration Path** values vary, but both have **@CurrentIteration**.
   - The **State** values vary and are specific to the organization.
   - They both have a funny **Tags** value: **Does Not Contain** `edburns_done-for-today`. This tag is obviously named.

1. Write an anologous set of queries, but for the last clause the **Tags** value is  **Contains** `edburns_done-for-today`.

2. Use the Daily Links chrome extension to cause all the saved queries in a new browser tab. Install the Daily Links extension from the [Chrome web store](https://chrome.google.com/webstore/detail/bpmiffipgillbacdfnbkicndliejockp). This also works in Edge.

   <img src="{{ site.url }}/blog/assets/20231124-daily-links.png" alt="Azure DevOps queries loaded by Daily Links extension" />
   
## Daily wrangling
   
1. Start of day process.

   1. Select the **Daily Links** button in the browser.
   
   1. For each tab:
   
      1. Decide which of the tasks you will **NOT*** be able to work on today.
      
      1. Apply the `edburns_done-for-today` tag to those issues.
      
      1. Re-run the query.
      
1. End of day process.

   1. Select the **Daily Links** button in the browser.
   
   1. For each tab:
   
      1. Run the analogous "Contains done for today" query for that tab.
      
      1. Use the multi-issue edit feature to remove that tag.
      
      
## Problems with this solution

- You have to write 2N queries.

- You pollute the tagspace with information that is only relevant to you.

- The act of resetting the "done for today" tag is high effort. I suspect there is a way to make a batch process that just does this with one action, but I have not invested in learning how.

- The quality of the queries regarding their ability to convey the context of each task is up to the query author. In my implementation, I simply include **Parent** next te **Title** in the **Column options**.
