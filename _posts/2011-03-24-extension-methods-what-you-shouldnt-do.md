---
id: 168
title: 'Extension methods, what you shouldn&#8217;t do'
date: '2011-03-24T19:56:38+01:00'
author: Steffen
excerpt: "I'll admit it, I love extension methods, especially the ones from the LinQ-to-objects framework.\r\n\r\nHowever I often see them abused, partly by making everything extension methods, and partly by breaking readability with methods which normally shouldn't work.\r\n\r\nAlso I'd like to share a few of my extension methods with you - intrigued ? Read on :-)"
layout: post
guid: 'http://www.ckode.dk/?p=168'
permalink: /programming/extension-methods-what-you-shouldnt-do/
categories:
    - Programming
tags:
    - 'C#'
    - LinQ
---

I’ll admit it, I love extension methods, especially the ones from the [LinQ-to-objects](http://msdn.microsoft.com/en-us/library/bb397919.aspx) framework.

However I often see them abused, partly by making everything extension methods, and partly by breaking readability with methods which normally shouldn’t work.

The latter requires an example, obviously:

```

public static bool IsNullOrEmpty(this string str)
{
	return string.IsNullOrEmpty(str);
}
```

What’s wrong with the above, you may wonder ? Well when using it it’ll look like this:

```

string name = null;
if (!name.IsNullOrEmpty())
	Console.WriteLine(name);
```

Normally calling a method on a null object will throw a [NullReferenceException](http://msdn.microsoft.com/en-us/library/system.nullreferenceexception.aspx), and rightly so!

Reading the above you’d think: this’ll break. You’d have to dig into the IsNullOrEmpty extension method, in order to understand that it doesn’t break.

Now assume that the extension method resides in a 3rd party DLL, so you haven’t got the source code… You’d have to test the method, or simply avoid it – either way is bad.

Finally, how much did you actually gain by writing foo.IsNullOrEmpty() instead of string.IsNullOrEmpty(foo) ? Exactly 6 letters! (“string”).

Bottom-line: Don’t do this 🙂

After this rant, I’ll show you some extension methods I wrote and use frequently (most are for ASP.Net):

**ToHashSet**

```

public static HashSet<T> ToHashSet<T>(this IEnumerable<T> items)
{
	return new HashSet<T>(items);
}

public static HashSet<T> ToHashSet<T>(this IEnumerable<T> items, IEqualityComparer<T> comparer)
{
	return new HashSet<T>(items, comparer);
}
```

I for one don’t understand why this isn’t included in the [LinQ-to-objects](http://msdn.microsoft.com/en-us/library/bb397919.aspx) framework, as it already has ToList, ToArray and ToDictionary.

It’s used the same way as the other ToFoo methods:

```

	var usernames = users
					.Select(user => user.Name)
					.ToHashSet();
```

**IsItem**

```

public static bool IsItem(this RepeaterItemEventArgs e)
{
	return (e.Item.ItemType == ListItemType.AlternatingItem || e.Item.ItemType == ListItemType.Item);
}

public static bool IsItem(this DataListItemEventArgs e)
{
	return (e.Item.ItemType == ListItemType.AlternatingItem || e.Item.ItemType == ListItemType.Item);
}
```

This one really shortens the check for ItemType inside ItemDataBound events of the respective repeating controls.

```

protected void repUsers_ItemDataBound(object sender, RepeaterItemEventArgs e)
{
	if (!e.IsItem())
		return;
	// As opposed to
	if (e.Item.ItemType != ListItemType.AlternatingItem && e.Item.ItemType != ListItemType.Item)
		return;
}
```

**FindControl**

```

public static T FindControl<T>(this RepeaterItemEventArgs e, string id) where T : Control
{
	return (T)e.Item.FindControl(id);
}

public static T FindControl<T>(this DataListItemEventArgs e, string id) where T : Control
{
	return (T)e.Item.FindControl(id);
}
```

A nice shortcut to the somewhat hideous syntax you’d otherwise use for setting e.g. Literals’ Text inside ItemDataBound events:

```

protected void repUsers_ItemDataBound(object sender, RepeaterItemEventArgs e)
{
	var user = (User)e.Item.DataItem;
	e.FindControl<Literal>("litName").Text = user.Name;
	// As opposed to
	((Literal)e.Item.FindControl("litName")).Text = user.Name;
}
```

And that’s it, notice how few I have – Either I’m missing out on something, or everything doesn’t have to be an extension method – what do you think ?

Also if you’ve got any nice extension methods up your sleeve, feel free to share them with me 🙂 (Dropping a comment)