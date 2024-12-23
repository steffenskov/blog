---
id: 155
title: 'Trouble reading registry key from C#'
date: '2010-11-23T19:29:09+01:00'
author: Steffen
excerpt: "Reading from registry in C# is very simple, so I was quite stumped when my code didn't work.\r\nThe reason however, was very simple once found: The Wow6432Node..."
layout: post
guid: 'http://www.ckode.dk/?p=155'
permalink: /programming/trouble-reading-registry-key-from-c/
categories:
    - Programming
tags:
    - 'C#'
---

Recently I needed to read a registry key in C#. This is obviously pretty simple using the Registry class in the “Microsoft.Win32” namespace:

```

public string ReadHypervisorName()
{
	using (var key = Registry.LocalMachine.OpenSubKey(@"SOFTWARE\Microsoft\Virtual Machine\Guest\Parameters", false))
	{
		return key.GetValue("PhysicalHostNameFullyQualified") as string;
	}
}
```

As you can tell, I’m trying to read out the servername of a virtual machine’s hypervisor (using MS [Hyper-V](http://www.wikipedia.org/search-redirect.php?language=en&go=Go&search=Hyper-V) virtualization). However this didn’t work!

OpenSubKey simply returned null.

I could get as far as opening SOFTWARE\\Microsoft, and that was it. “Virtual Machine” apparently didn’t exist…

Very puzzling indeed, as I could see it in my registry editor on the server.

I tried checking the [ACLs](http://www.wikipedia.org/search-redirect.php?language=en&go=Go&search=Access_control_list) for the registry key, which was fine – my user had full access to it.

Then something struck me: “Wow6432Node”. This is a registry key which exists beneath SOFTWARE and is used for 32-bit software under 64-bit Windows.

I quickly checked my project configuration, and alas Visual Studio had configured it for x86 by default!

So when I asked the Registry class to open a subkey, it was in fact adding Wow6432Node to it, so it actually tried finding “SOFTWARE\\Wow6432Node\\Microsoft\\Virtual Machine\\Guest\\Parameters” which obviously doesn’t exist.

I changed my build configuration to “Any CPU” (why Visual Studio defaults to x86 is beyond me…) and the code worked like it should 🙂