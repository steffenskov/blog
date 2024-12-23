---
title: 'Accessing session in HttpHandlers'
layout: post
---

In ASP.Net HttpHandlers cannot access the session state by default, in order to enable them to do so, you need to implement an interface.

However this is rather simple, since the interface is just a “marker interface” as MSDN calls it. Which means it has no properties/methods you need to implement.

If your handler only needs to read data from session, it’s enough to implement [IReadOnlySessionState](http://msdn.microsoft.com/en-us/library/system.web.sessionstate.ireadonlysessionstate.aspx#), however if you need both read and write access, you’ll have to implement [IRequiresSessionState](http://msdn.microsoft.com/en-us/library/system.web.sessionstate.irequiressessionstate.aspx)

For instance:

```
public class MyHandler : IHttpHandler, IReadOnlySessionState
{
	public void ProcessRequest(HttpContext context)
	{
		context.Response.ContentType = "text/plain";
		context.Response.Write(context.Session["UserName"]);
	}

	public bool IsReusable
	{
		get
		{
			return false;
		}
	}
}
```