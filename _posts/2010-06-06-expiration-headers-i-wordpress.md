---
id: 21
title: 'Expiration headers i WordPress'
date: '2010-06-06T11:31:45+01:00'
author: Steffen
layout: post
guid: 'http://www.ckode.dk/?p=21'
permalink: /wordpress/expiration-headers-i-wordpress/
categories:
    - Wordpress
tags:
    - Wordpress
---

Hvis man går lidt op i loadtider for ens site, er det en rigtig god ide at smide expiration header på alt indhold, der ikke ændrer sig.

Det kunne f.eks. være billeder, stylesheet og javascript i et WordPress theme.

Heldigvis kører de fleste WordPress installationer på en standard Apache server under \*nix, og der er det virkelig let at tilføje expiration headers.

Bare tilføj denne stump til din .htaccess fil, og så er der 2 ugers expiration på de angivne filtyper 🙂  
`<br></br>#Expire Header<br></br>ExpiresActive on<br></br><FilesMatch "\.(ico|jpg|jpeg|png|gif|js|css|swf)$"><br></br>ExpiresDefault "access plus 2 weeks"<br></br></FilesMatch><br></br>`