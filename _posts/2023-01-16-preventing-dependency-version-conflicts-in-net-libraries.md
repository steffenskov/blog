---
title: 'Preventing dependency version conflicts in .Net libraries'
layout: post
---

## What is the issue?

Dependency version conflicts happen when you have multiple libraries referencing the same dependency, but with different version numbers.

This is in fact quite common, so much so that Microsoft has done a lot to try and automatically resolve the issue. You might even be affected by it without knowing.

The way to determine if you’re affected, is to look at warnings in your solution as a result of a build. If you have any warnings about different versions of a DLL, you’re affected.

Like I mentioned .Net will automatically attempt to deal with this, and this works quite a lot of the time. However when it **doesn’t** work, you’ll get **runtime exceptions** about failing to load one or more Assemblies.

As this rears its ugly head runtime, I’d advise attempting to remove the conflicts completely, rather than relying on the automatic conflict resolution.

## How does it happen?

This is most easily explained with an example. Suppose you have the following setup:

“MyApplication” references “BrandNewStockBrokerService” and “YeOldWeatherService”. Both services deal with HTTP communication and references “System.Net.Http”, however “BrandNewStockBrokerService” references version 4.2.0 and “YeOldWeatherService” references version 4.0.0. (This is actually one of the more common version conflicts in the old .Net Framework 4.x)

The result of this is that “MyApplication” knows it needs “System.Net.Http” however it’s been told two different version numbers.

What will most commonly happen is it’ll resolve it by using version 4.2.0 and cross its fingers that it’s compatible with 4.0.0 (there’s no guarantee here mind you, and if it’s not compatible guess what? **Runtime exceptions**)

## What can be done about it?

Sadly if you’re the maintainer of “MyApplication”, you’re actually quite screwed in terms of fixing the issue. The problem can only be solved in your application by an update to “YeOldWeatherService”, where it too starts using version 4.2.0 of “System.Net.Http” (or a downgrade to version 4.0.0 by “BrandNewStockBrokerService”)

As both services are likely outside your influence, your options are very limited. In fact so much so, that the most common way of solving this, is to completely replace one of the services you rely on and rewriting the code that interacted with it… Quite cumbersome :-/

## How can we prevent it in the first place?

This is actually the main purpose of this post: How you as a library developer, can prevent others from getting into trouble when using your library.

The answer is simple to explain, but can lead to a bit of overhead for the consumers of your library. It stems from the “D” in the SOLID principles: “[Dependency inversion principle](https://steffenskov.github.io/blog/2013/11/24/solid-principles-part-5-dependency-inversion-principle.html "Dependency inversion principle")“, which can be boiled down to: “Depend on abstractions, not on concretions”.

An example is in order here I think. Suppose you’re building “YeOldWeatherService” from before, and you need to perform HTTP requests to some weather API. You’ve build this very bare-bone service:

```
public class WeatherService
{
	private HttpClient _client;

	public WeatherService()
	{
		_client = new();
	}
	
	public IEnumerable<Weather> GetWeatherAsync()
	{
		var response = await _client.GetAsync("someUri");
		var json = await response.Content.ReadAsStringAsync();
		// Further implementation
	}
}
```

This very much depends on the concrete HttpClient class, in fact it’s creating it by itself. So let’s try again with a bit of “Dependency Inversion Principle” in mind:

```
public class WeatherService
{
	private HttpClient _client;

	public WeatherService(HttpClient client)
	{
		_client = client;
	}
}
```

This is better in terms of testability and so forth (since we can now mock the HttpClient), however it didn’t really solve our hard coupled dependency to the “System.Net.Http”. Furthermore it wouldn’t even help us if an interface for the HttpClient class existed in the “System.Net.Http” library, as we’d still depend on the library for the interface definition.

If only there was a way in which we could define an interface ourselves, and depend on that instead… This is where a design pattern comes in to the rescue: [Facade pattern](https://en.wikipedia.org/wiki/Facade_pattern)

The Facade pattern is usually used to simplify complexity into an “easy-to-use” interface. Here we’ll do exactly that, albeit with a slightly different goal in mind: Decoupling.

The only method we’re using from the HttpClient is “GetAsync”, so surely an interface like this could work just as well, right?

```
public interface IHttpClientFacade
{
	public Task<HttpResponseMessage?> GetAsync(string uri);
}
```

Well not quite, you see the HttpResponseMessage class also lives inside “System.Net.Http”, as such our Facade needs to be more “Facade-y” and encapsulate the entire Get-And-Read flow like this:

```
public interface IHttpClientFacade
{
	public Task<string?> GetBodyAsync(string uri);
}
```

Now we just need to change our WeatherService to accept an instance of IHttpClientFacade instead and we’re golden:

```
public class WeatherService
{
	private IHttpClientFacade _client;

	public WeatherService(IHttpClientFacade client)
	{
		_client = client;
	}

	public IEnumerable<Weather> GetWeatherAsync()
	{
		var json = await _client.GetBodyAsync("someUri");
		// Further implementation
	}
}
```

Now we’re ready to completely remove the dependency on the “System.Net.Http” library, and anybody who uses our WeatherService won’t have any hassles, or will they?

Well yes, by depending solely on an abstraction, the responsibility of the concretion now lies with whoever uses our library. As such anyone who wants to use the “YeOldWeatherService” library will need to implement a concrete Facade themselves, like this:

```
public class HttpClientFacade : IHttpClientFacade
{
    private readonly HttpClient _client;

    public HttpClientFacade(HttpClient client)
    {
        _client = client;
    }
    
    public async Task<string?> GetBodyAsync(string url)
    {
        var response = await _client.GetAsync(url);

        return await response.Content.ReadAsStringAsync();
    }
}
```

This does provide the consumer with the option to swap out the HttpClient with another client, like e.g. the aging WebClient, RestSharp or something third. However it’s also an overhead the consumer needs to deal with. As such this approach for decoupling should primarily be used for libraries that are very “version volatile” or otherwise known for causing version conflicts.

Finally you might have a situation where you’d ideally like to create new instances of your Facade inside your library yourself. This is completely legitimate, and actually easily solved by using another pattern: the [Factory pattern](https://en.wikipedia.org/wiki/Factory_(object-oriented_programming))

Here we’ll do the exact same thing: provide a Factory interface and let the consumer implement the concretion:

```
public interface IHttpClientFacadeFactory
{
    IHttpClientFacade CreateFacade();
}
```

Our consumer will then have to implement this as well in a fashion similar to this:

```
public class HttpClientFacadeFactory : IHttpClientFacadeFactory
{
    private readonly HttpClient _client;

    public HttpClientFacadeFactory(HttpClient client)
    {
        _client = client;
    }
    
    public IHttpClientFacade CreateFacade()
    {
        return new HttpClientFacade(_client);
    }
}
```

And by injecting this concrete factory into our library, we can now create instances of a class we don’t even know about.

In fact this approach is what I’m using in my [Dapper.DDD.Repository](https://github.com/steffenskov/Dapper.DDD.Repository) library, to avoid locking it to a specific version of Dapper. This means anyone using that library of mine, can feel free to upgrade or even downgrade Dapper if they want to. AND there’ll be no version conflicts because of me.

I hope this (somewhat lengthy) will help you develop dependency-less libraries yourself or at the very least can inspire to how common design patterns can be tweaked ever so slightly to solve a lot more problems in a well-known fashion.