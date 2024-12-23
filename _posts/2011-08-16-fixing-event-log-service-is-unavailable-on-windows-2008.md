---
id: 173
title: 'Fixing &#8220;Event log service is unavailable&#8221; on Windows 2008'
date: '2011-08-16T16:38:07+01:00'
author: Steffen
excerpt: "One of our servers started showing this error: \"Event log service is unavailable. Verify that the service is running.\"\r\n\r\nFixing it proved quite simple, after a lot of searching on google..."
layout: post
guid: 'http://www.ckode.dk/?p=173'
permalink: /server-configuration/fixing-event-log-service-is-unavailable-on-windows-2008/
categories:
    - 'Server configuration'
tags:
    - 'Windows 2008'
---

Out of nowhere one of our servers wouldn’t show its event log anymore.

It simply popped up with this error: “Event log service is unavailable. Verify that the service is running.”

So I started by checking the service “Windows Event Log”, and sure enough – it wasn’t running. It was however set to automatically start, so I tried starting it manually.

This in turn popped this error: “Windows could not start the windows event log service on local computer.  
Error 2 – Cannot not find the file specified”

A bit of googling found a simple solution:

Open regedit, and go to “HKLM\\System\\CurrentControlSet\\Services\\Eventlog”  
Here you’ll find a key called “Parameters” – delete this and the service should be able to startup.

NOTE: When dealing with the registry database, it’s wise to make a backup before changing anything!