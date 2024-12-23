---
title: 'Webpage blinks in Internet Explorer'
layout: post
---

I’ve encountered this peculiar bug a couple of times in Internet Explorer.  
Sometimes parts of the webpage either blinks constantly, or disappears when e.g. hovering links only to reappear upon scrolling or hovering another link.

So far I’ve had success with a simple solution however 🙂

It appears Internet Explorer does this, because of the CSS style “position: relative;”. However that’s not the entire story – it \*only\* does it for elements which doesn’t **have layout**.

The whole concept of “having layout” is something only Internet Explorer uses, and it’s been around for quite a while as well (At least since IE 6).

The short story is: IE has a hidden property called “HasLayout”, which determines how elements are rendered. Unfortunately you cannot just set this property -you have to “trigger” it.

So my simple solution ? Trigger “HasLayout” with the CSS property “zoom”. Setting zoom to 1 does absolutely nothing, except for triggering “HasLayout” – a perfect solution 🙂

So from now on, whenever you use position: relative, consider adding a zoom: 1 as well, just to be sure IE behaves properly:

```

div.content
{
  position: relative;
  zoom: 1;
}
```

If you want to read more about “HasLayout”, msdn has an excellent article here: <http://msdn.microsoft.com/en-us/library/bb250481(v=vs.85).aspx>