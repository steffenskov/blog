---
id: 14
title: '1-Click install wordpress hos unoeuro'
date: '2010-06-06T11:09:10+01:00'
author: Steffen
layout: post
guid: 'http://www.ckode.dk/?p=14'
permalink: /wordpress/1-click-install-wordpress-hos-unoeuro/
categories:
    - Wordpress
tags:
    - Wordpress
---

[Unoeuro](http://www.unoeuro.com/) er et af de billigere webhoteller i Danmark, og de har for nylig fået en ganske fiks feature for PHP sites: 1-Click install.

Det betyder kort og godt, at du bare klikker på det du vil have kørende på dit site, og så lige angiver password mv. Og vupti så er det i luften.

Det tager ca. 30 sekunder at sætte [WordPress](http://wordpress.org/) op på denne måde – ganske snildt. Samtidig får du sat fil rettigheder osv. korrekt, så du slipper for at bakse med FTP oplysninger når du installerer plugins osv.

Eneste ulempe er at WordPress ikke er dansk per default, men det er heldigvis hurtigt at fikse:

1. Download [da\_DK.mo](http://svn.automattic.com/wordpress-i18n/da_DK/trunk/messages/da_DK.mo "Direkte download link") herfra: <http://da.wordpress.org/>
2. Upload da\_DK.mo til dit site i mappen “public\_html/wp-content/languages”
3. Download wp-config.php (ligger i public\_html)
4. Åben filen i en teksteditor (notepad, etc.) og find linien der starter med “define (‘WPLANG’,”
5. Ændr denne linie til “define (‘WPLANG’, ‘da\_DK’);” og gem filen.
6. Upload filen igen til public\_html og overskriv den eksisterende.

Så kører din WordPress installation dansk 🙂

Udover WordPress har Unoeuro følgende 1-Click install muligheder: [Coppermine](http://coppermine-gallery.net/), [Joomla](http://www.joomla.org/), [osCommerce](http://www.oscommerce.com/), [phpBB](http://www.phpbb.com/), [typo3](http://typo3.org/).