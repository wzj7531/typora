# Introduction to Authentication in ASP.NET Core

## What is Authentication

***Authentication is the process of verifying the identity of an individual. It is about determining who the user is.***

There are [many ways to handle Authentication in web applications](https://medium.com/@vivekmadurai/different-ways-to-authenticate-a-web-application-e8f3875c254a). For Example

- Cookie-Based authentication
- Token-Based authentication
- Third-party access(OAuth, API-token)
- OpenId
- SAML

## What is Authorization

***Authorization determines what the user allowed to do.***

 Before determining what the user is can do, you must know who the user is. Hence the authentication comes first, before authorization.

## Claims, ClaimsIdentity & ClaimsPrincipal

The ASP.NET Core uses ***claims-based*** authentication. To understand it, first, we need to understand what is a `Claim`, `ClaimsIdentity` & `ClaimsPrincipal`

### Claim

***A Claim is a piece of information about the user***. It is consists of a Claim type and an optional value. We store it in the form of name-value pair. A Claim can be anything for example `Name` Claim, `Email` Claim, `Role` Claim, `PhoneNumber` Claim, etc.

The Authorization Module uses the Claims to check whether the user has the rights to access the resources.

### ClaimsIdentity

The **ClaimsIdentity** is a collection of **Claims**.

For Example take your driving license. It has claims like `FirstName`, `LastName`, `DateOfBirth` etc. Hence the driving license is the ClaimsIdentity.

### ClaimsPrincipal

**ClaimsPrincipal** contains a collection of **ClaimsIdentity**. You can think of the **ClaimsPrincipal** (or **Principal** or **Identity**) as the user of your app.

A User can have more than one ClaimsIdentity. For Example, both driving license & Passport.

A driving license is a `ClaimsIdentity` with claims like `FirstName`, `LastName`, `DateOfBirth` & `DLNo`, etc Passport is another `ClaimsIdentity` with claims like `FirstName`, `LastName`, `Address`, `PassportNo`, etc.

Both identifies the same user or `ClaimsPrincipal`.

![](C:\Users\03010673\Documents\typora\Introduction To Authentication in ASP.NET Core\Claims-Based-Authentication-in-ASP.NET-Core.png)

## Reading the ClaimsPrincipal

We Read the `ClaimsPrincipal` from `User` Property of the `HttpContext` object.

Every Request that we receive will have a `HttpContext` object. It holds the information about the current HTTP request.

The `HttpContext` object also exposes the `ClaimsPrincipal` as the `User` property. 

We register the Authentication Middleware in the [Middleware pipeline](https://www.tektutorialshub.com/asp-net-core/asp-net-core-middleware-request-pipeline/) using the `UseAuthentication()` Method.

There are several ways, in which users can sign in to the system. Cookie-Based authentication & JWT Token-Based authentication are the popular ones. But How does our Authentication Middleware know which one to use to populate the `User` Property ?.

Well it uses the **Authentication Scheme** & **Authentication Handlers**

## Authentication Basics

The ASP.NET Code uses the **Authentication Handlers** to handle the Authentication.

A Authentication Handler is responsible for

1. Authenticate a user ( Using the [AuthenticateAsync()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.iauthenticationhandler.authenticateasync?view=aspnetcore-5.0#Microsoft_AspNetCore_Authentication_IAuthenticationHandler_AuthenticateAsync) method ).
2. If the User is unauthenticated, redirect the user to the login page (Challenge the current request using the `ChallengeAsync()` method)
3. If User is Authenticated, but not authorized then Forbid the Current Request (Using the `ForbidAsync()` method)

```c#
public void ConfigureServices(IServiceCollection services)
{
   ...
 
   services.AddAuthentication()
      .AddJwtBearer()
      .AddCookie();
 
   ..
   ..
}
```

The [AuthenticateAsync()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.iauthenticationhandler.authenticateasync?view=aspnetcore-5.0#Microsoft_AspNetCore_Authentication_IAuthenticationHandler_AuthenticateAsync) method of the Authentication Handlers is responsible for constructing the `ClaimsPrincipal` from the Request and return it to the Authentication Middleware. The Authentication middleware then sets the `HttpContext.User` Property with the `ClaimsPrincipal`.

For Example, `AuthenticateAsync()` method of the cookie authentication handler must read the cookies from the current request, construct the `ClaimsPrincipal`, and return it. Similarly, the JWT bearer handler must deserialize and validate the JWT bearer token, construct the `ClaimsPrincipal` and return it.

### Authentication Scheme

Each Authentication handler that we register using the `AddAuthentication` method becomes a new **Authentication Scheme**.

A **Authentication scheme** consists of

1. A unique Name, which identifies the **Authentication scheme**
2. Authentication Handler
3. Options for configuring that specific instance of the handler

In the example, we have registered two authentication schemes. But neither, we have given them any name nor configured any of their options.

```c#
 services.AddAuthentication()
      .AddJwtBearer()
      .AddCookie();
```

Each of those Authentication handlers comes with their own defaults. The Cookie Authentication handler defines all its defaults in the `CookieAuthenticationDefaults` class. Similarly, the `JwtBearer` uses the [JwtBearerDefaults](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.jwtbearer.jwtbearerdefaults?view=aspnetcore-5.0) class.

The Default name for *Cookie Authentication* is “**Cookies**” (`CookieAuthenticationDefaults.AuthenticationScheme`) and for *JwtBearer Authentication handler* uses the **“Bearer”**.([JwtBearerDefaults.AuthenticationScheme](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.jwtbearer.jwtbearerdefaults.authenticationscheme?view=aspnetcore-5.0)).

The following code is the same as the one above.

```
services.AddAuthentication()
    .AddJwtBearer("Bearer")
    .AddCookie("Cookies");
```

You can also define a particular handler more than once. The Example below defines the *Cookie Authentication* twice but gives both of them different names. Hence now we have **three Authentication schemes**.

```c#
services.AddAuthentication()
    .AddJwtBearer("Bearer")
    .AddCookie("Cookies")
    .AddCookie("Cookies2")
```

### Default Authentication scheme

When we use more than one Authentication Schemes, we also need to configure one of them as default authentication. We do that by providing the **Authentication scheme** name as the first argument to the `AddAuthentication` method.

The following example, sets the `Cookies2` as the default **Authentication scheme**.

```c#
services.AddAuthentication("Cookies2")
    .AddJwtBearer("Bearer")
    .AddCookie("Cookies")
    .AddCookie("Cookies2")
```

This code sets the “**Cookies**” as the default Authentication scheme.

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddJwtBearer("Bearer")
    .AddCookie("Cookies")
    .AddCookie("Cookies2")
```

### Authentication Handler Options

Each Authentication handler comes with options.

Configuring the Cookie Authentication Handler.

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie("Cookies", options =>
    {
        options.LoginPath = "/Account/Login";
        options.LogoutPath = "/Account/Logout";
        options.AccessDeniedPath = "/Account/AccessDenied";
        options.ReturnUrlParameter = "ReturnUrl";
    });
```

Configuring the JWT bearer Authentication Handler.

```c#
services.AddAuthentication(x =>
    .AddJwtBearer(x =>
    {
        x.RequireHttpsMetadata = true;
        x.SaveToken = true;
        x.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidIssuer = jwtTokenConfig.Issuer,
            ValidateAudience = true,
            ValidAudience = jwtTokenConfig.Audience,
            ValidateIssuerSigningKey = true,
            RequireExpirationTime = false,
            ValidateLifetime = true,
            ClockSkew = TimeSpan.Zero,
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(jwtTokenConfig.Secret))
        };
    });       
```

### Authentication Middleware

`UseAuthentication()` registers the Authentication Middleware.

***The main purpose of the Authentication Middleware is to update the `HttpContext.User` Property with the `ClaimsPrincipal`. To do that, it uses the default Authentication Handler and invokes the `AuthenticateAsync()` method. As mentioned earlier, The `AuthenticateAsync()` method of the Authentication Handler must return the `ClaimsPrincipal`.***

We place the Authentication Middleware after [EndPoint Routing](https://www.tektutorialshub.com/asp-net-core/asp-net-core-endpoint-routing/). Hence it knows which controller action method is handling the request.

It must come before Authorization Middleware ( `UseAuthorization()`) & [End Point Middleware](https://www.tektutorialshub.com/asp-net-core/asp-net-core-endpoint-routing/) (`UseEndPoints()`).

All middleware’s which appear after the `UseAuthentication()` in the [middleware pipeline](https://www.tektutorialshub.com/asp-net-core/asp-net-core-middleware-request-pipeline/) can check whether the user is authenticated by inspecting the `HttpContext.User` Property.

startup.cs

```c#
 app.UseRouting();
 
 app.UseAuthentication();
 app.UseAuthorization();
 
 app.UseEndpoints(endpoints =>
 {
     endpoints.MapRazorPages();
 });
```

## How Authentication & Authorization Works

### Unauthenticated Request

When a request arrives from a user who is unauthenticated to a page that is protected.

1. The Request reaches the Authentication Middleware.
2. The Authentication Middleware checks to see if a proper credential present in the request. It will use the **default authentication handler** to do that. It could be a Cookies handler/ Jwt handler. Since it does not find any credential, it will set the User Property to an anonymous user.
3. Authorization Middleware (UseAuthorization()) checks to see if the destination page needs Authorization.
   1. If No then the user is allowed to visit the Page
   2. If Yes it invokes the `ChallengeAsync()` on the Authentication Handler. It redirects the user to Login Page

### Signing In

1. User Presents the Id & Password in the Login Form and clicks on the Login button.
2. The Request hits the sign-in endpoint after going through the Authentication & Authorization Middlewares. Hence the sign-in endpoint must have `Allowanonymous` decorator else request will never reach it.
3. Users id & password is validated against the database.
4. If the Cookie Authentication handler is used
   1. Creates a `ClaimsPrincipal` of the user with the claims of the User
   2. Use the `HttpContext.SignInAsync` to creates an encrypted cookie and adds it to the current response.
   3. The Response is returned to the Browser
   4. The Browser stores the cookie
5. If the JWT Bearer Authentication handler is used
   1. Creates a JWT Token of the user with the claims of the User
   2. JWT Token is sent to the user as a response
   3. The Users reads the token and stores it in Local storage, session storage, or even in cookies
6. The user is now authenticated.

### Authenticating the Subsequent Requests

1. The user makes a request to protect the page.
2. If you are using cookie authentication, then you do not have to do anything. The Browser will automatically include the cookie with every request. But in the case of the JWT token, you need to include the token in `Authorization` header.
3. The Request reaches the Authentication Middleware. It will use the default Authenticate handler to read the cookie / JWT Token and constructs the `ClaimsIdentity` and updates the `HttpContext.User` property with it
4. The Authorization Middleware sees that the user is authenticated by inspecting the `HttpContext.User` property and allows access to it.