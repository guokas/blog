---
layout: post
title: "Add Custom Middleware"
date: 2020-06-05 10:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'add custom middleware'
navi-order: 'a1-5-2'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: How to add custom middleware, how to use avoid common mistakes when use DI in your custom middleware.
excerpt: How to add custom middleware, how to use avoid common mistakes when use DI in your custom middleware.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or 
              post.navi-order == "a1-5"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

In the previous episode, we build an anonymous middleware. Now, I am going to show you how to define middleware in another separate file, and how to use dependency injection in custom middleware.

Before we start this article, let's take a review of the previously defined anonymous middleware.
```c#
app.Use(async (context,next) =>
{
  var agents = context.Request.Headers["User-Agent"];
  if (agents.Count == 0){
      context.Response.StatusCode = Microsoft.AspNetCore.Http.StatusCodes.Status403Forbidden;
      await context.Response.WriteAsync("robot is not permitted");
  }
  else
      await next.Invoke();
});
```
We use the `Use` method to add our custom middleware. The `Use` method accepts a delegate. In our custom middleware, if we want to invoke the next middleware, then we execute the `Invoke()` method, we can also **short-circuiting** the request pipeline by ***not calling*** the `Invoke()` method.

I am going to add a custom `ExceptionHandlerMiddlware` middleware, which can capture exceptions. The caught exception is going to be recorded into a log file by our `ExceptionHandlerMiddlware`.

**1. define a *ExceptionHandlerMiddlware.cs* file**

```c#
namespace share.middleware
{
    public class ExceptionHandlerMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<ExceptionHandlerMiddleware> _logger;
        public ExceptionHandlerMiddleware(RequestDelegate next,ILogger<ExceptionHandlerMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }
        public async Task Invoke(HttpContext httpContext)
        {
            try
            {
                await _next(httpContext);
            }
            catch (Exception e)
            {
                //we log exception into a console window instead of a file.
                _logger.LogError("current time : {0}",DateTime.Now);
                _logger.LogError("message : {0}", e.Message);
                _logger.LogError("stack trace : \n{0}", e.StackTrace);

                //prompt user
                await httpContext.Response.WriteAsync("An unexpected error occur, please contact the website manager.");
            }
        }
    }
    public static class ExceptionHandlerMiddlewareExtension {
        public static void UseExceptionHandlerMiddleware(this IApplicationBuilder app) {
            app.UseMiddleware<ExceptionHandlerMiddleware>();
        }
    }
}
```

**2. register `ExceptionHandlerMiddleware` through `UseExceptionHandlerMiddleware` extension method**

```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseExceptionHandlerMiddleware();

    //other code
}
```

In the above example, we build an `ExceptionHandlerMiddleware` class, which has a constructor method and an `Invoke()` method. The constructor accepts a `RequestDelegate` parameter, which represents the next middleware delegate. To execute the next middleware in sequential, the `Invoke()` created.

The above example also uses dependency injection. The trick here is that don't inject scoped service ***via a constructor*** because it will force the services to behave like a singleton. You should register your ***scoped services*** at the **Invoke()** method's signature.

>Because middleware is constructed at app startup, not per-request, scoped lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request. If you must share a scoped service between your middleware and other types, add these services to the Invoke method's signature. 

```c#
public class ExceptionHandlerMiddleware
{
    private readonly RequestDelegate _next;
    private readonly MySignletonService _signleton;
    //if you want your services behave like singleton,
    //register them via constructor
    public ExceptionHandlerMiddleware(RequestDelegate next,MySignletonService signleton)
    {
        _next = next;
        _signleton = signleton;
    }
    //if you want your services behave like scoped and transient,
    //register them via the Invoke() method
    public async Task Invoke(HttpContext httpContext,MyScopedService scoped,MyTransientService transient)
    {
        //await _next(httpContext);
    }
}
```

For more information, see [here][1].




[1]:https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/write?view=aspnetcore-3.1#per-request-middleware-dependencies
