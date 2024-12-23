---
title: 'Getting a &#8220;pure&#8221; date in MS SQL (without the time)'
layout: post
---

You probably know the situation: You want to get all records grouped by day, but you stored their creation date using getdate(), so now they all contain time as well as the date.

The solution until MS SQL 2008 was not pretty:

```

CAST(FLOOR(CAST(DateCreated as Float)) as DateTime)
```

This works because datetime are stored internally as floating point numbers, and it just so happens that the date part is stored as the integral part and the time as the decimal part.  
In other words the above is somewhat prone to breaking, as it works by assuming how the datetime datatype is stored internally – Microsoft could potentially change this in a new version of MS SQL, and you’d be screwed. (So far they haven’t though, so no worries… yet)

However with MS SQL 2008 we finally have a “pure” date datatype: [Date](http://msdn.microsoft.com/en-us/library/bb630352.aspx)

So from MS SQL 2008 you can just get the date like this:

```

CAST(DateCreated as Date)
```

Simple, huh ?

If you for instance want every row created today, you’d do this:

```

SELECT * FROM SomeTable WHERE Cast(DateCreated as Date) = CAST(getdate() as Date)
```

Remember in all these examples DateCreated is a DateTime. This is often the case if:

- Your project used to run on MS SQL 2005
- You’re new to MS SQL 2008 and didn’t know about the Date type

In fact I work on a project which falls under the first category 🙂

And just as a bonus I’d like to mention the new [Time](http://msdn.microsoft.com/en-us/library/bb677243.aspx) datatype – it’s purpose should be pretty self explanatory 😉

And yeah you can do this with time as well as with Date:

```

CAST(DateCreated as time)
```