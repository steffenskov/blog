---
title: 'SOLID principles - Part 1: Single Responsibility Principle'
layout: post
---

This is the first of a five part series, where I go over the 5 principles which make up the [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles by [Robert C. Martin](http://en.wikipedia.org/wiki/Robert_C._Martin).

The first one is S: [Single Responsibility Principle](http://en.wikipedia.org/wiki/Single_responsibility_principle) (SRP)

This principle states that a class should only one have responsibility, which is usually interpreted as having a single “reason to change”.

Consider the following class:

```

public class RevenueCalculator
{
	public decimal CalculateRevenueForCustomer(Customer customer)
	{
	}

	public string FormatRevenueReportForCustomer(Customer customer)
	{
	}
}
```

Clearly this seems to nicely encapsulate revenues… BUT it has 2 reasons to change:

1\. The logic behind calculating revenues changes.

2\. The format of the report changes.

In fact this class even mixes UI / Cosmetic functionality with business logic – a big no-go as well.

The right thing to do here, would be to put FormatRevenueReportForCustomer in its own class.

So you’d end up with something like this:

```

public class RevenueCalculator
{
	public decimal CalculateRevenueForCustomer(Customer customer)
	{
	}
}

public class RevenueReportFormatter
{
	public string FormatRevenueReportForCustomer(Customer customer)
	{
	}	
}
```

Now each class has only one reason to change.

Whilst this example is a bit simplified, I think it sums up the principle quite well.

If you abide by the Single Responsibility Principle, you’ll usually end up with a lot of small classes.

Some of you might dislike this, but consider the fact that each class encapsulates just one responsibility, and you end up with very easily maintainable code.

I for one do not miss the days of looking through a 2000 lines class 😀

Furthermore not mixing “inappropiate” functionality together, means the classes are easier to test and debug, and will end up being more robust / “solid”. (pun intended :-D)

Finally do note that these principles are guidelines, they’re not meant to be enforced a 100% (some people get all “fanatic” about these things).  
Use common sense when employing any principle (or pattern for that matter).

If you want to read more about the Single Responsibility Principle, Wikipedia has a decent article on it: [http://en.wikipedia.org/wiki/Single\_responsibility\_principle](http://en.wikipedia.org/wiki/Single_responsibility_principle)  
And Robert C. Martin has a very thorough article here: <http://www.objectmentor.com/resources/articles/srp.pdf>

### Links to the other parts of this series:

1. Single Responsibility Principle (SRP)
2. [Open/Closed Principle (OCP)](https://steffenskov.github.io/blog/2011/10/25/solid-principles-part-2-openclosed-principle.html)
3. [Liskov’s Substitution Principle (LSP)](https://steffenskov.github.io/blog/2012/02/25/solid-principles-part-3-liskovs-substitution-principle.html)
4. [Interface Segregation Principle (ISP)](https://steffenskov.github.io/blog/2012/09/30/solid-principles-part-4-interface-segregation-principle.html)
5. [Dependency Inversion Principle (DIP)](https://steffenskov.github.io/blog/2013/11/24/solid-principles-part-5-dependency-inversion-principle.html)