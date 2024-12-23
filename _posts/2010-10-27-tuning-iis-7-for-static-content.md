---
title: 'Tuning IIS 7 for static content'
layout: post
---

Note: The below works for both IIS 7 and IIS 7.5. If you don’t want the story behind this, you can jump straight to the [tuning instructions here](#instructions).

At work we recently discovered some performance issues with our media server. This is a somewhat powerful webserver, which only serves images uploaded by our users.

### The server

The server has a Core 2 Quad (Xeon actually) CPU, 20 GB of RAM and a RAID 1+0 consisting of 15k rpm SCSI disks. The RAID controller has 192 MB of cache (not that much, I know :-()

It runs Windows 2008 R2, and thus an IIS 7.5.

With that in mind we figured it should be plenty for serving out some puny images – after all they’re only about ~100 KB each. However some of our users started complaining the site was slow during prime time. Also they said images would occasionally load very slowly, one at a time.

It didn’t take long to assert this, however the CPU was hardly doing anything – running at roughly 10% usage. And the server still had 12GB free memory, so the only reasonable explanation had to be I/O.  
A perfmon later we asserted this as well.

### Finding the bottleneck

When checking perfmon for I/O bottlenecks, the preferred counter is found under `PhysicalDisk` and called `% Idle Time`, which, as a rule of thumb, should stay above 20%. Ours was flatlining at 0…  
Clearly the disk was very busy all the time, and requests were queuing up.

### Removing the bottleneck

Now we had two options for solving this:

1. Upgrade the servers’ hardware with a new RAID controller (with a larger cache) and perhaps some fasters disk.
2. Try to cache more of the files in memory

Whilst our system administrator checked prices etc. for upgrading the hardware, I started toying around with the IIS 7.

First I made sure we had expiration headers enabled (so the images would be cached locally in the user’s browser)

I also checked that output caching was indeed enabled in the Internet Information Services (IIS) Manager, and the maximum file size was reasonable (it defaults to 256KB, which is fine for our usage)

All this was fine.

From here the Internet Information Services (IIS) Manager is useless, you have to start looking into configuration files instead.

There are 3 settings which have a major impact on caching in the IIS:

1. **frequentHitThreshold**: How many times should the same file be requested, before it’s cached ? Defaults to 2.
2. **frequentHitTimePeriod**: Time interval in which the same file should be requested {frequentHitThreshold} times, in order to be cached. Defaults to 10 seconds.
3. **ObjectCacheTTL**: How long the cache lives, before being flushed for unused files. Defaults to 30 seconds

With the default settings, I checked perfmon for some caching counters (all found under “Web Service Cache”):

- **Current File Cache Memory Usage**
- **Current Files Cached**
- **File Cache Hits %**

These should be pretty self-explanatory.

At default the server used around 8MB memory and cached 600 files. Also the File Cache Hits % was around 1%.

Clearly some horrible numbers…

First I changed **frequentHitThreshold** to 1, meaning every file is instantly cached. This also means **frequentHitTimePeriod** is superfluous as it’s never checked.  
This alone changed our total counters to this:

- **Current File Cache Memory Usage**: 86MB
- **Current Files Cached**: 6.000
- **% Idle Time**: **85%**

Now the server caches 10 times as many files, and the disk is hardly doing anything! (Especially the **% Idle Time** is important here, as it’s a pretty direct measurement of the end-user performance)

However we still weren’t using that much memory, as many of our images are actually thumbnails at around 10 KB or less.

Not content with that, I went ahead and doubled **ObjectCacheTTL** to 60 seconds. However this was today, and our primetime is during the evening, so my only measurements of this, are made with quite few users online.

With the default setting of 30 seconds (but **frequentHitThreadhold** set to 1) we we’re caching around 1.600 files on average.  
After changing the setting to 60 seconds, we’re caching around 6.000 files! That’s almost a factor 4 improvement on top of the existing factor 10 improvement.

This evening will tell, whether we get a factor 40 improvement during prime time or not, but so far it’s looking very promising.

### Tuning Instructions<a name="instructions"></a>

Now the last piece missing in the puzzle: Where do you change these settings ? Well here goes:

**frequentHitThreshold** and **frequentHitTimePeriod** both reside in a file called `applicationHost.config`, which is found in `%windir%\system32\inetsrv\config`

This file resembles web.config and machine.config quite a bit, so you should be familiar with the syntax.

The element these attributes are set on, is called `serverRuntime` and should exist already, albeit being empty.  
e.g. it looks like this:  
`<serverRuntime />`

The syntax for setting the attributes is as follows:  
`<serverRuntime frequentHitTimePeriod="00:00:10" frequentHitThreshold="1" />`

so **frequentHitTimePeriod** is a TimeSpan, and **frequentHitThreshold** is an unsigned integer (should be 1 or more)  
Note that setting **frequentHitTimePeriod** is in fact superfluous when **frequentHitThreshold** is set to 1.

These changes take effect as soon as you save the file, and the result can be observed in perfmon immediately.

The last setting, **ObjectCacheTTL** resides in the registry database, and doesn’t exist per default either. It’s location is `HKEY\_LOCAL\_MACHINE\\System\\CurrentControlSet\\services\\InetInfo\\Parameters`

Create a new DWORD (32-bit) value called `ObjectCacheTTL` and set its decimal value to the number of seconds, which should pass between the IIS flushing its cache for unused files. (It only removes files which weren’t requested after initially being added to the cache)

There is however a minor quirk to this, as there is in fact **\*another\*** registry key, which does pretty much the same – only it doesn’t… Confused ? That’s understandable.

The thing is, IIS caches files either in user-mode or kernel-mode, and depending on which mode it caches in, it \*may\* use a different registry key for the flush interval.  
So to be certain it actually does what you want, create another DWORD (32-bit) value called `OutputCacheTTL` and give it the same value as **ObjectCacheTTL**.

The reason I say \*may\* use, is because I haven’t been able to find any official documentation stating exactly when each of the keys are used. So setting both is merely a “better safe than sorry” approach.

After setting these, you’ll need to reboot Windows for them to take effect – so don’t do this during your prime time 😉

Finally a word of caution: Whilst tuning the server, make sure you keep an eye on all bottlenecks of the system. If done wrong, you might end up using all your memory, and slowing down the system due to paging. We had no such problems, as we had plenty of memory, and our files are very small – your scenario might be different.

If you have limited memory, your best bet is to modify only **frequentHitTimePeriod** as it has the least impact of the 3, and as such can be used for more subtle tuning, with little risk of consuming too much memory.

As always, feel free to drop a comment with your own experiences 🙂

### Update

Today I got the results from the evening perfmon, and whilst we didn’t archive a factor 40 improvement, we still got around 20 times as many files cached (approx. 12.000), and used 185 MB for cache (compared to the puny 8 MB it initially used)

The disk has almost no activity, and as such we’ll tune it no further for now, even though we could.

### Second update

Whilst the server was indeed running very well, we anticipate a somewhat large increment in simultaneous online users, which in return means a higher load on the server. So I set out to increase the caching yet again.

Since I could only increment the **ObjectCacheTTL / OutputCacheTTL**, that’s what I did. I increased it from 60 seconds to a whopping 180 seconds, and the result was… As expected 🙂

In primetime we’re now caching 26.000 files on average (maxing out at 52.000) and the IIS consumes 400 MB RAM for caching, this is excellent!

I’m looking forward to the increase in users, to see how it copes – so far the disks are doing almost no work.

### Third update

As pointed out in the comments by Rovastar, I actually had another issue with the file caching.  
Every so the IIS would completely empty the cache (about every 2-3 minutes) for no apparent reason.

After a lot of debugging, and asking the question at Serverfault, I finally figured it out:  
In our web.config of the site serving this static content, we had this section:

`<caching enableKernelCache="true">`

After removing it, the odd complete flushes disappeared. The IIS still makes large flushes at times, however now it keeps around 50.000 files in the cache, when doing so.  
At the same time our performance counter for `File cache hit %”** went up from 5% to 33% – A huge increase.

If you’re interested, the question at Serverfault can be reached here: <http://serverfault.com/questions/448942/why-is-iis-7-5-flushing-file-cache-very-often>