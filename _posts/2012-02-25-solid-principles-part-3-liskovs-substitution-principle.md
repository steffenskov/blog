---
title: "SOLID principles - Part 3: Liskov's Substitution Principle"
layout: post
---

This is the third of a five part series, where I go over the 5 principles which make up the [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles by [Robert C. Martin](http://en.wikipedia.org/wiki/Robert_C._Martin).

The third one is L: [Liskov’s Substitution Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle) (LSP)

This principle states that if S is a subtype of T, then objects of type T may be replaced with objects of type S without altering the desirable properties of the program (includes correctness, task performed, etc.)

In other words, if you make a subclass it should not alter its parents behavior significantly.

There are a few rules to follow in this regard:

- [Contravariance of method arguments in the subtype. (The arguments may be wider than the parent’s method required, e.g. going from float to double)](#contravariance)
- [Covariance of return types in the subtype. (The return types may be narrower than the parent’s return types, e.g. going from double to float)](#covariance)
- [No new exceptions should be thrown, unless the exceptions are subtypes of exceptions thrown by the parent.](#exceptions)
- [Preconditions cannot be strengthened in the subtype. (You cannot require more than the parent)](#preconditions)
- [Postconditions cannot be weakened in the subtype. (You cannot guarantee less than the parent)](#postconditions)
- [Invariants must be preserved in the subtype.](#invariants)
- [History Constraint – the subtype must not be mutable in a way the supertype wasn’t. For instance MutablePoint cannot inherit ImmutablePoint without violating the History Constraint (as it allows mutations which its supertype didn’t)](#history)

Phew quite a long list, how about some examples of each rule ?  
Notice that in C# you cannot directly violate the Covariance and Contravariance requirements, the code will simply not compile.

I’ll show this in the following example: (The code below will NOT compile)

### <a name="contravariance">Contravariance of method arguments</a>

```

public class Vehicle
{
	public string Name { get; set; }
}

public class Car : Vehicle
{
}

public class Bike : Vehicle 
{
}

public class SuperType
{
	public virtual string GetName(Car vehicle)
	{
		return car.Name;
	}
}

public class LSPLegalSubType : SuperType
{
	public override string GetName(Vehicle vehicle) // This is legal due to the Contravariance requirement - you're allowed to widen the argument type
	{
		return base.GetName((Car)age);
	}
}

public class LSPIllegalSubType : SuperType
{
	public override string GetName(Bike vehicle) // this is illegal due to the Contravariance requirement
	{
		return base.GetName((Car)age);
	}
}
```

Now like I said the above code will not compile, but it does show quite clearly what the Contravariance requirement means.

The requirement is there to prevent exceptions when substituting the supertype with the subtype.  
Take a look at this example:

```

SuperType type = new LSPLegalSubType();
type.GetName(new Car()); // No problem, the LSPLegalSubType will accept anything that's a Vehicle, which a Car is.
```

As opposed to this:

```

SuperType type = new LSPIllegalSubType();
type.GetName(new Car()); // This would break if it had compiled, as LSPIllegalSubType only accepts a Bike, and a Car is not a Bike.
```

And that’s all there is to the first requirement.

### <a name="covariance">Covariance of return types</a>

```

public class SuperType
{
	public virtual int GetDaysSinceLastLogin(User user)
	{
		return int.MaxValue;
	}
}

public class LSPLegalSubType : SuperType
{
	public override short GetDaysSinceLastLogin(User user)
	{
		return short.MaxValue; // Legal because it will always fit into an int
	}
}

public class LSPIllegalSubType : SuperType
{
	public override long GetDaysSinceLastLogin(User user)
	{
		return long.MaxValue; // Illegal because it will not surely fit into an int
	}
}
```

This code will also not compile, but again it shows exactly what the Covariance requirement means.

Suppose you called `int days = GetDaysSinceLastLogin(user)` and the subtype returned `long.MaxValue`. This would clearly throw an exception.  
On the other hand `short.MaxValue` will easily fit in an `int`, so no problems there.

### <a name="exceptions">No new exceptions should be thrown</a>

```

public class CustomArgumentNullException : ArgumentNullException
{
	public CustomArgumentNullException(string paramName)
		: base(paramName)
	{
	}
}

public class SuperType
{
	public virtual int GetDaysSinceLastLogin(User user)
	{
		if (user == null)
			throw new ArgumentNullException("user");
		return int.MaxValue;
	}
}

public class LSPLegalSubType : SuperType
{
	public override int GetDaysSinceLastLogin(User user)
	{
		if (user == null)
			throw new CustomArgumentNullException("user"); // Legal because "CustomArgumentNullException" inherits "ArgumentNullException"
		return int.MaxValue;
	}
}

public class LSPIllegalSubType : SuperType
{
	public override int GetDaysSinceLastLogin(User user)
	{
		if (user == null)
			throw new Exception("User is null"); // Illegal because a catch on the SuperType's "ArgumentNullException" will not catch "Exception"
		return int.MaxValue;
	}
}
```

Once again I have a legal and an illegal example – the legal one is legal because `CustomArgumentNullException` inherits `ArgumentNullException`. This means a `catch(ArgumentNullException)` clause will also catch my `CustomArgumentNullException` thus it doesn’t break anything.

On the other hand `Exception` doesn’t inherit `ArgumentNullException` so a `catch(ArgumentNullException)` clause will not catch it, thereby breaking any logic relying on catching that exception.

### <a name="preconditions">Preconditions cannot be strengthened</a>

A [precondition](http://en.wikipedia.org/wiki/Precondition) is a condition which must hold true BEFORE executing a method.  
Usually preconditions are concerned with the arguments to a method, or the state of the object.

```

public class SuperType
{
	public virtual string FormatName(string name)
	{
		if (string.IsNullOrEmpty(name))
			throw new ArgumentException("name cannot be null or empty", "name");
		return name;
	}
}

public class LSPLegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		if (name == null)
			throw new ArgumentNullException("name");
		return name;
	}
}

public class LSPIllegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		if (string.IsNullOrEmpty(name) || name.Length 
<p>As can be seen the <code>SuperType</code> has a precondition of <code>name</code> not being null or empty.</p>
<p>The legal subtype only requires name to not be null, which is actually a weakening of the precondition. This is perfectly legal as any valid arguments to the supertype will also be valid in the subtype. (Do also notice I use <code>ArgumentNullException</code>, which might seem like invalid due to the previous constraint about exceptions. However <code>ArgumentNullException</code> inherits <code>ArgumentException</code> so this is perfectly fine)</p>
<p>The illegal subtype however strengthens the precondition to also require the name's length to be at least 4 characters long. This means the value "Bob", which is a valid argument for the supertype, will cause an exception in the subtype. That's no good!</p>
<p>Oh and whilst talking about conditions, you might want to look into using <a href="http://msdn.microsoft.com/en-us/library/dd264808.aspx" target="_blank">Code Contracts</a> instead of writing your own conditions.</p>
<p>Here's an example using Code Contracts for a precondition:</p>

public class SuperType
{
	public virtual string FormatName(string name)
	{
		Contract.Requires<ArgumentException>(!string.IsNullOrEmpty(name));
		return name;
	}
}

public class LSPLegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		Contract.Requires<ArgumentNullException>(name != null);
		return name;
	}
}

public class LSPIllegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		Contract.Requires<ArgumentException>(!string.IsNullOrEmpty(name));
		Contract.Requires<ArgumentException>(name.Length >= 4);
		return name;
	}
}

<p>Notice how I put the two requirements (preconditions) of the illegal subtype on their own lines ? This is primarily to increase readability - you could go with a single line:

<code>Contract.Requires<argumentexception>(!string.IsNullOrEmpty(name) && name.Length >= 4);</argumentexception></code>

However this is less easy to read, AND it gives you more cluttered information from the static checker.</p>
<p>So I highly suggest going with one line per requirement.</p>
<h3><a name="postconditions">Postconditions cannot be weakened</a></h3>
<p>A <a href="http://en.wikipedia.org/wiki/Postcondition" target="_blank">postcondition</a> is a condition which must hold true AFTER running a method.</p>
<p>Postconditions usually concern the result of a method, or the state of the object.</p>

public class SuperType
{
	public virtual string FormatName(string name)
	{
		Contract.Ensures(!string.IsNullOrEmpty(Contract.Result<string>()));
		return name;
	}
}

public class LSPLegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		Contract.Ensures(!string.IsNullOrWhiteSpace(Contract.Result<string>()));
		return "a" + name;
	}
}

public class LSPIllegalSubType : SuperType
{
	public override string FormatName(string name)
	{
		Contract.Ensures(Contract.Result<string>() != null);
		return name;
	}
}

<p>Here I'm using <code>Contract.Ensures</code> for my postconditions. The supertype ensures the result is never null or empty.</p>
<p>My valid subtype strengthens this postcondition to also ensure it's never just whitespace. This is perfectly valid to do, because the subtype can now only return a subset of what the supertype could return. This effectively means that any code relying on the result being not null or empty, will always be satisfied.</p>
<p>The invalid subtype weakens the postcondition to only ensure the result isn't null. Therefore this subtype may actually return empty strings, which the supertype couldn't.

If any code relies on the method NOT returning empty strings, this subtype would cause that code to break.</p>
<h3><a name="invariants">Invariants must be preserved</a></h3>
<p>An <a href="http://en.wikipedia.org/wiki/Invariant_(computer_science)" target="_blank">invariant</a> is a condition which must hold true BOTH before and AFTER running a method.

Usually invariants are concerned with the state of the object they're defined on.

This is actually one of the more difficult constraints to fulfill, because invariants are often not explicitly defined in code. Many developers go with the "you just have to know this invariant exists" approach. (Which is bad IMHO)

So to comply with this constraint, I suggest starting by adding the proper invariants to the supertype.

Since invariants are inherited using Code Contracts, this constraint becomes a lot simpler, once the supertype implements these properly.</p>

public class SuperType
{
	public string Name { get; set; }
	public void ChangeName(string newName)
	{
		this.Name = newName;
	}

	[ContractInvariantMethod]
	protected virtual void ObjectInvariant()
	{
		Contract.Invariant(!string.IsNullOrEmpty(Name));
	}
}

public class LSPLegalSubType : SuperType
{
	protected override void ObjectInvariant()
	{
		base.ObjectInvariant();
		Contract.Invariant(Name.Length > 3);
	}
}

public class LSPIllegalSubType : SuperType
{
	protected override void ObjectInvariant()
	{
		Contract.Invariant(Name != null);
	}
}

<p>This example is somewhat "stupid", because normally you'd not make your <code>ObjectInvariant</code> method virtual (or protected for that matter, it'd be private).

But for the example, it serves the purpose.

The constraint simply says invariants must be preserved, and this obviously happens in the legal subtype, since it calls <code>base.ObjectInvariant()</code>.

Likewise the illegal subtype isn't legal, because it fails to call the base method, and its own invariant isn't equal to the supertype invariant.</p>
<p>Like I said, a quite crude example. But with Code Contracts this constraint is really quite hard to screw up.</p>
<p>If, on the other hand, you didn't use Code Contracts, you might've had an example like this:</p>

public class InvariantException : Exception
{
	public InvariantException(string message)
		: base(message)
	{
	}
}

public class SuperType
{
	public string Name { get; set; }
	public virtual void ChangeName(string newName)
	{
		VerifyInvariants();
		this.Name = newName;
		VerifyInvariants();
	}

	protected void VerifyInvariants()
	{
		if (string.IsNullOrEmpty(Name))
			throw new InvariantException("Name");
	}
}

public class LSPLegalSubType : SuperType
{
	public override void ChangeName(string newName)
	{
		VerifyInvariants();
		this.Name = newName;
		VerifyInvariants();
	}
}

public class LSPIllegalSubType : SuperType
{
	public override void ChangeName(string newName)
	{
		VerifyInvariants();
		this.Name = newName;
		// Forgot to check Name AFTER method has been run, invariants is thus not preserved
	}
}

<p>Not only is the code a lot clumsier than Code Contracts, it's also a lot easier to forget to check an invariant.

Most likely the clumsiness of this, is why many developers don't implement invariants in their code.</p>
<p>On a final sidenote, Invariants are most often found in <a href="http://en.wikipedia.org/wiki/Domain_driven_design" target="_blank">Domain Driven Design</a>, where the domain model must be valid at all times.</p>
<h3><a name="history">History Constraint</a></h3>

public class SuperType
{
	public string Name { get; private set; }
	public int Age { get; set; }
	public SuperType(string name, int age)
	{
		Name = name;
		Age = age;
	}
}

public class LSPLegalSubType : SuperType
{
	public void IncrementAge()
	{
		Age++;
	}
}

public class LSPIllegalSubType : SuperType
{
	public void ChangeName(string newName)
	{
		var propertyType = base.GetType().GetProperty("Name").SetValue(this, newName);
	}
}

<p>Quite a simple example this one, but then again the constraint is also quite simple.</p>
<p>The supertype doesn't allow mutation of <code>Name</code> (since it has a private setter), but does allow mutation of <code>Age</code> through a public setter.</p>
<p>The legal subtype adheres to this, as it only adds an <code>IncrementAge</code> method, which only mutates <code>Age</code>.</p>
<p>On the other hand the illegal subtype offers a <code>ChangeName</code> method, which mutates the otherwise immutable property Name using Reflection.</p>
<p>Although this requirement is easy to understand, it can be troublesome to conform to, if your supertype is complex (Since figuring out all non-mutable properties can be quite cumbersome, unless they like here are marked with private setters)

On the other hand if the supertype DOES use private setters, it's quite easy to figure out which properties not to mutate, as you have to use Reflection or similar hacks to alter them. (Lesson to be learned: Don't use Reflection to modify private fields or properties)</p>
<p>Phew longest blog post yet, and quite delayed as well.

Feel free to leave a comment if I missed anything 🙂</p>
<h3>Links to the other parts of this series:</h3>
<ol>
<li><a href="https://steffenskov.github.io/blog/2011/10/20/solid-principles-part-1-single-responsibility-principle.html">Single Responsibility Principle (SRP)</a></li>
<li><a href="https://steffenskov.github.io/blog/2011/10/25/solid-principles-part-2-openclosed-principle.html">Open/Closed Principle (OCP)</a></li>
<li>Liskov's Substitution Principle (LSP)</li>
<li><a href="https://steffenskov.github.io/blog/2012/09/30/solid-principles-part-4-interface-segregation-principle.html">Interface Segregation Principle (ISP)</a></li>
<li><a href="https://steffenskov.github.io/blog/2013/11/24/solid-principles-part-5-dependency-inversion-principle.html">Dependency Inversion Principle (DIP)</a></li>
</ol>
```