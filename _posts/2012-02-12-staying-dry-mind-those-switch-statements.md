---
title: 'Staying DRY: Mind those switch statements'
layout: post
---

As a developer you probably know the DRY abbreviation already: Dont Repeat Yourself.  
This is one of the better known principles, and it’s fairly easy to comprehend too: Don’t implement the same logic twice.

However one place where most people slip with DRY, is the switch statement.

I’ll present an example. Suppose we have a `ReportGenerator`, which uses a `ReportSettings` class to retrieve necessary information for the report:

```

public enum ReportType
{
	Invoices,
	Customers
}

public class ReportSettings
{
	public string GetReportHeader(ReportType reportType)
	{
		switch (reportType)
		{
			case ReportType.Customers: return "List of customers";
			case ReportType.Invoices: return "Invoices";
			default: throw new ArgumentException(string.Format("Unknown reportType '{0}'", reportType));
		}
	}
}
```

As can be seen it holds a single method: `GetReportHeader`. This in turn takes a `ReportType `as argument, and returns the proper header.  
There’s nothing really wrong here – the code is DRY at the moment.

Suppose we’ll need another method to get a footer for the report – many developers would be tempted to do this:

```

public class ReportSettings
{
	public string GetReportHeader(ReportType reportType)
	{
		switch (reportType)
		{
			case ReportType.Customers: return "List of customers";
			case ReportType.Invoices: return "Invoices";
			default: throw new ArgumentException(string.Format("Unknown reportType '{0}'", reportType));
		}
	}

	public string GetReportFooter(ReportType reportType)
	{
		switch (reportType)
		{
			case ReportType.Customers: return "Customer footer";
			case ReportType.Invoices: return "Invoice footer";
			default: throw new ArgumentException(string.Format("Unknown reportType '{0}'", reportType));
		}
	}
}
```

You may be thinking “that’s not duplicate code”, and you’d be somewhat right. The return statements aren’t duplicate, but the entire switch statement is.  
Supposing I add a third `ReportType`, I’ll have to add code to both switch statements – thus maintaining the same switch block multiple places, or one could say `repeating myself`  
And whilst I’m talking about modifying my switch block to add a new type, I’d like to point out this also violates the [Open/Closed Principle](https://steffenskov.github.io/blog/2011/10/25/solid-principles-part-2-openclosed-principle.html) (henceforth referred to as OCP).

But back on track with DRY – a simple way to get the above code DRY again, would be to create a class to encapsulate the header and footer information, for each `ReportType`, and have a single method which returns an instance of this class:

```

public interface IReportSetting
{
	string Header { get; }
	string Footer { get; }
}

public class CustomersReportSettings : IReportSetting
{
	public string Header
	{
		get { return "List of customers"; }
	}

	public string Footer
	{
		get { return "Customer footer"; }
	}
}

public class InvoiceReportSettings : IReportSetting
{
	public string Header
	{
		get { return "Invoices"; }
	}

	public string Footer
	{
		get { return "Invoice footer"; }
	}
}

public class ReportSettingsLocator
{
	public IReportSetting GetReportSettings(ReportType reportType)
	{
		switch (reportType)
		{
			case ReportType.Customers: return new CustomersReportSettings();
			case ReportType.Invoices: return new InvoiceReportSettings();
			default: throw new ArgumentException(string.Format("Unknown reportType '{0}'", reportType));
		}
	}
}
```

You may be thinking “That’s an awful lot of classes, wouldn’t it be easier to just have `ReportSetting` class with Header/Footer, and set them inside the switch statement?”  
And well yes that would indeed be possible, but that really violates [Single Responsibility Principle](https://steffenskov.github.io/blog/2011/10/20/solid-principles-part-1-single-responsibility-principle.html) (henceforth known as SRP).  
“How?” you may wonder ? Well it’s simple – the `GetReportSettings` method all of a sudden has to both figure out which setting to return, and figure out what this setting contains (by setting the proper header/footer inside the case block)

That aside, you can see that I’ve changed the `ReportSettings` class to a `ReportSettingsLocator`. This is done because this is essentially a [Service locator pattern](http://en.wikipedia.org/wiki/Service_locator_pattern).

This is also somewhat like the first example – there’s only one switch statement.  
But whilst this solves my issue with DRY, I still have the violation of OCP.  
If I were to create a new `ReportType`, I’d have to create a new class which implements `IReportSetting `– no harm done there.  
However I also have to modify my switch statement – that’s no good.

This problem can be easily solved using a bit of [Reflection](http://en.wikipedia.org/wiki/Reflection_(computer_programming)#C.23) magic.

Now I know some of you will start thinking “Reflection, but that’s slow as hell, what are you thinking?”. I also know some developers won’t touch Reflection with a ten foot pole.  
These reactions however, stem mostly from lack of knowledge about Reflection.  
Yes Reflection is slow, yes you can cause performance issues with it.  
But when used properly Reflection is a very powerful tool, and causes little to no slowdown.

Allow me to present an easily extendable service locator:

```

public interface IReportSetting
{
	string Header { get; }
	string Footer { get; }
	ReportType Type { get; }
}

public class CustomersReportSettings : IReportSetting
{
	public string Header
	{
		get { return "List of customers"; }
	}

	public string Footer
	{
		get { return "Customer footer"; }
	}

	public ReportType Type
	{
		get { return ReportType.Customers; }
	}
}

public class InvoiceReportSettings : IReportSetting
{
	public string Header
	{
		get { return "Invoices"; }
	}

	public string Footer
	{
		get { return "Invoice footer"; }
	}

	public ReportType Type
	{
		get { return ReportType.Invoices; }
	}
}

public class ReportSettingsLocator
{
	private static IDictionary<ReportType, IReportSetting> reportSettings;
	static ReportSettingsLocator()
	{
		reportSettings = new Dictionary<ReportType, IReportSetting>();

		Type interfaceType = typeof(IReportSetting);
		foreach (Type type in Assembly.GetExecutingAssembly().GetTypes())
		{
			if (type.IsClass && !type.IsAbstract && interfaceType.IsAssignableFrom(type))
			{
				IReportSetting reportSetting = (IReportSetting)Activator.CreateInstance(type);
				reportSettings[reportSetting.Type] = reportSetting;
			}
		}
	}

	public IReportSetting GetReportSettings(ReportType reportType)
	{
		IReportSetting reportSetting;
		if (reportSettings.TryGetValue(reportType, out reportSetting))
			return reportSetting;

		throw new ArgumentException(string.Format("Unknown reportType '{0}'", reportType));
	}
}
```

I’ve added `ReportType Type { get; }` to my interface, and now I’m using Reflection in the `ReportSettingsLocator` class to find all classes which implements `IReportSetting`, and add them to a dictionary with their `ReportType` as key.

My `GetReportSettings` method will now work for new `ReportTypes` without being modified – I only have to add the new type to the enum, and create a class which implements `IReportSetting`.

Not only is the above code very much DRY, it also supports OCP.

But what about the performance ? Well the above Reflection is run inside a static constructor, meaning it’ll only run once during your application’s lifetime.  
Furthermore the above code finishes in roughly 10ms on my Athlon II X4 (a somewhat puny CPU) for an Assembly containing 1.700 types.

And that concludes todays lesson: Mind your switch statements 🙂