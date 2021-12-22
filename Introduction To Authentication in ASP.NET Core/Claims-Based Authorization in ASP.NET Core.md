# Claims-Based Authorization in ASP.NET Core

How to configure claims-based Authorization in the ASP.NET core.



## How Claims Based Authorization works

The claims-based authorization works by checking if the user has a claim to access an URL.

In ASP.NET Core we create policies to implement the Claims-Based Authorization. The policy defines what claims that user must process to satisfy the policy. We apply the policy on the Controller, action method, razor page, etc.

Only those users who carry claims, which satisfies the policy allowed to access the resources. Others are redirected to access denied page.



## Assign Claims to Users

A **Claim** is a piece of information about the user. It is consists of a Claim type and an optional value. We store it in the form of name-value pair. A Claim can be anything for example `Name` Claim, `Email` Claim, `Role` Claim, `PhoneNumber` Claim, etc.



### Storing Claims

A User can have any number of claims. The Identity API stores the claims in the `AspNetUserClaims` table. You can refer to the tutorial [how to add claims to users in Identity](https://www.tektutorialshub.com/asp-net-core/adding-managing-claims-in-asp-net-core-identity/).



### Signing Users In

The Claims are added to the **Authentication cookies** or **[JWT bearer token](https://www.tektutorialshub.com/asp-net-core/jwt-authentication-in-asp-net-core/)** (depending on which one you are using) when the user signs in sucessfully.

You can read more about [Cookie Authentication in ASP.NET Core](https://www.tektutorialshub.com/asp-net-core/user-registration-login-using-cookie-authentication-asp-net-core/) & [JWT Authentication in ASP.NET Core](https://www.tektutorialshub.com/asp-net-core/jwt-authentication-in-asp-net-core/). The [ASP.NET Core Identity](https://www.tektutorialshub.com/asp-net-core/asp-net-core-identity/) uses the Cookie Authentication.

These **cookies** or **Tokens** are then sent to the client along with the response. The Client needs to send them back with every request made to the server.

The Authentication cookies arrive as cookies. The browser does it automatically for us. But the JWT token needs to be stored somewhere safe by the client. It must include the token in the Authorisation Header on every request made to the server.



## Building Authorization Policy Using Claims

In ASP.NET Core, we cannot use claims directly in the `Authorize` attribute. But instead, we define a Policy. We map the Claim to the Policy using the `RequireClaim` method.

A Policy defines a collection of requirements, that the user must satisfy. We define the Policy in the `ConfigureServices` method of the [Startup class](https://www.tektutorialshub.com/asp-net-core/asp-net-core-startup-class/) using the [AddAuthoirization](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.authorizationservicecollectionextensions.addauthorization?view=aspnetcore-2.2) extension method.

The following code creates a `AdminOnly` policy. The policy requires the claim `Admin`.

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();
 
    services.AddAuthorization(options =>
    {
        options.AddPolicy("AdminOnly", policy => policy.RequireClaim("Admin"));
    });
}
```



### Policy with Single Claim

The following policy creates an `AdminOnly` Policy. The user must have the `Admin` claim. The value of the Claim does not matter here.

```c#
options.AddPolicy("AdminOnly", policy => policy.RequireClaim("Admin"));
```



### Policy with Claim & Value

The following is the `ITOnly` Policy, which requires the user the have a claim `Permission` with the value `IT`. The `Permission` claim with any other value is not allowed.

```c#
options.AddPolicy("ITOnly", policy => policy.RequireClaim("Permission", "IT"));
```



### Policy with Multiple Claims

You can chain multiple Claims together as shown below. The `SuperIT` policy requires user the have `Permission` claim with Value `IT` and separate `IT` claim. The user must satisfy both conditions.

```c#
options.AddPolicy("SuperIT", 
        policy => policy.RequireClaim("Permission", "IT")
                        .RequireClaim("IT"));
```



## Securing the End Points with Claims

To secure an endpoint, we apply the Policy using the `Authorize` attribute.

The following example shows how to use policy to secure the `SomeSecureAction`

```c#
[Authorize(Policy = "AdminOnly")]
public IActionResult SomeSecureAction()
{
    return View();
}
```

