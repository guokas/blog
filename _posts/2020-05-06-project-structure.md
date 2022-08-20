---
layout: post
title: "Project structure"
date: 2020-05-06 18:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Project structure'
navi-order: 'a1-3'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: 
excerpt: 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

In this episode, we are going to introduce the basic project structures. It's essential to know the basic web project structure. To better illustrate the structure of the web project, I created an empty **ASP.NET CORE** project in visual studio 2019.

![Alt][1]

# Dependencies
You can add references or packages in here.

# launchSettings.json
In the properties menu, you will see a *launchSettings.json* file, which describes how to launch the project. It includes startup options, command, whether to open a browser, environment variables, etc..

![Alt][2]

According to this project's configuration, if you are using the IISExpress to launch this project, then two URLs will be matched, they are [https://localhost:44326](https://localhost:44326) and [http://localhost:63489](http://localhost:63489). You can use either [https://localhost:44326](https://localhost:44326) or [http://localhost:63489](http://localhost:63489) to access your website. The difference is that one is `HTTPS` and the other one is `HTTP`. the `HTTP` address is specified explicitly in the `"applicationUrl": "http://localhost:63489"` field. `HTTPS` specified in this `"sslPort":44326` field. By default, Visual Studio launches the `HTTPS` one in the browser.

![Alt][3]

Looking at your application icons in your desktop, you will find out that the IIS server launched. 

![Alt][4]

The IIS server is currently hosting your website.

As we can see from the *launchSettings.json* file that has two profiles, we already introduced the IISExpress one. Another is the WebApplication1, which is similar to the IISExpress. WebApplication1 has two URLs that configured in one line `"applicationUrl": "https://localhost:5001;http://localhost:5000"`.


Because WebApplication1 has nothing to do with the IIS, so WebApplication1 wouldn't use the IIS as its server, an internal web server hosting instead.

# Program.cs
*Project.cs* is an entry point of the entire application. Essentially, *ASP.NET Core* is a console project, which executing from the `public static void Main(string[] args)`, where we can create a web host application.

## Set Host in ASP.NET Core 3.x
the following is the `Program.cs` file
```c#
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
```
The `Main()` method calls the `CreateHostBuilder()` static methods with the pre-configured defaults. The `CreateHostBuilder()` returns an `IHostBuilder` instance, which can produce an `IHost` instance by calling the `Build()` method. When you get the `IHost` instance, start the web application through the `Run()` method.


## CreateDefaultBuilder

the `CreateDefaultBuilder()` method in `Host.cs` returns an `IHostBuilder` instance, which initialized some default settings, for example, load the content root path, load environment variables, load settings file contents, configure ILoggerFactory, etc..

The following is the source code from [dotnet/runtime github][5]:
```c#
public static IHostBuilder CreateDefaultBuilder(string[] args)
{
    var builder = new HostBuilder();

    builder.UseContentRoot(Directory.GetCurrentDirectory());
    builder.ConfigureHostConfiguration(config =>
    {
        config.AddEnvironmentVariables(prefix: "DOTNET_");
        if (args != null)
        {
            config.AddCommandLine(args);
        }
    });

    builder.ConfigureAppConfiguration((hostingContext, config) =>
    {
        var env = hostingContext.HostingEnvironment;

        config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
              .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);

        if (env.IsDevelopment() && !string.IsNullOrEmpty(env.ApplicationName))
        {
            var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));
            if (appAssembly != null)
            {
                config.AddUserSecrets(appAssembly, optional: true);
            }
        }

        config.AddEnvironmentVariables();

        if (args != null)
        {
            config.AddCommandLine(args);
        }
    })
    .ConfigureLogging((hostingContext, logging) =>
    {
        var isWindows = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);

        // IMPORTANT: This needs to be added *before* configuration is loaded, this lets
        // the defaults be overridden by the configuration.
        if (isWindows)
        {
            // Default the EventLogLoggerProvider to warning or above
            logging.AddFilter<EventLogLoggerProvider>(level => level >= LogLevel.Warning);
        }

        logging.AddConfiguration(hostingContext.Configuration.GetSection("Logging"));
        logging.AddConsole();
        logging.AddDebug();
        logging.AddEventSourceLogger();

        if (isWindows)
        {
            // Add the EventLogLoggerProvider on windows machines
            logging.AddEventLog();
        }
    })
    .UseDefaultServiceProvider((context, options) =>
    {
        var isDevelopment = context.HostingEnvironment.IsDevelopment();
        options.ValidateScopes = isDevelopment;
        options.ValidateOnBuild = isDevelopment;
    });

    return builder;
}

```

# Startup.cs

An ASP.NET Core application must specify a Startup class, which needs to register in the `Program` class by using the generic `UseStartup<T>` method.
```c#
webBuilder.UseStartup<Startup>();
```

Open the *Startup.cs* file:

![Alt][6]

## ConfigureServices
The Configureservices method is a place where you can register your dependent classes with the built-in IoC container (**ASP.NET Core refers dependent class as a Service**).  After registering services, you can use it anywhere in the application. The IoC container will inject them automatically.

## Configure
The Configure method is a place where you can configure your pipeline by adding middlewares. The pipeline middlewares specify how the app responds to the HTTP request. The middlewares added to the `IApplicationBuilder`, but it's not registered in the service container. Hosting creates an `IApplicationBuilder` and passes it directly to the Configure method.

This is the end of the article. In this article, we have learned the basic web structure. In the next articles, I am going dig into it.

[1]: /public/img/2020-05-06-project-structure-a.png
[2]: /public/img/2020-05-06-project-structure-b.png
[3]: /public/img/2020-05-06-project-structure-c.png
[4]: /public/img/2020-05-06-project-structure-d.png
[5]: https://github.com/dotnet/runtime
[6]: /public/img/2020-05-06-project-structure-e.png


