---
layout: post
title: "Dependency Injection"
date: 2020-05-08 10:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Dependency Injection'
navi-order: 'a1-4-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: ASP.NET Core supports dependency injection(DI), which is a technique for achieving Inverse of Control(IoC). The Built-In IoC container doesn't provide as many features as other third libraries
excerpt: ASP.NET Core supports dependency injection(DI), which is a technique for achieving Inverse of Control(IoC). The Built-In IoC container doesn't provide as many features as other third libraries
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or
              post.navi-order == "a1-4" %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

# Built-In IoC Container
ASP.NET Core supports dependency injection(DI), which is a technique for achieving Inverse of Control(IoC). The Built-In IoC container doesn't provide as many features as other third libraries. If you want to use other features, like property injection, you can use third libraries instead([more info][1]).

**The class(types) managed by IoC container is called service**. IoC container will automatically inject services into our constructors or action methods. 

Usually, project services have two types. There are:
* Framework Service: The service that ASP.NET Core defined, like `ILogger<T>`、 `IWebHostEnvironment`、 `IHostApplicationLifetime`.
* Application Service: The service that you create for your application as a programmer.

Next! I will explain how to register services? What's the service lifetime? How to retrieve service from the IoC container?

# Where to register service?
By default, ASP.NET Core uses `Startup` class as its startup type in *Program.cs* file. The specified startup class is used to register service and handle middleware.
![Alt][2]
Here we follow the ASP.NET Core default convention. The `Startup` class has two methods that are `ConfigureServices` and `Configure`. The `ConfigureServices` is used to register our services. The `Configure` is used to set up middleware.

The `ConfigureServices` has an `IServiceCollection` parameter, which is an instance that can register application services on.

Consider the following example:

*Log.cs*
```c#
public interface ILog
{
    void Write(string message);
}
public class ConsoleLog : ILog
{
    public void Write(String message)
    {
        Console.Write(message);
    }
}
```
Let us register the above instance into the IoC container. 

*Startup.cs*
```c#
public class Startup{
  public void ConfigureServices(IServiceCollection services){
    services.Add(new ServiceDescriptor(typeof(ILog),typeof(ConsoleLog),ServiceLifetime.Singleton));
  }
}
```
The [ServiceDescriptor][3] describes the service with its service type, implementation, and lifetime. Here, we specified the `ILog` as a service and the `ConsoleLog` as its implementation, and register it as a singleton lifetime.

# Service lifetime
The IoC container keeps track of the lifetime of service and disposes of its resource when their life ends.

There are three types of service lifetime.

1. **Transient:** The IoC container will create a new instance each time when they are requested.

2. **Scoped:** The IoC container will create a new instance of the specified service type once per client request.

3. **Singleton:** The IoC container will create an instance and share it throughout the application lifetime. Every subsequent request gets the same as first.

There are many things to pay attention to when dealing with Dependency Injection. The following are some recommendations for better to built application.

* Register your service as a transient wherever possible, because you didn't need to care a lot of multi-threading, and memory leak problems.
* Consider multi-threading when designing scoped service. Do not resolve scoped service from singleton service. It is dangerous.
* Use singleton carefully, because you need to deal with multi-threading and potential memory-leak problem.

![Alt][4]

# Registration method
we can register our service through `ServiceDescriber`, which provides a lot of overload constructors. 

The following example illustrates various usages of the `ServiceDescriber` constructor.

```c#
services.Add(new ServiceDescriptor(typeof(ILog),new ConsoleLog()));//singleton
services.Add(new ServiceDescriptor(typeof(ILog),typeof(ConsoleLog)));//singleton
services.Add(new ServiceDescriptor(typeof(ILog), typeof(ConsoleLog), ServiceLifetime.Transient));//transient
services.Add(new ServiceDescriptor(typeof(ILog),typeof(ConsoleLog),ServiceLifetime.Scoped));//scoped
```

Microsoft also provides extension methods for each type of lifetime.

```c#
services.AddTransient<ConsoleLog>();//transient
services.AddScoped<ConsoleLog>();//scoped
services.AddSingleton<ConsoleLog>();//singleton
```


# Constructor Injection

Once we register services into the IoC container, the ASP.NET Core will automatically inject services into target constructor, when its parameters are service types.

Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.

```c#
public class HomeController : Controller
{
    private readonly ILog _log;
    public HomeController(ILog log)
    {
        _log = log;
    }
    public IActionResult Index()
    {
        _log.Write("hello, I come from console log");
        return View();
    }
}
```
The above simple example injects `ILog` into the `HomeController` constructor.

Switch debug profile to the current project name. because we are using a `Console.Write()` to print debug information, which is not working with IIS.

As my project name is  `DependencyInjection`, so I switched to *DependencyInjection* profile.
![Alt][5]

Then start to debug your project, open [https://localhost:5001/](https://localhost:5001/) or [http://localhost:5000/](http://localhost:5000/)

Once you open it in a browser. View your console window.

![Alt][6]

# Action Method Injection
ASP.NET Core provides an action method injection, which injects the service type parameter into an action method.

The `FromServicesAttribute` specifies that an action parameter should be bound using the request services.

```c#
public class HomeController : Controller
{
    public IActionResult Index([FromServices] ILog _log)
    {
        _log.Write("hello, I come from console log");
        return View();
    }
}
```


[1]: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1#default-service-container-replacement
[2]: /public/img/2020-05-08-dependency-injection-a.png
[3]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev16.query%3FappId%3DDev16IDEF1%26l%3DEN-US%26k%3Dk(Microsoft.Extensions.DependencyInjection.ServiceDescriptor);k(DevLang-csharp)%26rd%3Dtrue&view=dotnet-plat-ext-3.1
[4]: /public/img/2020-05-08-dependency-injection-b.png
[5]: /public/img/2020-05-08-dependency-injection-c.png
[6]: /public/img/2020-05-08-dependency-injection-d.png