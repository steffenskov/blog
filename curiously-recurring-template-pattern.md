```
layout: post
title: Curiously Recurring Template Pattern
```

Try saying that out loud 3 times rapidly :-D

This is actually an old pattern dating back to the early C++ days, however it's recently started being used in the .NET framework by Microsoft, and for good reason too.

The generic math functionality which came about in .NET 7 makes use of this. Here's how the CRT (Curiously Recurring Template) part of the interface looks:

```
public interface INumber<TSelf> 
where TSelf : INumber<TSelf>?
{
}
```
  
which means e.g. `double` now has this definition:
```
public struct double : INumber<double>
{
}
```
  
The idea here is to allow an abstract to know about its concrete implementation. This can be useful both for interfaces and abstract classes, where you want to build a generic implementation or specification, that relies on the concrete type in question.

I'm using this approach myself in the StrongTypedId library for a bunch of stuff, but here's one of the more useful examples (Notice how `IParsable<TSelf>` from the .NET framework ALSO uses this pattern):

```
public abstract class StrongTypedId<TSelf, TPrimitiveId>
where TSelf: StrongTypedId<TSelf, TPrimitiveId>
where TPrimitiveId: IParsable<TPrimitiveId>
{
	public static TSelf Parse(string s, IFormatProvider? provider = null)
        {
		return Create(TPrimitiveId.Parse(s, provider));
        }
        
        public static TSelf Create(TPrimitiveId value)
        {
        	// Removed for brevity
        }
}
```

In using the library I can create an `UserId` class this simple:

```
public class UserId : StrongTypedId<UserId, Guid>
{
}
```

And I'll get a  `public static UserId Parse(string s, IFormatProvider? provider = null)` method for free. This is absolutely great when trying to keep your codebase DRY.
