---
title: "&quot;eval&quot; in C#, yes it's possible"
layout: post
---

If you’ve used javascript, you probably know the nifty “[eval](http://www.w3schools.com/jsref/jsref_eval.asp)” statement.

To put it simple, eval runs dynamic code, which is just written in a string. So in javascript you could do this:

```
eval("alert('hello world');");
```

And it’d pop up the alert.

Recently I needed something similar in C#. I had a configuration file with a bunch of SQL queries in it, each of the queries would return a single value, and I’d then have to check that value against a configured threshold, to see if it was valid.

Problem was I really wanted that threshold to be as flexible as possible, without me constantly having to update the system with new threshold options (e.g. comparing dates, strings, whatnot)

So I figured, why not let the threshold be a small bit of C# code, and just “eval” it ?

A quick search at [Stack Overflow](http://stackoverflow.com/) showed that many want to do this, yet few know how.

This is also somewhat troubling, as using eval should really be seen as a last resort, since it opens up for easy [script injection / code injection](http://en.wikipedia.org/wiki/Code_injection), which in turn is a huge security breach.

I knew my system would only run in-house, and only be configured by skilled programmers, so it’s not that big of deal here. Just keep in mind, that if something is doable without eval, don’t go down that route!

With that out of the way, I assume you actually need the eval function for C#, so here’s the code:

```
{% raw %}
using System;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.CSharp;

public static class EvalProvider
{
	public static Func<T, TResult> CreateEvalMethod<T, TResult>(string code, string[] usingStatements = null, string[] assemblies = null)
	{
		Type returnType = typeof(TResult);
		Type inputType = typeof(T);

		var includeUsings = new HashSet<string>(new[] { "System" });
		includeUsings.Add(returnType.Namespace);
		includeUsings.Add(inputType.Namespace);
		if (usingStatements != null)
			foreach (var usingStatement in usingStatements)
				includeUsings.Add(usingStatement);

		using (CSharpCodeProvider compiler = new CSharpCodeProvider())
		{
			var name = "F" + Guid.NewGuid().ToString().Replace("-", string.Empty);
			var includeAssemblies = new HashSet<string>(new[] { "system.dll" });
			if (assemblies != null)
				foreach (var assembly in assemblies)
					includeAssemblies.Add(assembly);

			var parameters = new CompilerParameters(includeAssemblies.ToArray())
			{
				GenerateInMemory = true
			};

			string source = string.Format(@"
{0}
namespace {1}
{{
	public static class EvalClass
	{{
		public static {2} Eval({3} arg)
		{{
			{4}
		}}
	}}
}}", GetUsing(includeUsings), name, returnType.Name, inputType.Name, code);

			var compilerResult = compiler.CompileAssemblyFromSource(parameters, source);
			var compiledAssembly = compilerResult.CompiledAssembly;
			var type = compiledAssembly.GetType(string.Format("{0}.EvalClass", name));
			var method = type.GetMethod("Eval");
			return (Func<T, TResult>)Delegate.CreateDelegate(typeof(Func<T, TResult>), method);
		}
	}

	private static string GetUsing(HashSet<string> usingStatements)
	{
		StringBuilder result = new StringBuilder();
		foreach (string usingStatement in usingStatements)
		{
			result.AppendLine(string.Format("using {0};", usingStatement));
		}
		return result.ToString();
	}
}
{% endraw %}
```

Quite a lot of code there 😀

So here’s how it works: It uses the CSharpCodeCompiler to actually compile a very small DLL in-memory with just your single function.  
Once compiled it’ll get a Func<t tresult=""> delegate to the method just created, and return the delegate.</t>

You want to hold onto that Func delegate throughout your application’s lifetime! Otherwise you’ll end up compiling new DLLs everytime you need the eval function.

Once all of this is done, calling the Func is actually as fast as any other method. So performance-wise you’re not losing anything.

You’ll notice it only adds a single usings statement by default: System. Also it only references system.dll by defaults, so if you need functions in other namespaces, you’ll need to add those in the optional arguments “usingStatements” and “assemblies”.

Other than that it should be pretty straightforward 🙂

Here’s a simple example of using it:

```

static void Main(string[] args)
{
	var HelloWorld = EvalProvider.CreateEvalMethod<int, string>(@"return ""Hello world "" + arg;");

	Console.WriteLine(HelloWorld(42));
}
```

The above code will write out “Hello world 42”.

Notice how the code has to include the return statement, and how i use the argument “arg” (which is the int value).

And that’s pretty much it, any questions to how it works ? Drop me a comment 🙂