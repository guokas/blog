---
layout: post
title: "Policy Based Authorization"
date: 2020-06-18 09:04:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'policy based authorization'
navi-order: 'a1-7-1-4'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: how to add custom policy, how to implements custom requirements, how to add custom policy, let's explore it together.
excerpt: how to add custom policy, how to implements custom requirements, how to add custom policy, let's explore it together.
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

Underneath the previous cover, we mentioned [role-based authorization][1]. You may hear another term, which is known as ***claim-based authorization***. 

You can found this project's source code repository in [Authentication&Authorization][7].

# Claim-based Authorization Example

The principle of ***claim-based authorization*** is that to check whether has target claims.

```c#
var userClaim = new List<Claim>()
{
    new Claim(ClaimTypes.Email,"test@gmail.com")
    //...
};
```
Add a policy, which will require an Email claim type existing.

```c#
services.AddAuthorization(config=> {
    config.AddPolicy("emailRequire",policyBuilder=> {
        policyBuilder.RequireClaim(ClaimTypes.Email);
    });
});
```

Add the policy against a controller or an action.

```c#
[authorize(Policy = "emailRequire")]
public Controller EmailRequireController() 
{
}
```

The above example illustrates a simple role-based authorization, which involves another knowledge spot of this article, **Policy-Based Authorization**.

There have three primary interfaces(**[IAuthorizationService][2]**, **IAuthorizationHandler**, **[IAuthorizationRequirement][3]**), those are the foundation of the policy-based authorization.

# IAuthorizationService
The [IAuthorizationService][2]'s main responsibility is to check policy-based permission for a user.

```c#
namespace Microsoft.AspNetCore.Authorization
{
    //
    // Summary:
    //     Checks policy based permissions for a user
    public interface IAuthorizationService
    {
        //
        // Summary:
        //     Checks if a user meets a specific set of 
        //     requirements for the specified resource
        //
        // Parameters:
        //   user:
        //     The user to evaluate the requirements against.
        //
        //   resource:
        //     An optional resource the policy should be 
        //     checked with. If a resource is not
        //     required for policy evaluation you may 
        //     pass null as the value.
        //
        //   requirements:
        //     The requirements to evaluate.
        //
        // Returns:
        //     A flag indicating whether authorization 
        //     has succeeded. This value is true when
        //     the user fulfills the policy; otherwise false.
        //
        // Remarks:
        //     Resource is an optional parameter and 
        //     may be null. Please ensure that you check
        //     it is not null before acting upon it.
        Task<AuthorizationResult> AuthorizeAsync(
          ClaimsPrincipal user, 
          object resource, 
          IEnumerable<IAuthorizationRequirement> requirements);
        //
        // Summary:
        //     Checks if a user meets a specific authorization policy
        //
        // Parameters:
        //   user:
        //     The user to check the policy against.
        //
        //   resource:
        //     An optional resource the policy 
        //     should be checked with. If a resource is not
        //     required for policy evaluation 
        //     you may pass null as the value.
        //
        //   policyName:
        //     The name of the policy to check against a specific context.
        //
        // Returns:
        //     A flag indicating whether authorization
        //     has succeeded. Returns a flag indicating
        //     whether the user, and optional resource has
        //     fulfilled the policy. true when the
        //     policy has been fulfilled; otherwise false.
        //
        // Remarks:
        //     Resource is an optional parameter and may
        //     be null. Please ensure that you check
        //     it is not null before acting upon it.
        Task<AuthorizationResult> AuthorizeAsync(
          ClaimsPrincipal user, 
          object resource, 
          string policyName);
    }
}
```
The [IAuthorizationService][2] has two methods, and one of them requires a set of [IAuthorizationRequirement][3], we will talk this interface later.

The following code import from [official website][4], and it shows the simplified (and annotated with comments) default implementation of the authorization service:
```c#
public async Task<AuthorizationResult> AuthorizeAsync(
             ClaimsPrincipal user, 
             object resource,
             IEnumerable<IAuthorizationRequirement> requirements)
    {
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an 
    // IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all 
    // requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```
We can have the following inclusion that handlers will be used to authorize requirements in authorization service.
![Alt][5]

