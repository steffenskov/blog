---
title: 'SOLID principles - Part 5: Dependency Inversion Principle'
layout: post
---

This is the fifth of a five part series, where I go over the 5 principles which make up the [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles by [Robert C. Martin](http://en.wikipedia.org/wiki/Robert_C._Martin).

The fifth one is D: [Dependency Inversion Principle](http://en.wikipedia.org/wiki/Dependency_inversion_principle) (DIP)

This principle states two things:

- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend upon details. Details should depend upon abstractions.

The purpose of this principle is to decouple low-level and high-level modules, making changes as well as testing a lot easier.

Take this example, which shows what the principle tries to avoid:

```

[Serializable]
public class StoredSettings
{
	private readonly string storagePath;
	public StoredSettings(string storagePath)
	{
		this.storagePath = storagePath;
	}

	public void Save()
	{
		BinaryFormatter formatter = new BinaryFormatter();
		using (FileStream stream = File.Create(storagePath))
		{
			formatter.Serialize(stream, this);
		}
	}
}
```

The problem here is in the `Save` method. It uses a `BinaryFormatter`, and is heavily coupled to it.

A better design would be “injecting” the formatter – either in the `Save` method or in the constructor. I’d opt for the constructor since a `Load` method would need to use the same formatter.

That’d make it look like this:

```

[Serializable]
public class StoredSettings
{
	private readonly string storagePath;
	private readonly IFormatter formatter;
	public StoredSettings(string storagePath, IFormatter formatter)
	{
		this.storagePath = storagePath;
		this.formatter = formatter;
	}

	public void Save()
	{
		using (FileStream stream = File.Create(storagePath))
		{
			formatter.Serialize(stream, this);
		}
	}
}
```

The method used here is known as [Dependency Injection](http://en.wikipedia.org/wiki/Dependency_injection), because the formatter is injected into our class in the constructor.  
People often confuses Dependency Injection with Dependency Inversion, so for clarification: Dependency Inversion is the general principle, and Dependency Injection is just a method to accomplish the principle.

What we’ve accomplished here is to decouple the formatter from our class. Now we could use a mock IFormatter for unit testing, or we could easily change from using `BinaryFormatter` to for instance a XML or JSON formatter.

This is also known as manual dependency injection, as opposed to using an automated framework for it, such as [NInject](http://www.ninject.org/).

Personally I prefer manual dependency injection, because you’re always in control of what happens and why. However I can see how automated injection could be useful in larger projects, to streamline unit tests etc.

Regardless of which method you choose, I highly recommend remembering this pattern, as it’s simple to implement, and makes both unit testing as well as changing implementations so much simpler.

### Links to the other parts of this series:

1. [Single Responsibility Principle (SRP)](https://steffenskov.github.io/blog/2011/10/20/solid-principles-part-1-single-responsibility-principle.html)
2. [Open/Closed Principle (OCP)](https://steffenskov.github.io/blog/2011/10/25/solid-principles-part-2-openclosed-principle.html)
3. [Liskov’s Substitution Principle (LSP)](https://steffenskov.github.io/blog/2012/02/25/solid-principles-part-3-liskovs-substitution-principle.html)
4. [Interface Segregation Principle (ISP)](https://steffenskov.github.io/blog/2012/09/30/solid-principles-part-4-interface-segregation-principle.html)
5. Dependency Inversion Principle (DIP)