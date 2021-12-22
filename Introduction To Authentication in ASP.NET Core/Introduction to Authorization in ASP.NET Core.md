# Introduction to Authorization in ASP.NET Core

Authorization in ASP.NET Core determines whether a user can access a particular route, [controller](https://www.tektutorialshub.com/asp-net-core/asp-net-core-controllers/), controller action, Resource, etc.



## What is Authorization

Authorization is the process, which determines what the user **can do** or **cannot do**.

It is different from [Authentication](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/), which determines who the user is.

[Authentication](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/) always comes first, before Authorization. You are authorized only if you are Authenticated.



## Authentication Recap

Authorization depends on [Authentication](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/) to identify the user.

In ASP.NET Core we add the Authentication to [Middleware pipeline](https://www.tektutorialshub.com/asp-net-core/asp-net-core-middleware-request-pipeline/) using the `UseAuthentication` method.

```c#
    app.UseRouting();
 
    app.UseAuthentication();
    app.UseAuthorization();
 
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
```

We usually add it after the `UseRouting`, so that the [authentication middleware](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#authentication-basics) knows about the URL being accessed by the User.

The purpose of the [Authentication Middleware](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#authentication-middleware) is to update the `HttpContext.User` Property with the information about the user ( `ClaimsPrincipal` ). The `ClaimsPrincipal` contains the `claims` of the users.

All the middleware, which needs to know who the `User` is (For Example Authorization middleware) must come after the `UseAuthentication`.

The Authorization Middleware reads the `ClaimsPrincipal` from `HttpContext.User` and uses it to check whether the user is authorized.



## Authorization Strategies

We can use Authorization services in several ways to build a robust authorization system.

### Declarative Authorization

In this mode, we use the `Authorize` attribute to secure a page. The `Authorize` attribute describes how the page needs to be secured. The Authorization Middleware will read the attribute and figures out how to secure the page.

You can configure the Declarative Authorization using the following ways:

1. Simple Authorization
2. Claim based Authorization
3. Role based Authorization
4. Policy based Authorization



#### Simple Authorization

Simple Authorization, we apply `[Authorize]` attribute to a controller, action, or Razor Page. This will stop the unauthenticated users from accessing the page.

```
[Authorize]
public class HomeController : Controller
{
    public ActionResult Login()
    {
    }
 
    public ActionResult Logout()
    {
    }
}
```



#### Claim based Authorization

In a claim based Authorization strategy, we build an Authorization Policy and map it to a claim (or claims) in the `ConfigureServices` method of the [startup class](https://www.tektutorialshub.com/asp-net-core/asp-net-core-startup-class/).

```c#
    services.AddAuthorization(options =>
    {
        options.AddPolicy("AdminOnly", policy => policy.RequireClaim("Admin"));
    });
```

We apply the policy to a controller, action, or Razor Page using the `Authorize` attribute. This will not only stop the unauthenticated users but also the authenticated users if they do carry the `Admin` claim.

```c#
[Authorize(Policy = "AdminOnly")]
public class AdminController : Controller
{
    public ActionResult Login()
    {
    }
 
    public ActionResult Logout()
    {
    }
}
```

Note that Claims cannot be used directly using the `Authorize` attribute. You need to build a policy to use it.



#### Role based Authorization

The Role-based Authorization is very similar to the Claim-based Authorization, except for it uses the Roles instead of Claims.

Another difference is that you can use the Roles directly using the `Authorize` attribute.

```c#
[Authorize(Roles = "Admin")]
public class AdminController : Controller
{
}
```

You can also create a Policy using the `RequireRole` in the startup class. and use the policy instead of Roles in the Controller.

```c#
    services.AddAuthorization(options =>
    {
        options.AddPolicy("AdminPolicy",
             policy => policy.RequireRole("Admin"));
    });
```

```c#
[Authorize(Policy = "Admin")]
public class AdminController : Controller
{
}
```

Role-based authorization exists because of backward compatibility. It is better to avoid using it as it may be deprecated in the near future.



#### Policy based Authorization

Both claim-based & Role-based authorizations use the Policy behind the scenes. But if we run into complex situations, then we need to build a custom policy-based authorization scheme.

The building blocks of Policy authorization is **policy**, **requirement** & **requirement handler**.

The `requirement` defines the authorization requirement.

The `requirement handler` contains the logic that checks the **requirement**. A requirement can have more than one handler.

A **Policy** can contain more than one requirement.



### Imperative Authorization

#### Resource-based authorization Using Policies

The Authorization middleware, which uses the `Authorize` attribute to check for permissions runs it much before the execution of the page handler or the action method. Hence it does not have the access to the data or resource on which the page or action method operates. 

For Example, take a document that has an author. Only the Author can edit the document. The other users can only view the document. To implement the above security, we need to retrieve the document from the server, check its author and then decide whether to allow edit or not. The declarative authorization with `Authorize` attribute cannot handle this situation.

Hence, we write the code to validate the user right in the method itself by injecting the Authorization service into the controller/ page. Then we use the `AuthorizeAsync` method to manually trigger an authorization.



## How it works

1. The [UseRouting](https://www.tektutorialshub.com/asp-net-core/asp-net-core-endpoint-routing/) resolves the parses incoming HTTP requests and constructs an [Endpoint](https://www.tektutorialshub.com/asp-net-core/asp-net-core-endpoint-routing/#what-is-an-endpoint).
2. [Authentication Middleware](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#authentication-middleware) constructs the [ClaimsPrincipal](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#claimsprincipal) from the [Cookies](https://www.tektutorialshub.com/asp-net-core/user-registration-login-using-cookie-authentication-asp-net-core/) or [JWT Token](https://www.tektutorialshub.com/asp-net-core/jwt-authentication-in-asp-net-core/) and updates the `HttpContext.User`
3. The request reaches the Authorization Middleware. It checks whether to allow access to URL to the user
4. If authorization is successful, the request proceeds normally to the endpoint middleware and completes
5. But, if authorization fails, the middleware returns an error

If authorization fails, then there are two possibilities. Either the user is not authenticated or he is authenticated but not authorized.



### The User is not authenticated

If the user is not authenticated, the Authorization middleware invokes the `ChallengeResult` on the [Authentication handler](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#authentication-handlers).

In a web application, the handler will redirect the user to the login page. In an API application, it returns a 401 Unauthorized error response.



### The user is not authorized

If the user is authenticated but not authorized, the Authorization middleware invokes the `ForbidResult` on the [Authentication handler](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#authentication-handlers).

In a web application, the handler will redirect the user to the Access Denied web page. In an API application, it returns a 403 Forbidden error response.