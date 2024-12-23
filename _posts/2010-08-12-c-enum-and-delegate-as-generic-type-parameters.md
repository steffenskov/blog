---
title: 'C# Enum and Delegate as generic type constraint'
layout: post
---

In C# there are a few limitations to what you may use as a generic type constraint, these include (this list isn’t exhaustive, mind you):

- delegates
- enums
- value types

Also only one class type can be used at a time, so you can’t go

```
class Foo<T> where T: OneClass, SomeOtherClass
```

The limitation I find most annoying is for enums, but there’s a somewhat simple work-around for that:

```

class Foo<T> where T: struct
static Foo()
{
if (! typeof (T).IsSubclassOf( typeof ( Enum )))
throw new ArgumentException ( "Type argument T is not an enum: " + typeof (T).Name);
}
```

This makes use of the fact that a static constructor is called only once. However if the class has generic type parameters, the static constructor is called once for each unique generic type parameter combination (assuming multiple generic type parameters).

So the following code would trigger the static constructor two times (but not three!)

```

var i = new Foo<int>();
var j = new Foo<double>();
var k = new Foo<int>();
```

Thereby we only test whether the generic type parameter is valid **once** per type, thus not hurting performance.

That being said, the solution is slightly less neat than it seems at first. This is because exceptions thrown within the static constructor are automatically wrapped inside another exception: TypeInitializationException.  
The InnerException of the TypeInitializationException, will then hold the ArgumentException your code throws.

Obviously the same method is applicable to delegates and any value types as well 🙂