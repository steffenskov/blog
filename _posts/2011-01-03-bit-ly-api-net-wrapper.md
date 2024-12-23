---
id: 164
title: 'Bit.ly API .Net wrapper'
date: '2011-01-03T19:14:10+01:00'
author: Steffen
excerpt: "This is a simple-to-use Bit.ly API wrapper for .Net.\r\nIt makes use of the well-documented REST api for Bit.ly."
layout: post
guid: 'http://www.ckode.dk/?p=164'
permalink: /downloads/bit-ly-api-net-wrapper/
categories:
    - Downloads
tags:
    - 'C#'
---

\[[Download](http://www.ckode.dk/wordpress/wp-content/uploads/2011/01/Bitly.Net_1.0.zip)\]

This is a simple-to-use Bit.ly API wrapper for .Net. It makes use of the REST api for Bit.ly which is documented here: [code.google.com/p/bitly-api/wiki/ApiDocumentation](http://code.google.com/p/bitly-api/wiki/ApiDocumentation).

The wrapper is unfortunately not entirely feature complete, as Bit.ly has added more features since I developed this. It does however have all the core functions, which really is all you’ll need.

It requires .Net 3.5 and includes full source code (of course).

It’s also my first project written using [TDD (Test Driven Development)](http://www.wikipedia.org/search-redirect.php?language=en&go=Go&search=Test_driven_development), so there’s a pretty large unit test suite included in the project.

The unit tests require Visual Studio 2010 Professional or higher, as I’m using VS’ unit testing capabilities instead of [NUnit](http://www.wikipedia.org/search-redirect.php?language=en&go=Go&search=NUnit).

Finally here’s a simple example of using the library:

```

var client = new BitlyClient("yourlogin", "yourapikey");
var shortUrl = client.Shorten("http://www.ckode.dk/downloads/bit-ly-api-net-wrapper");
Console.WriteLine(shortUrl); // Writes "http://bit.ly/g8F9Lj"
```

And that’s all there’s too it 🙂

Download it here: \[[Download](http://www.ckode.dk/wordpress/wp-content/uploads/2011/01/Bitly.Net_1.0.zip)\]