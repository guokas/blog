---
layout: post
title: "Role Based Authorization"
date: 2020-06-15 09:04:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'role based authorization'
navi-order: 'a1-7-1-3'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: how to add custom roles.how to implements role based authorization.
excerpt: how to add custom roles. how to implements role based authorization.
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
When users register on your website, you may want to distribute their different roles. For example, the user Ellen has comedian and citizen roles, and user LiWenliang has a whistleblower role. Different Roles have different permissions. In this article, I will show you how to implements a role-based authorization step by step.

# Adding Roles

Multiple ways can achieve adding roles for a user. You can add it through a claim or an identity.
```c#
var userClaim = new List<Claim>()
{
    new Claim(ClaimTypes.Name, "user name"),
    new Claim(ClaimTypes.Role,"admin")
};
```
or add roles by `UserManager`(if you are using ***[identity authorization][2]***):
```c#
//if the role doesn't exist,create it
if (!await _roleManager.RoleExistsAsync("admin"))
{
    await _roleManager.CreateAsync(new IdentityRole("admin"));
}
//add the role to the current user
await _userManager.AddToRoleAsync(user,"admin");
```
The `_roleManager` is an instance of the type `RoleManager<IdentityRole>`, and the `_userManager` is an instance of the type `UserManager<IdentityUser>`. Both instances inject via [Constructor injection][1].

As `UserManger` and `RoleManager` are under in the *Microsoft.AspNetCore.Identity* namespace, so you needs to install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, and the `UserManager` and `RoleManager` will automatically map actions into the database by Entity Framework Core. 

# Checking Roles

You can use `authorize` attribute with the `role` parameter to authority a user's roles. You can embed them against actions or controllers.

```c#
[Authorize(Roles = "admin")]
public IActionResult SecretAction() {
}

[Authorize(Roles = "admin")]
public class SecretController : Controller{
}
```

You can specify multiple roles as a comma separated list:
```c#
[Authorize(Roles = "admin,guest")]
public class SecretController : Controller
{
}
```
This controller would be only accessible by users who are members of the `admin` role or the `guest` role.

If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `citizen` and `president` role.
```c#
[Authorize(Roles = "citizen")]
[Authorize(Roles = "president")]
public class WhiteHouseController : Controller
{
}
```

You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.

```c#
[Authorize(Role = "admin")]
public class TestController : Controller
{
  public ActionResult Action1()
  {
  }
  [AllowAnonymous]
  public ActionResult Action2()
  {
  }
}
```

# Policy based role checks
Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at `startup` as part of the `Authorization` service configuration. This normally occurs in `ConfigureServices()` in your `Startup.cs` file.
```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:
```c#
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Secret()
{
    return View();
}
```

# Getting Roles
You can use the following method to retrieve out all roles of a user.
```c#
//get a ClaimsPrincipal instance
ClaimsPrincipal _claimsPrincipal = HttpContext.User;

//all roles that user belongs to
List<String> roles = _claimsPrincipal.Claims.Where(c => c.Type == ClaimTypes.Role).Select(s=>s.Value).ToList();

//check is if user has a specify role
if (_claimsPrincipal.IsInRole("admin")) { 
    
}
```

If you are working on [identity authorization][2], then check the following code:
```c#
var user = await _userManager.GetUserAsync(User);
IList<String> roles = await _userManager.GetRolesAsync(user);
//.. more operations
```

You can found this project's respository in [authentication and authroization][3] github respository.


[1]: https://voltwu.github.io/blog/csharp-ef/2020/02/28/Entitiy-Framework-Tutorial/
[2]: https://voltwu.github.io/blog/csharp-asp.net%20core/2020/06/09/aspnetcore-identity/
[3]: https://github.com/voltwu/C-Sharp-Web-Net-Core-authentication-and-authorization
