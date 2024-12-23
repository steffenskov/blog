---
title: 'SOLID applied - to REST API endpoints'
layout: post
---

I see an approach to REST APIs rather often, that isn’t exactly ideal, so here I want to show how to use SOLID to analyze the intended solution and hopefully reach the conclusion, that something else might be better.

## Our example

For this example let’s say we have a Task management API, and our Task view model looks like this in C#: (Disregard the implementation of the custom types, that’s irrelevant to this article)

```
public class TaskViewModel
{
  public TaskId Id { get; }
  public string Title { get; }
  public TaskType Type { get; }
  public UserId AssignedTo { get; }
}
```

Our initial API has just one endpoint:

```
// GET /Task
public async Task<ActionResult<IList<TaskViewModel>>> GetTasksAsync()
{
...
}
```

Which returns `TaskViewModel[]` – all the tasks that exist in our database.

So far so good, now a new requirement enters the picture: The requirement to filter the data from the API on both `Type` and `AssignedTo`. We’d want to add the filter values to our API endpoint, for the filtering to take place. Since we don’t know the exact size of our request, as there could be \*many\* filters. We’ll switch over to a POST method and use the request body for the values. I like to add a suffix to the route to show this is \*not\* our CREATE endpoint (which usually would be `POST /Task`)

```
// POST /Task/filtered
public async Task<ActionResult<IList<TaskViewModel>>> GetFilteredTasksAsync(TaskFilterModel model)
{
...
}

...

public record TaskFilterModel(TaskType[] Types, UserId[] AssignedTo);
```

Phew that was a good chunk of code added, but now surely we’re set right? Well… No. The application calling our API has no way of knowing what valid values for `TaskType` and `UserId` might be. It’s going to need that from the API to give the end-user some sort of list to pick from.

## The problem

And we’re finally at the topic of this article: How to model the API for retrieving those lists.  
What I often see is a single endpoint looking somewhat like this:

```
// GET /Task/filters
public async Task<ActionResult<TaskFilterViewModel>> GetFiltersAsync()
{
...
}

...

public class TaskFilterViewModel
{
  public UserId[] AssignableUserIds { get; }
  public TaskType[] TaskTypes { get; }
}
```

The application will then call this single endpoint and have everything it needs to immediately render two lists for the end-user to select from.

At first glance this seems fine, heck it’s probably the first solution many come up with. But let’s see what happens if we apply the SOLID principles to this particular endpoint.

**Single Responsibility Principle**  
Does this method have exactly one reason to change? No not quite, as the resulting model contains elements of both `UserId` and `TaskType`. Any changes to `UserId` would probably affect this method, and the same goes for `TaskType`.

**Open/Closed Principle**  
Is the code open for extension and closed for modification? Not at all, in fact one might argue the opposite. If we were to introduce a new property on our `TaskViewModel`: `DueDate` and we’d want to filter by date, we’d have to change both the `TaskFilterViewModel` and the logic of the actual API method to retrieve the data and model them onto the result. I’d argue this is the exact opposite of Open/Closed as there’s no way to extend it, and any need requirement \*needs\* modification of this.

**Liskov’s Substitution Principle**  
Doesn’t really apply here, as there’s no inheritance. We’ll skip along.

**Interface Segregation Principle**  
No interfaces to see here, \*however\* the principle can, at a bit of stretch, be applied to thick models as well. So suppose our application at some point want to show a statistic of how many `Task` exists for each `TaskType`. If the application would want to populate a dropdown list with the available `TaskType` it’d have to call this endpoint and receive both `TaskType` and `AssignableUserId` lists. So I’d actually say we’re in violation of this principle as well. Even though we’re not using an `interface` directly.

**Dependency Inversion Principle**  
This doesn’t strictly apply here either I’d say, though it could become relevant for a more sophisticated solution.

Now having been through SOLID, we’ve learnt this solution is in direct violation of the first two principles and somewhat in violation of the 4th. To me this is a really strong hint that this solution will cause maintenance headaches in the future.

