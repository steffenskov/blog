---
id: 160
title: 'Avoiding caching with jQuery.load'
date: '2010-12-18T16:32:36+01:00'
author: Steffen
excerpt: 'jQuery.load is an excellent way to load parts of your website asynchronously, however by default the browser just might cache the result from it, and your page will never be refreshed.'
layout: post
guid: 'http://www.ckode.dk/?p=160'
permalink: /programming/avoiding-caching-with-jquery-load/
categories:
    - Programming
tags:
    - 'Internet Explorer'
    - jQuery
---

[jQuery.load](http://api.jquery.com/load/) is an excellent way to load parts of your website asynchronously, however by default the browser just might cache the result from it, and your page will never be refreshed.

This mostly occurs in Internet Explorer, but can potentially happen in any browser.

The solution, is luckily simple: Add a random querystring to the url you’re trying to load. This way the browser sees the request as a new unique one every single time.

If you’re refreshing a part of your page with [jQuery.load](http://api.jquery.com/load/) every X seconds, you want to do this in javascript – otherwise it’ll only be the first request which is unique.

In fact the simplest solution is to \*always\* create the random querystring in javascript, that way you’ll never forgot it.

Even so, there’s an even simpler approach: just use the data argument for jQuery.load, which does exactly what we want.

Here’s a snippet for you:

```

  jQuery('#content').load('http://www.foo.dk/bar.html', 'f' + (Math.random()*1000000));
```

Load takes 2 arguments here: The URL to load, and an (optional) argument which is included in the request.

Notice that the argument part starts with a string, and then append Math.random ? For some reason you cannot just use Math.random – some browser won’t accept that (which means they’ll still cache the requests).

And that’s pretty much it – simple, isn’t it ? 🙂