---
id: 261
title: 'C# based simple service locator'
date: '2015-03-04T21:09:37+01:00'
author: Steffen
excerpt: "I've always loved the Service locator pattern - why ? Because it requires so little maintenance, and it's a very crucial point if you want somewhat automatic dependency injection.\r\n\r\nAs you can read in my blog post \"Staying DRY: Mind those switch statements\" a service locator can replace cumbersome multiple switch statements which require each one to be changed whenever a new case comes along."
layout: post
guid: 'http://www.ckode.dk/?p=261'
permalink: /programming/c-based-simple-service-locator/
categories:
    - Programming
tags:
    - 'C#'
    - Caching
---

Before you read any further I strongly suggest downloading the source file and opening it in Visual Studio (or whichever environment you prefer).

[Download ServiceLocator.zip](http://www.ckode.dk/wordpress/wp-content/uploads/2015/10/servicelocator.zip)

I’ve always loved the [Service locator pattern](http://en.wikipedia.org/wiki/Service_locator_pattern) – why ? Because it requires so little maintenance, and it’s a very crucial point if you want somewhat automatic dependency injection.

As you can read in my blog post “[Staying DRY: Mind those switch statements](http://www.ckode.dk/programming/staying-dry-mind-those-switch-statements/)” a service locator can replace cumbersome multiple switch statements which require each one to be changed whenever a new case comes along.

In that article I’ve gone with an extremely simple approach, which used [Reflection](https://msdn.microsoft.com/en-us/library/f7ykdhsy%28v=vs.110%29.aspx) to find whatever type implemented a given interface and contained a given key, and ran [Activator.CreateInstance](https://msdn.microsoft.com/en-us/library/system.activator.createinstance%28v=vs.110%29.aspx) to give me an instance. I then cached that instance for quick lookups later.

This is all well and good for replacing switches, but it’s rather lackluster if you want a new instance every time. You could change it to simply call [Activator.CreateInstance](https://msdn.microsoft.com/en-us/library/system.activator.createinstance%28v=vs.110%29.aspx) each time, but I want something faster.

As some of you might know [Activator.CreateInstance](https://msdn.microsoft.com/en-us/library/system.activator.createinstance%28v=vs.110%29.aspx) IS actually fast, but only for the first few types you create – it has a very small internal cache it uses to speed things up.

But for larger projects this internal cache simply isn’t enough, so I opted to create my own cache of infinite size – trading RAM for CPU is a rather common approach to performance, and the premise of all caching solutions.

Now what I do is use Reflection to find a proper parameterless constructor for a given type, then I use [DynamicMethod](https://msdn.microsoft.com/en-us/library/system.reflection.emit.dynamicmethod%28v=vs.110%29.aspx) to create a new method which invokes this constructor, and finally I cache a delegate to this new [DynamicMethod](https://msdn.microsoft.com/en-us/library/system.reflection.emit.dynamicmethod%28v=vs.110%29.aspx).

The reason I go with [DynamicMethod](https://msdn.microsoft.com/en-us/library/system.reflection.emit.dynamicmethod%28v=vs.110%29.aspx) is that invoking a [ConstructorInfo](https://msdn.microsoft.com/en-us/library/system.reflection.constructorinfo%28v=vs.110%29.aspx) directly is rather slow, to the best of my knowledge it utilizes [Reflection](https://msdn.microsoft.com/en-us/library/f7ykdhsy%28v=vs.110%29.aspx) each time it’s invoked, and as we all know [Reflection](https://msdn.microsoft.com/en-us/library/f7ykdhsy%28v=vs.110%29.aspx) is slow.

So I build a new [DynamicMethod](https://msdn.microsoft.com/en-us/library/system.reflection.emit.dynamicmethod%28v=vs.110%29.aspx) and use IlGenerator to set it up to simply return the result from my [ConstructorInfo](https://msdn.microsoft.com/en-us/library/system.reflection.constructorinfo%28v=vs.110%29.aspx), simple as that. In the included class I’ve limited it to parameterless constructors, but the code building the [DynamicMethod](https://msdn.microsoft.com/en-us/library/system.reflection.emit.dynamicmethod%28v=vs.110%29.aspx) actually support parameters, so you can easily expand upon it.

All of this is of course terribly slow, so to ensure good performance I cache everything in a static [ConcurrentDictionary](https://msdn.microsoft.com/en-us/library/dd287191%28v=vs.110%29.aspx), and I even use a lock to ensure I don’t create something twice.

The reason for using [ConcurrentDictionary](https://msdn.microsoft.com/en-us/library/dd287191%28v=vs.110%29.aspx), is to allow lock-less Gets even when another thread is currently inserting into the dictionary. The custom lock is to ensure two threads don’t run the same [Reflection](https://msdn.microsoft.com/en-us/library/f7ykdhsy%28v=vs.110%29.aspx) code, because it’s simply faster to wait for the one thread that’s already on the job.

I’ve implemented two classes in this ServiceLocator – one which simply takes an interface and gives you an instance of whichever class implements it. This is useful for abstracting away a persistance layer. You could run one layer in production, another in development and a third for [unit testing](http://en.wikipedia.org/wiki/Unit_testing).

And another which like the example in the article about [DRY](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself) is created around an interface which defines a key. This locator will give you an instance which implements the predefined interface AND contains the proper key. This is the one you want to use to replace those pesky switches.

An example of using the first one:

```
public interface IFoo
{
}

private class Foo : IFoo
{
}

class Program
{
	static void Main(string[] args)
	{
		ServiceLocator locator = new ServiceLocator();
		IFoo foo = locator.CreateInstance<ifoo>(); // This will create an instance of the class Foo
	}
}
</ifoo>
```

Notice in this example that the class Foo is private – the ServiceLocator doesn’t require the class to be public. Actually it also doesn’t require a public constructor, it’ll work just as well with a private one.

Now for an example of the other version:

```
public enum FooType
{
	First,
	Second
}

public interface IFoo : ILocatable<FooType>
{
}

internal class FooFirst : IFoo
{
	FooType ILocatable<FooType>.LocatorKey
	{
		get { return FooType.First; }
	}
}

internal class FooSecond : IFoo
{
	public FooType LocatorKey
	{
		get { return FooType.Second; }
	}
}

class Program
{
	static void Main(string[] args)
	{
		ServiceLocator<FooType, IFoo> locator = new ServiceLocator<FooType, IFoo>();

		var first = locator.CreateInstance(FooType.First); // Creates an instance of FooFirst
		var second = locator.CreateInstance(FooType.Second); // Creates an instance of FooSecond
	}
}
```