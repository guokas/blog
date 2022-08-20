---
layout: post
title: "Authentication And Authorization Basics"
date: 2020-06-08 09:04:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'authentication and authorization basics'
navi-order: 'a1-7-1-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: asp.net core security and identity,authentication,authorization,identityserver.
excerpt: asp.net core security and identity,authentication,authorization,identityserver.
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

In this article, we are going to discuss some basic concepts, and with a useful project. You can find and download this project on [authenticaton & authorization][1] repository.

Authentication and Authorization are two things. However, they have similar concepts. Typically, Authentication determines a user's identity, and Authorization determines what a user can do. Let's think about this scenario that a person comes to your home. The first thing you need to ask is who you are. After you identify this person's identity, then you determine what's this person can do in your house. 

# Deploy Authentication Settings
The following project illustrates how to use Cookie authentication. The *startup.cs* file contains the following snippet.

```c#
public void ConfigureServices(IServiceCollection services)
{
    //add database service
    services.AddDbContext<AppDbContext>(
        options => options.UseSqlServer("Server=.;Database=CookieAuthDatabase;Trusted_Connection=True;")
    );

    // add authentication dependency configuration
    // the following configuration will be used in the Authentication and Authorization middleware
    services.
        AddAuthentication("CookieAuth").
        AddCookie("CookieAuth", config =>
        {
            config.Cookie.Name = "user.cookie";
            config.LoginPath = "/Home/Login";
        });

    //add controller with views
    services.AddControllersWithViews();
}
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
   //add development exception handler middleware
    if (env.IsDevelopment()) {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    //determines user's identity
    app.UseAuthentication();
    //determines what a user is able to do
    app.UseAuthorization();

    app.UseEndpoints(opt => {
        opt.MapDefaultControllerRoute();
    });
}
```

Let's look at the Authentication configuration first. Here, I deployed a cookie configuration.
```c#
services.
    AddAuthentication("CookieAuth").
    AddCookie("CookieAuth", config =>
    {
        config.Cookie.Name = "user.cookie";
        config.LoginPath = "/Home/Login";
    });
```
*The default authentication scheme name* must be the same as *the cookie scheme name*. Just as the above example, they both use the `CookieAuth`. In the inner cookie's configuration, we need to specify the cookie's name and the login path. The default login path is */Account/Login* if the `LoginPath` absent. The request that failed authorization will be automatic redirects via the 302 status code to the login path.

A bunch of configuration options is available in `CookieAuthenticationOptions`. With them, you can specify the cookie's expired time, the cookie's path, and the request denied path, etc.
```c#
//config is a type of CookieAuthenticationOptions
config.Cookie.MaxAge = TimeSpan.FromDays(7);
config.ExpireTimeSpan = TimeSpan.FromDays(7);
//... other more configuration
```

# Log in
```c#
[HttpPost]
public async Task<IActionResult> Loginup(String name,String password) {
    User _user = new User(name,password);
    //query this user from database
    User DbRes = dbContext.users.Where(u =>
                      _user.name.Equals(u.name)
                      && _user.passwordHash.Equals(u.passwordHash)
                  ).FirstOrDefault();
    //check is if this user exists
    if (DbRes != null)
    {
        //login in, if user exists
        var userClaim = new List<Claim>()
        {
            new Claim(ClaimTypes.Name, DbRes.name),
            new Claim("passwordhash",DbRes.passwordHash)
        };
        var userIdentity = new ClaimsIdentity(userClaim, CookieAuthenticationDefaults.AuthenticationScheme);
        var userPrinciple = new ClaimsPrincipal(userIdentity);
        await HttpContext.SignInAsync(userPrinciple);
        return RedirectToAction("Userprofile");
    }
    //redirects to login page, if user doesn't exist
    return RedirectToAction("login");
}
```

The `HttpContext.SignInAsync` method accepts a `ClaimsPrincipal` object. A `ClaimsPrincipal` consist of `ClaimsIdentity`. A `ClaimsIdentity` consist of `Claim` list. We put our user's information into a list of claims.

```c#
var userClaim = new List<Claim>()
{
    new Claim(ClaimTypes.Name, DbRes.name),
    new Claim("passwordhash",DbRes.passwordHash)
    //new Claim(ClaimTypes.Email, 'email str'),
    //new Claim(ClaimTypes.Role, 'role')
    //...other identity fields
};
```
# Log out
```c#
public async Task<IActionResult> Loginout() {
    await HttpContext.SignOutAsync();
    return RedirectToAction("login");
}
```
Logout is quite easy, and you only need to invoke the `SignOutAsync()` or `SignOut()` method.

# Register

```c#
[HttpPost]
public async Task<IActionResult> Registerup(String name,String password) {
    //query is if exists the same user in database
    User _user = new User(name, password);
    User DbRes = dbContext.users.Where(u => u.name.Equals(_user.name)).FirstOrDefault();
    if (DbRes == null) {
        //it doesn't exist same user,so save this user into database
        dbContext.users.Add(_user);
        int RCount = await dbContext.SaveChangesAsync();
        //check is if insert success
        if (RCount > 0)
        {
            //log in in HttpContext
            var userClaim = new List<Claim>()
            {
                new Claim(ClaimTypes.Name, _user.name),
                new Claim("passwordhash",_user.passwordHash)
            };
            var userIdentity = new ClaimsIdentity(userClaim, CookieAuthenticationDefaults.AuthenticationScheme);
            var userPrinciple = new ClaimsPrincipal(userIdentity);
            await HttpContext.SignInAsync(userPrinciple);
            return RedirectToAction("Userprofile");
        }
        //insert data failed
        return RedirectToAction("register");
    }
    //this name has already registered by others
    return RedirectToAction("register");
}
```
The process is similar to logins. See the <a href="#log-in">login part</a> for a detailed explanation.

# Test Authorization
The default web project templates allow anonymous access to the home pages. To test authorization, add `[Authorize]`:
```c#
[Authorize]
public IActionResult Userprofile() {
    ClaimsPrincipal _claimsPrincipal = HttpContext.User;
    ClaimsIdentity claimIdentity = _claimsPrincipal.Identities.Where(ct => ct.AuthenticationType == CookieAuthenticationDefaults.AuthenticationScheme).FirstOrDefault();
    ViewData["name"] = claimIdentity.Claims.Where(cm => cm.Type == ClaimTypes.Name).FirstOrDefault().Value;
    ViewData["passwordhash"] = claimIdentity.Claims.Where(cm => cm.Type == "passwordhash").FirstOrDefault().Value;
    return View();
}
```

# Conclusion
The above example is the almost simplest authentication. It doesn't include identity authorization, OAuth and identity server, etc.. Even though, it is simple. But it also provides you a very scalable framework. As it's flexible, you can add whatever additional functionality you want.

[1]: https://github.com/voltwu/C-Sharp-Web-Net-Core-authentication-and-authorization