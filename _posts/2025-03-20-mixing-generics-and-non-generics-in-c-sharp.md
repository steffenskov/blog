---
title: 'Mixing generics and non-generics in C#'
layout: post
---

# The problem

I'm coming across this situation more and more recently, so I figured others might be as well:

Suppose you have a concept called `Location`, and you want to support a bunch of different types of `Location`. You may implement an `ILocation` interface and have e.g. `Address : ILocation`, `Coordinate : ILocation` etc.
Now suppose you want to allow the end-user of your application to pick a `Location`, but you want to build the code so it doesn't really care what type of `Location` it is.
This could be a useful design to allow for adding more types of locations in the future, think [Open/Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).


# The simple solution

Now one way to go about this, would be to abstract the service/whatever retrieving locations (be it from an external service, a database or something third):

```
public interface ILocationService
{
	ILocation GetLocationAsync(Guid id, CancellationToken cancellationToken = default);
}
```

Now you can create a bunch of services for your different location types:

```
internal class AddressLocationService : ILocationService
{
	public ILocation GetLocationAsync(Guid id, CancellationToken cancellationToken = default)
	{
		// Logic here
		return address;
	}
}
```

And even use [Keyed Dependency Injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection#keyed-services) if you want to, by adding e.g. a `enum LocationType`.


## The shortcomings of this solution

Wait what? What shortcomings, we're golden right??

Well yes, BUT what if you wanted to take the quality of your code up a notch and do some [Unit Testing](https://en.wikipedia.org/wiki/Unit_testing)?

All your tests of `AddressLocationService` would go something like this:

```
[Fact]
public async Task GetLocationAsync_ValidId_ReturnsAddress()
{
	// Arrange
	var service = new AddressLocationService();

	// Act
	var location = await service.GetLocationAsync(validAddressId);

	// Assert

	var address = Assert.IsType<Address>(location);
	// Actual assertions
}

```
You'd need to explicitly cast (or `Assert`) your results back to the `Address` type every single time.
Sure it's doable, but it's not exactly *nice*

Furthermore what in this design is actually protecting you from inadvertently returning e.g. a `Coordinate` from the service called `AddressLocationService`? Nothing, as there's no strong typing here.

Finally what if you'd like some strong typing of e.g. your ids? (Consider using [StrongTypedId](https://www.nuget.org/packages/StrongTypedId) by yours truly ;-) )
That's not possible either, as your method signature is set in stone by the `ILocationService`.


# [Generics](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/generics) to the rescue, or????

No worries I hear you say, we'll just add the magic sauce of generics to the mix and change our `ILocationService` into an `ILocationService<T>` - that'll surely fix our type issues for the results.
Heck whilst we're at it, why don't we go ahead and handle the strong typing of ids as well with `ILocationService<TId, TLocation>`

Our new interface would look like this:

```
public interface ILocationService<TId, TLocation>
	where TId: StrongTypedGuid<TId>
	where TLocation: ILocation
{
	TLocation GetLocationAsync(TId id, CancellationToken cancellationToken = default);
}
```

And here's a couple of implementations for good measure:

```
public class AddressLocationService : IlocationService<AddressId, Address>
{
	public Address GetLocationAsync(AddressId id, CancellationToken cancellationToken = default)
	{
		// Logic here
		return address; // Won't compile if this isn't an Address
	}
}

public class CoordinateLocationService: ILocationService<CoordinateId, Coordinate>
{
	public Coordinate GetLocationAsync(CoordinateId id, CancellationToken cancellationToken = default)
	{
		// Logic here
		return coordinate; // Again won't compile if this isn't a Coordinate
	}
}

```

This is gonna be AWESOME for unit testing, we'll get the right return types right away, we're protected from mixing id types. Generics saved the day!!

```
Fact]
public async Task GetLocationAsync_ValidId_ReturnsAddress()
{
        // Arrange
        var service = new AddressLocationService();

        // Act
        var address = await service.GetLocationAsync(validAddressId);

        // Assert
        // Actual assertions, as address is already of the right type
}

```


## The shortcomings of this solution

Wait what? I'm still not satisfied??

Well here's the thing, whilst the above design using generics DOES resolve the shortcomings of the initial solution, it also completely *BREAKS* the initial solution and actually doesn't serve the initial purpose. This is because you can no longer simply go keyed dependency injection and grab the relevant `ILocationService` via e.g. `enum LocationType`, as the `ILocationService<TId, TLocation>` is now heavily typed with generics.

Bollocks, if only there was a way to have your cake AND eat it too...

# The mixed solution

Turns out there is, by combining 3 C# language features (some of which are quite lesser known), we can mix the two solutions and get the best of both worlds WITHOUT their drawbacks.
The features we'll be needing are:
- [Explicit Interface Implementation](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation)
- [Default Interface Members](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface#default-interface-members)
- [Shadow Members](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/new-modifier)

So how do we do it? Well we'll be having BOTH `ILocationService` interfaces defined, and let the generic one implement the non-generic one:

```
public interface ILocationService
{
        ILocation GetLocationAsync(Guid id, CancellationToken cancellationToken = default);
}


public interface ILocationService<TId, TLocation> : ILocationService
	where TId: StrongTypedGuid<TId>
        where TLocation: ILocation
{
        new TLocation GetLocationAsync(TId id, CancellationToken cancellationToken = default); // Shadow method, hiding the version from ILocationService

	ILocation ILocationService.GetLocationAsync(Guid id, CancellationToken cancellationToken) // Explicit Interface Implementation
	{
		return this.GetLocationAsync(StrongTypedGuid<TId>.Create(id), cancellationToken); // Default Interface Member
	}
}

```

Ok a lot is happening here, let's go over it:

- We HIDE the "simple" `GetLocationAsync` method on our generic interface, so anyone implementing the generic interface ONLY exposes the strongly typed version.
- We Explicitly Implement the "simple" `GetLocationAsync` method using the Default Interface Member feature, to ensure any classes implementing the generic interface won't have to implement *both* methods. Our default implementation simple forwards the method call to the strong-typed one.

Note: This only works the the types are compatible, meaning your want a [Covariant](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/) return type and a [Contravariant](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/) input type.
In our case we ensure Covariance of the return type via a generic constraint: `where TLocation: ILocation`, and we actually forego the Contravariance requirement by means of the `StrongTypedGuid` type, which is capable of creating a more specific type from a lesser one using it's `Create` method.
If you're not using a library that supports this, you cannot have stronger typed input arguments!


Ok that sounds somewhat well and dandy, but how would our `AddressLocationService` and unit testing look?

Well they'll look exactly like the generic solution above:

```
public class AddressLocationService : IlocationService<AddressId, Address>
{
        public Address GetLocationAsync(AddressId id, CancellationToken cancellationToken = default)
        {
                // Logic here
                return address; // Won't compile if this isn't an Address
        }
}
```

```
Fact]
public async Task GetLocationAsync_ValidId_ReturnsAddress()
{
        // Arrange
        var service = new AddressLocationService();

        // Act
        var address = await service.GetLocationAsync(validAddressId);

        // Assert
        // Actual assertions, as address is already of the right type
}

```

But what about our use case with Keyed DI? No worries, just inject the services as `ILocationService`:

```
	services.AddKeyedSingleton<ILocationService, AddressLocationService>(LocationTypes.Address);
	services.AddKeyedSingleton<ILocationService, CoordinateLocationService>(LocationTypes.Coordinate);
```

And you'll be getting the "simple" `ILocationService` for those situations that require the utmost abstraction level.


In other words: You're getting your cake AND you can eat it too, bon appetit :-)
