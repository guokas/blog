---
layout: post
title: "Asp.Net Core Identity"
date: 2020-06-09 09:04:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'asp.net core identity'
navi-order: 'a1-7-1-2'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: asp.net core identity help you manage user,password, profile data,roles, claims,email confirmation etc...
excerpt: asp.net core identity help you manage user,password, profile data,roles, claims,email confirmation etc...
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or 
              post.navi-order == "a1-7" or 
              post.navi-order == "a1-7-1" %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>

<!--navigation bar-->
The [previous article][1] discussed how to use claim implements authentication, authentication basics, and its strength and weakness. ***ASP.NET CORE IDENTITY*** is another alternative way to implements authentication. Before this article start, let's take a look at what's is ***ASP.NET CORE IDENTITY***.

>ASP.NET Core Identity:
>
>   * Is an API that supports user interface (UI) login functionality.
>   * Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.

According to the above explanation, ***ASP.NET CORE IDENTITY*** is a little bit of a difference with [claims][1]. We need to provides additional components that ***ASP.NET CORE IDENTITY*** needs, for example, the database. 

You can found this project's source code in Github [authentication&authorization][2].

# Deploy Settings
As ***ASP.NET CORE IDENTITY*** requires a database setting, I use the SQL server database. And because I use Entity Framework Core, so it doesn't need me to create a database manually. Following is my database connection configuration.
```c#
services.AddDbContext<AppDbContext>(options =>
        options.UseSqlServer("Server=.;Database=IdentityAuthentication;Trusted_Connection=True;")
);
```
If you are unfamiliar with the entity framework core, take a look at [ef core][3].

The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.
```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<AppDbContext>(options =>
            options.UseSqlServer("Server=.;Database=IdentityAuthentication;Trusted_Connection=True;")
    );
    services.AddIdentity<IdentityUser, IdentityRole>(config =>
    {
        // user settings
        config.User.AllowedUserNameCharacters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+ ";
        config.User.RequireUniqueEmail = true;

        //lock out settings
        config.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(5);
        config.Lockout.MaxFailedAccessAttempts = 5;
        config.Lockout.AllowedForNewUsers = true;

        // Password settings.
        config.Password.RequireDigit = false;
        config.Password.RequireLowercase = false;
        config.Password.RequireNonAlphanumeric = false;
        config.Password.RequireUppercase = false;
        config.Password.RequiredLength = 6;
        config.Password.RequiredUniqueChars = 1;
    })
    .AddEntityFrameworkStores<AppDbContext>()
    .AddDefaultTokenProviders();

    services.ConfigureApplicationCookie(config=> {
        // Cookie settings
        config.Cookie.Name = "user.cookie";
        config.Cookie.Expiration = TimeSpan.FromDays(7);

        config.ExpireTimeSpan = TimeSpan.FromDays(7);
        config.LoginPath = "/Home/login";
    });
    services.AddControllersWithViews().AddRazorRuntimeCompilation();
}
```
The above configuration specifies various authentication configurations, and it is similar to the previous [claim example][4]. In this example, I use `AddIdentity()` rather than `AddAuthentication()`.

![Alt][5]

Even though the implementation is different, however, their implementation thoughts are the same. Another thing to note is the `AddEntityFrameworkStores<T>()` method, so we need to install and import the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package.


# LOG IN
```c#
[HttpPost]
public async Task<IActionResult> Loginin(String name, String password)
{
    var user = await _userManager.FindByNameAsync(name);
    if (user != null)
    {
        //sign in
        var signResult = await _signInManager.PasswordSignInAsync(user, password, false, false);
        if (signResult.Succeeded)
        {
            return RedirectToAction("Userinfo");
        }
    }
    return RedirectToAction("Login");
}
```
The `_userManager` is a `UserManager` type, which manages user information. If you take a look at the previous [claim authentication login part][6], you will find out that this login method is much simpler.

The `UserManager.FindByNameAsync` method will query the database, which we provided before, to check is if the user exists. Identity does a lot of work under the code, so it's much easier for us to focus on business logic concentratedly.

# LOG OUT
```c#
public async Task<IActionResult> Loginout() {
    await _signInManager.SignOutAsync();
    return RedirectToAction("Login");
}
```
Log out is quite easy, you only need to invoke `SignOutAsync()` or `SignOut()` on `_signInManager`, which is a `SignInManager` instance.
# REGISTER
```c#
[HttpPost]
public async Task<IActionResult> Registerup(String name,String email, String password)
{
    var user = new IdentityUser
    {
        UserName = name,
        Email = email
    };
    var result = await _userManager.CreateAsync(user, password);
    if (result.Succeeded)
    {
        var res = await _signInManager.PasswordSignInAsync(user, password, false, false);
        if (res.Succeeded)
            return RedirectToAction("Userinfo");
    }
    return RedirectToAction("Login");
}
```
Register a new user is also very easy. As the Identity Authentication will do most of the work, so we only need to create an `IdentityUser` instance, then invoke the `CreateAsync()` method, and pass in our user and password.

# Test Identity
The default web project templates allow anonymous access to the home pages. To test `authorization` or `identity`, add `[Authorize]`:
```c#
[Authorize]
public IActionResult Userinfo() {
    return View();
}
```
You also can retrieve the user information back as the following methods.
```cs
@using Microsoft.AspNetCore.Identity
@inject UserManager<IdentityUser> userManager
@{
    var user = await userManager.GetUserAsync(User);
}
<h1>
    User information page
</h1>
<br />
@if (user != null)
{
    <span>name: </span><span>@user.NormalizedUserName</span>
    <br />
    <span>email: </span><span>@user.NormalizedEmail</span>
    <br />
    <span>my password hash: </span><span>@user.PasswordHash</span>
}
else
{
    <div> authentication failed </div>
}
```

# Conclusion
According to the [preceding article][1] and this article, we have studied how to use identity authentication and [claim authentication][1]. Identity authentication provides a complete function, which includes database operation, user management, etc.. so you don't need to operate SQL operations by yourself. However, [Claim authentication][1] ***doesn't*** provide SQL operations, so you have to implement SQL operations by yourself manually.


[1]: https://voltwu.github.io/blog/csharp-asp.net%20core/2020/06/08/authentication-and-authorization-basics/
[2]: https://github.com/voltwu/C-Sharp-Web-Net-Core-authentication-and-authorization
[3]: https://voltwu.github.io/blog/csharp-ef/2020/02/28/Entitiy-Framework-Tutorial/
[4]: https://voltwu.github.io/blog/csharp-asp.net%20core/2020/06/08/authentication-and-authorization-basics/#deploy-authentication-settings
[5]: /public/img/2020-06-09-aspnetcore-identity-a.png
[6]: https://voltwu.github.io/blog/csharp-asp.net%20core/2020/06/08/authentication-and-authorization-basics/#log-in