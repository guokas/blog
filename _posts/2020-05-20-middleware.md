---
layout: post
title: "Middleware"
date: 2020-05-20 10:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Middleware'
navi-order: 'a1-5-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: As our previous episodes, we have known that ConfigureService is a place we can register our service. So, where is the place to register middleware
excerpt: As our previous episodes, we have known that ConfigureService is a place we can register our service. So, where is the place to register middleware
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

As our previous episodes, we have known that `ConfigureService` is a place we can register our service. So, where is the place to register our middleware?

The answer is the Configure method. 
![Alt][3]
In this episode, we will learn basic concepts and usages about middleware. 

# Middleware Overview
Middleware is a set of applications in the request processing pipeline, it process on both request and response. The following is a diagram copied from the official.

![Alt][1]

![Alt][2]

Each middleware may process both before the next call and after the next call. If a middleware doesn't invoke the next middleware, it's called as ***short-circuiting***. ***Short-circuiting*** avoids the waste of resources. Let's think about this scenario that you are handling static file resources, like js, CSS, font files. These requests only need to be processed by your static file processing middleware, and they do not need to be treated by deeper middleware. It saves application resources.

The order of middleware placed is **critical**, which affects security, performance, functionality. As middleware is processed one by one, so if you want to records exception logs, then you should place your exception handler at the front in the pipeline.

# A Simple Custom Middleware
The following example uses two methods, they are `Use` and `Run`. Both can register middleware. 
```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.Use(async (context,next) =>
    {
        await context.Response.WriteAsync("This is my first custom middleware!\n");
        await next.Invoke();
    });
    app.Run(async context=> {
        await context.Response.WriteAsync("Hello World");
    });
}
```
The `Use` method is optical. We can only use the `Run` method.
```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.Run(async context=> {
        await context.Response.WriteAsync("Hello World");
    });
}
```
You need to use an asynchronous method to strengthen its flexibility. The `invoke()` method represents starting to call the next middleware. It's permitted to have custom actions before the next middleware call and after the next middleware call. 
```c#
app.Use(async (context,next) =>
{
    //actions before the next middleware call
    
    await next.Invoke();

    //actions after the next middleware call
});
```

# What's Use, Run, Map?
`Use`, `Run`, `Map` are three methods you can use to configure the HTTP request pipeline.

The `Use` method is an extension method that allows chaining multiple middleware delegates. The `next` parameter represents the next middleware delegate. You can **short-circuit** the request pipeline by ***not calling*** the `Invoke()` method.
```c#
app.Use(async (context,next) =>
{
  var agents = context.Request.Headers["User-Agent"];
  if (agents.Count == 0){//if no User-Agent found. then short-circuiting the request pipeline
      context.Response.StatusCode = Microsoft.AspNetCore.Http.StatusCodes.Status403Forbidden;
      await context.Response.WriteAsync("robot is not permitted");
  }
  else
      await next.Invoke();
});
app.Run(async context=> {
  await context.Response.WriteAsync("Welcome");
});
```
From the above example, we suppose if a request doesn't have a User-Agent header, then it's an illegal request, which will be **short-circuiting** by ***not calling*** the next middleware.

The `Run` method doesn't receive a `next` parameter. The first `Run` method is always terminal and terminates the request pipeline. Its recommended to only use one `Run` method within one pipeline. If you have more than one `Run` method in a request pipeline, then except for the first `Run` method, other `Run` methods will not execute.
```c#
app.Run(async context=>{
  await context.Response.WriteAsync("I am always a short-circuiting method");
});
```
As the `Run` method always terminates the request pipeline, so it's a ***short-circuiting*** method.That is also the reason that why `Run` doesn't have the `next` parameter.


You can use `map` extensions to branch the request pipeline. The `map` branches the request pipeline based on its given path. we have already known that the `Run` is a terminal method, and `Run` just can be used once per application. So it's impossible to achieve multi-terminal with `Run`. The `map` strengthens `Run`'s functionality. In other words, with `map` branching a request pipeline, an application can have more than one terminals.
```c#
app.Map("/def", app => {
    app.Run(async context =>
    {
        await context.Response.WriteAsync("you are on /def path.");
    });
});
app.Map("/abc/def", app => {
    app.Run(async context =>
    {
        await context.Response.WriteAsync("you are on /abc/def path.");
    });
});
app.Map("/abc", app =>
{
    app.Run(async context =>
    {
        await context.Response.WriteAsync("you are on /abc path.");
    });
});
app.Run(async context=> {
    await context.Response.WriteAsync("I am from non map method.");
});
```

|Request   |Response   |
|---|---|
|localhost/abc/def   |you are on /abc/def path.   |
|localhost/abc   |you are on /abc path.   |
|localhost/def   |you are on /def path.   |
|localhost   |I am from non map method.   |

<span style="color:red">Note:</span>
> Because the route path matches in sequential, so the order is critical. Put the detailed path first.

# Built-in Middleware

ASP.NET Core provides multiple useful built-in middlewares. The following table illustrates some of them.

|Middleware   |Description   |
|---|---|
|Authentication   |Adds authentication support.   |
|Authorization   |Adds authorization support.   |
|CORS   |Configures Cross-Origin Resource Sharing.   |
|Diagnostics   |Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.   |
|MVC   |Processes requests with MVC/Razor Pages.   |
|Static Files   |Provides support for serving static files and directory browsing.   |
|HTTPS Redirection   |Redirect all HTTP requests to HTTPS.   |
|Session   |Provides support for managing user sessions.   |
|URL Rewrite   |Provides support for rewriting URLs and redirecting requests.   |
|Response Compression   |Provides support for compressing responses.   |

Looking for entire built-in middlewares, see [here][4].

Here is the end of this article, we learned the concept of middleware and usage of middleware if you have any questions leave a message below. 

[1]: /public/img/2020-05-20-middleware-a.png
[2]: /public/img/2020-05-20-middleware-b.png
[3]: /public/img/2020-05-20-middleware-c.png
[4]: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1#built-in-middleware