# IAuthorizationRequirement
A policy can use requirements to evaluate a user's principle. A custom requirement class needs to realize from the [IAuthorizationRequirement][3] interface.  In our "emailsPolicy" policy, we build an `EmailsRequirement` class, which can be implemented as the following code.
```c#
public class EmailsRequirement 
    : IAuthorizationRequirement {
    public List<String> emailList { get; }

    public EmailsRequirement(params string[] emails) {
        this.emailList = new List<string>(emails);
    }
}
```
The `EmailsRequirement` class has s single constructor, which accepts an array of emails, like Gmail, Outlook, etc...

# IAuthorizationHandler
The ***RequireHandler*** response to authorize the preceding defined authorization requirement. The ***RequireHandler*** needs to implement the [IAuthorizationHandler][6], whose main responsibility is to authorize is if requirements are met.

We don't need to derive our class from the `IAuthroizationHandler`, because it's a little complicated. Usually, we use another class `AuthorizationHandler<T>` instead, which is typically a one to one relationship, and it derived from `IAuthroizationHandler`. If you want to authorize multiple requirements, please directly implements from `IAuthroizationHandler` interface.
```c#
public class EmailsRequireHandler : AuthorizationHandler<EmailsRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context, 
        EmailsRequirement requirement)
    {
        List<String> userAllClaims = 
            context.User.Claims.Select(s=>s.Type).ToList();
        //check is if  userAllClaims contains all requirement items.
        bool containsAll = !requirement.emailList.Except<String>(userAllClaims).Any();
        if (containsAll) 
        {
            context.Succeed(requirement);  
        }
        return Task.CompletedTask;
    }
}
```
Regardless of implements from `AuthorizationHandler` or `IAuthorizationHandler`, both `AuthorizationHandlerContext` will be injected in via constructor dependency injection. 

If requirements are met, then invoke the `success()` method to represent authorize success. If you don't invoke the `success()` method, or invoke the `fail()` method, then it represents authorization failed.

Note:
>If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called. This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement. When set to `false`, the `InvokeHandlersAfterFailure` property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called. `InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.

# Handler registration
Handlers are registered in the services collection during configuration. we can register our above example as the following code:
```c#
public void ConfigureServices(IServiceCollection services)
{
  //database configuration
  services.AddDbContext<AppDbContext>(
      options => 
      options.UseSqlServer("Server=.;Database=CookieAuthDatabase;Trusted_Connection=True;")
  );
  //authentication
  services.
    AddAuthentication("CookieAuth").
    AddCookie("CookieAuth", config =>
    {
      config.Cookie.Name = "user.cookie";
      config.LoginPath = "/Home/Login";
      config.Cookie.MaxAge = TimeSpan.FromDays(7);
      config.ExpireTimeSpan = TimeSpan.FromDays(7);
    });
  //authorization
  services.AddAuthorization(config =>
  {
    config.AddPolicy("emailsPolicy", policyBuilder =>
    {
        policyBuilder.Requirements.Add(
            new EmailsRequirement("Gmail","Outlook","QQ"));
    });
  });
  //register our EmailsRequireHandler
  services.AddSingleton<IAuthorizationHandler, EmailsRequireHandler>();

  services.AddControllersWithViews();
  services.AddRazorPages();
}
```
The above code illustrates a simple code that illustrates how to use authorization generally. This repository [Authentication&Authorization security level example][8] also includes another project, which is more useful and convenient. In that project, you can specify a security level, which will be used to compare with the source security level. If a user's security reaches the target level, then it authorizes success, otherwise, authorizes fail.

Thanks for reading, if you have any questions, please be free to leave comments at bellow.

[1]: /csharp-asp.net%20core/2020/06/15/role-based-authorization/#policy-based-role-checks
[2]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice?view=aspnetcore-3.1
[3]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.IAuthorizationRequirement?view=aspnetcore-3.1
[4]: https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-3.1
[5]: /public/img/2020-06-18-policy-based-authorization-a.png
[6]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.IAuthorizationHandler?view=aspnetcore-3.1
[7]: https://github.com/voltwu/C-Sharp-Web-Net-Core-authentication-and-authorization
[8]: https://github.com/voltwu/C-Sharp-Web-Net-Core-authentication-and-authorization/