Now one may actually argue there’s already an easy giveaway for this, and that’d be the endpoint route / URL. It’s not \*that\* apparent here, however in real life these endpoints tend to grow over time and envelop more and more separate domains – having a single URL for that becomes a naming nightmare, so that’s another thing to look out for as a “something might not be great here” hint.

## The solution

“Ok ok you’ve convinced me, how do I fix this?” Luckily this is rather easy: separate your concerns and everything else will follow suit.  
This means rather than having a single REST endpoint for your filters, you create two:

```
// GET /Task/assignableUserIds
public async Task<ActionResult<IList<SelectableItemViewModel<UserId>>>> GetAssignableUserIdsAsync()
{
...
}

// GET /Task/taskTypes
public async Task<ActionResult<IList<SelectableItemViewModel<TaskType>>>> GetTaskTypesAsync()
{
...
}

...

public class SelectableItemViewModel<TValue>
{
  public string Title { get; }
  public TValue Value { get; }
}
```

Now we have TWO methods for retrieving the valid values of each type in our application. I even snuck in a unified ViewModel for items to present in lists for selection (e.g. a dropdown) – your frontend/application developers will love you for providing consistent modelling, so there’s a freebie right there.

How does this small change solve everything? Let’s dive into the affected SOLID principles again:

**Single Responsibility Principle**  
Each method now has a single reason to change: Whatever subset of data it deals with. So if something changes with `TaskType` the code delivering `AssignableToUserIds` is no longer affected.

**Open/Closed Principle**  
This time I’d say we got the principle right – any new requirements can be implemented with yet another method and there’s no need to modify existing code whatsoever.

**Interface Segregation Principle**  
Our result type is now the rather simple `SelectableItemViewModel` – it doesn’t contain any superfluous data if for instance our application needs just a list of `TaskType` in the future.

## Surely that can’t be all?

Well it kinda of is, however there \*are\* of course arguments that exist around the initial solution. One I’ve heard often is performance. The argument is that sending multiple HTTP requests instead of one is slower. And whilst this is true, you have to look at the broader picture here.

The initial endpoint would go to the database and fetch two sets of data, then return it. If we go into Big O-notation on this, I’d say the entire HTTP call stack consists of:

- 1 HTTP call
- 2 Database invocations, each incurring at least `O(log(n))` is binary search is possible via indexing, otherwise `O(n)` for scanning the entire data collection.

Let’s assume indexing is in place, and the total cost becomes `1+2+(2*log(n))` – reducing this as per Big O-notation rules (getting rid of constants if anything scales with `n`), we end up with `log(n)`.

Let’s consider the newer, SOLID, approach:

- 2 HTTP calls
- 2 Database invocations still (one per HTTP call)

The total cost becomes `2+2+(2*log(n))` – a slightly higher constant part, however as we’re reducing this we end up again at `log(n)`.

Now you may argue “But there IS a difference in real life”, and you’d be right. However for \*most\* applications, utmost performance isn’t a goal in itself. \***Sufficient**\* performance is and there’s a huge difference between the two. Performance further tends to become more important, as your response times increase. The main driver in the above for increasing response times IS the dataset size. Our flat HTTP overhead is just that: flat. It doesn’t matter if we’re getting 10k items, or 1.  
As such when you \*need\* performance out of the above solution, the extra HTTP call isn’t going to hurt you – your data size is.

Therefore I’d argue the improved maintainability outweighs any minor performance gains you stand to gain my muddling up your codebase.

This is in fact an excellent case of **premature optimization**, where you jump into the “It needs to be FAST” rabbit hole, before even knowing if there is a problem. Be very wary about that, as increase performance almost always comes at the cost of added complexity (consider caching for instance, it speeds up pretty much anything immensely, but you suddenly have data coherency to consider, cache invalidation, etc.)  
The added complexity is even worse in a distributed system, and with many going the microservices route everything is essentially a distributed system nowadays.

I hope this sheds some light on how and where to utilize design principles such as SOLID, to determine early in your development process, whether you’re on the right track or not. It may seem cumbersome initially, however I promise you it \*will\* become second nature if you stay at it.