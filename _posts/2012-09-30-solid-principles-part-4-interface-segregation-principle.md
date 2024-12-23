---
title: 'SOLID principles - Part 4: Interface Segregation Principle'
layout: post
---

This is the fourth of a five part series, where I go over the 5 principles which make up the [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles by [Robert C. Martin](http://en.wikipedia.org/wiki/Robert_C._Martin).

The fourth one is I: [Interface Segregation Principle](http://en.wikipedia.org/wiki/Interface_segregation_principle) (ISP)

This principle states that interfaces should only contain the absolutely required properties/methods needed.  
If an interface contains properties/methods the client code does not need, it should be split into more smaller interfaces.

This principle is really one of the simpler ones in my opinion.

We’ll take a quick example:

```

public interface IVehicle
{
  string Name { get; set; }
  string Manufactorer { get; set; }
}
```

This is all nice and clean, but what if I want to add information about boot size, engine size, and fuel ?

The obvious choice is to simply add these:

```

public interface IVehicle
{
  string Name { get; set; }
  string Manufactorer { get; set; }
  int BootSizeInLitres { get; set; }
  string Engine { get; set; }
  string FuelType { get; set; }
}
```

BUT what if my client code needs to handle cars and bicycles ? Most bicycles don’t have a boot, an engine or uses fuel.  
Clearly this interface contains information my client doesn’t need.

The solution is to split the interface up as follows:

```

public interface IVehicle
{
  string Name { get; set; }
  string Manufactorer { get; set; }
}

public interface IBicycle : IVehicle
{
}

public interface ICar : IVehicle
{
  int BootSizeInLitres { get; set; }
  string Engine { get; set; }
  string FuelType { get; set; }
}
```

And that’s pretty much all there is too it, simply enough 🙂

There a few simple rules of thumb you can use to detect these “bloated” interfaces:

- You have an implementation where a property or method throws NotImplementedException
- You have an implementation where a property or method returns a default value (e.g. 0, “”, etc.)
- You have an implementation where a void method does nothing at all (empty method body)

If you notice any of these three, they’re a good indicator your interfaces contains more than needed. This may be a good time to consider splitting up the interfaces into multiple smaller interfaces.

Finally as always with these principles, keep in mind they’re simply guidelines. There may be perfectly valid reasons to have one or more of the 3 points above in your code – just make sure you understand why 🙂

### Links to the other parts of this series:

1. [Single Responsibility Principle (SRP)](https://steffenskov.github.io/blog/2011/10/20/solid-principles-part-1-single-responsibility-principle.html)
2. [Open/Closed Principle (OCP)](https://steffenskov.github.io/blog/2011/10/25/solid-principles-part-2-openclosed-principle.html)
3. [Liskov’s Substitution Principle (LSP)](https://steffenskov.github.io/blog/2012/02/25/solid-principles-part-3-liskovs-substitution-principle.html)
4. Interface Segregation Principle (ISP)
5. [Dependency Inversion Principle (DIP)](https://steffenskov.github.io/blog/2013/11/24/solid-principles-part-5-dependency-inversion-principle.html)