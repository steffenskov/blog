---
title: 'IIS with static content occasionally gives 503 Server Fault on all requests'
layout: post
---

### The problem

Our content server occasionally responds 503 Server Fault on all requests for about a minute or so.

This seem to occur at random intervals for no apparent reason.

### Searching for the cause

The server is configured as described in [Tuning IIS 7 for static content](https://steffenskov.github.io/blog/2010/10/27/tuning-iis-7-for-static-content.html), which means it caches files pretty efficiently, so clearly it couldn’t be a performance issue.

After meddling with the number of TCP ports available, searching through the registry, checking every dialog in IIS management console, and looking through netsh, we finally found the cause:  
By default IIS will scan all folders in your site for web.config files, this means you can apply certain configurations only for a set of subfolders.

Quite handy normally, however on our content server we have at least 50.000 subfolders, so scanning all those folders apparently strains the server quite a bit.

Since we only have a web.config in our root folder, we can simply disable the subfolder scan without any negative consequences.

After disabling it we have had no more 503 responses.

### How to implement the solution

To change the web.config scan behaviour, you need to edit the `applicationhost.config”** file, which is found in `%windir%\\system32\\inetsrv\\config”**.  
You’ll need to run in elevated mode to access this folder if UAC is enabled. (which it really should be on any server IMHO)

1. In the file search for **&lt;sites&gt;** (the file is an XML).
2. Under **&lt;sites&gt;** find the **&lt;site&gt;** which has the issue (or apply the fix to all sites if you prefer)
3. Under **&lt;site&gt;** find the **&lt;virtualDirectory&gt;** element with path set to “/”
4. Add the attribute **allowSubDirConfig=”false”** to this element.
5. It should look something like this (apart from physicalPath obviously): **&lt;virtualDirectory path=”/” physicalPath=”D:\\MySite” allowSubDirConfig=”false” /&gt;**
6. Reboot the server and you’re done 🙂 (Note: rebooting may not be necessary – I’ve not been able to find accurate documentation stating whether it is)