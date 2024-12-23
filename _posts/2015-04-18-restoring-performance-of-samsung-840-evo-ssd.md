---
id: 267
title: 'Restoring performance of Samsung 840 EVO SSD'
date: '2015-04-18T12:03:57+01:00'
author: Steffen
excerpt: "If you're reading this, you've most likely already heard about the problems with old data getting slow on Samsung's popular 840 EVO SSD drive.\r\n\r\nHere I go over a simple workaround to keep performance up, until Samsung can fix the problem permanently."
layout: post
guid: 'http://www.ckode.dk/?p=267'
permalink: /desktop-machines/restoring-performance-of-samsung-840-evo-ssd/
categories:
    - 'Desktop machines'
---

If you’re reading this, you’ve most likely already heard about the problems with old data getting slow on Samsung’s popular 840 EVO SSD drive.

The issue only hits old data, so if you’re constantly writing data to the drive, chances are you aren’t affected.

In short the problem is the NAND cells losing charge over time, which in turn forces a lot of retries when the controller wants to read data from the NAND. This is what causes read performance to plummet to below 10 MB/S.

Samsung initially released a firmware which should fix it, however that only worked briefly as all it did was rewrite all the data of the drive.

You can however do this every once in a while to easily maintain performance. Do note however that everytime you rewrite all the data, you’re wearing the drive ever so slightly.

There’s a really simple tool called DiskFresh, which will do exactly this: read and rewrite all data of a drive. You can download the tool here: <http://www.puransoftware.com/DiskFresh.html>

Unfortunately I haven’t got an 840 EVO myself, but a friend of mine does, and he was kind enough to first benchmark his drive, then DiskFresh it, and finally benchmark it again.

Benchmarks are done using the old but excellent tool HDTach, which you’ll find here: <http://www.majorgeeks.com/files/details/hdtach.html>

Below are the results from before/after. Do note his drive is an mSata version, so its performance may be slightly below that of a “normal” 2,5″ 840 EVO.

#### 8 MB test before DiskFresh:

[![HDTachBefore8mb](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachBefore8mb.png)](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachBefore8mb.png)

#### 8 MB test after DiskFresh:

[![HDTachAfter8mb](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachAfter8mb.png)](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachAfter8mb.png)

#### 32 MB test before DiskFresh:

[![HDTachBefore32mb](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachBefore32mb.png)](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachBefore32mb.png)

#### 32 MB test after DiskFresh:

[![HDTachAfter32mb](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachAfter32mb.png)](http://www.ckode.dk/wordpress/wp-content/uploads/2015/04/HDTachAfter32mb.png)

First the obvious conclusion: Before DiskFresh there are huge gaps with awful performance – these are completely gone after DiskFresh.  
However as you may have noticed the average read speed actually drops after DiskFresh. For the 8 MB test this is definately a fluke, as the initial benchmark has a section reading at over 1.600 MB/S, which simply isn’t possible on SATA.  
For the 32 MB test I’m inclined to call it a fluke as well, as it has readings of over 600 MB/S, which the 840 EVO shouldn’t be capable of in the first place.

Even if the 32 MB test isn’t a fluke I’d personally happily take that trade rather than having huge portions of my drive reading at http://www.anandtech.com/show/9158/new-samsung-ssd-840-evo-read-performance-fix-coming-later-this-month