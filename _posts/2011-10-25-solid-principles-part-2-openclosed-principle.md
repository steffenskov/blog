---
id: 190
title: 'SOLID principles &#8211; Part 2: Open/Closed Principle'
date: '2011-10-25T16:52:40+01:00'
author: Steffen
excerpt: "This is the second of a five part series, where I go over the 5 principles which make up the <a href=\"http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)\" target=\"_blank\">SOLID</a> principles by <a href=\"http://en.wikipedia.org/wiki/Robert_C._Martin\" target=\"_blank\">Robert C. Martin</a>.\r\n\r\nThe second one is O: <a href=\"http://en.wikipedia.org/wiki/Open/closed_principle\" target=\"_blank\">Open/Closed Principle</a> (OCP)\r\n\r\nThis principle states that classes should be open to extension, but closed to modification."
layout: post
guid: 'http://www.ckode.dk/?p=190'
permalink: /programming/solid-principles-part-2-openclosed-principle/
categories:
    - Programming
tags:
    - 'C#'
    - SOLID
---

This is the second of a five part series, where I go over the 5 principles which make up the [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles by [Robert C. Martin](http://en.wikipedia.org/wiki/Robert_C._Martin).

The second one is O: [Open/Closed Principle](http://en.wikipedia.org/wiki/Open/closed_principle) (OCP)

This principle states that classes should be open to extension, but closed to modification.

Usually this is archieved through inheritance, where your extension inherits the existing class, and applies whatever functionality you need.

The reason for doing this is simple: You don’t modify existing, working code.  
This decreases the risk of introducing bugs, and thus increases the stability of your code.

This means all new functionality should go into new classes, and the existing classes should only be modified for bug fixing.

For example:

```

public enum ShapeType
{
	Square,
	Rectangle
}
public abstract class Shape
{
	public abstract ShapeType Type { get; }

	public static void RenderShapes(IEnumerable<Shape> shapes)
	{
		foreach (Shape shape in shapes)
		{
			switch (shape.Type)
			{
				case ShapeType.Rectangle:
				{
					RenderRectangle(shape);
					break;
				}
				case ShapeType.Square:
				{
					RenderSquare(shape);
					break;
				}
			}
		}
	}

	private static void RenderSquare(Shape shape)
	{
	}

	private static void RenderRectangle(Shape shape)
	{
	}
}
```

Now some people will be thinking: “That code is awful, nobody writes code like that”. Unfortunately that’s not the case at all – the code is indeed awful, but it’s very common practice 🙁

But back on track: Why is this code awful ? I mean clearly it’s open to extension, as you can easily create a new class inheriting Shape.  
It is however not closed for modification at all: While creating a subclass is easy, getting it to render is difficult.

Suppose you introduced a new shape: Circle. This would require you to modify the switch statement in the base class, as well as create a new RenderCircle method in it.

That’s horrible design as you risk breaking the switch statement in the base class (yes it’s simple in this example, but most real life examples are far more complex).

So how would we improve this using Open/Closed Principle ?

Like this:

```

public abstract class Shape
{
	public abstract ShapeType Type { get; }
	protected abstract void Render();

	public static void RenderShapes(IEnumerable<Shape> shapes)
	{
		foreach (Shape shape in shapes)
		{
			shape.Render();
		}
	}
}
```

Now each subclass is in charge of its own rendering logic. This means we no longer need to modify the base class, whenever a new shape is introduced.

That pretty much wraps it up – quite a simple principle this one 🙂

If you want to read more about the principle, there’s a brief overview at Wikipedia here: [http://en.wikipedia.org/wiki/Open/closed\_principle](http://en.wikipedia.org/wiki/Open/closed_principle)  
and an excellent article by Robert C. Martin here: <http://www.objectmentor.com/resources/articles/ocp.pdf>

### Links to the other parts of this series:

1. [Single Responsibility Principle (SRP)](http://www.ckode.dk/programming/solid-principles-part-1-single-responsibility-principle/)
2. Open/Closed Principle (OCP)
3. [Liskov’s Substitution Principle (LSP)](http://www.ckode.dk/programming/solid-principles-part-3-liskovs-substitution-principle/)
4. [Interface Segregation Principle (ISP)](http://www.ckode.dk/programming/solid-principles-part-4-interface-segregation-principle/)
5. [Dependency Inversion Principle (DIP)](http://www.ckode.dk/programming/solid-principles-part-5-dependency-inversion-principle/)