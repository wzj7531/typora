# Adding & Managing Claims in ASP.NET Core Identity

In this tutorial, we will show you how to add [Claims](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#claim) in [ASP.NET Core Identity](https://www.tektutorialshub.com/asp-net-core/asp-net-core-identity/).

- The [Claim](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/#claim) is a piece of information about the user.

- A User can have multiple claims. 

- A Single claim can also have multiple values. 

 The Identity API stores the Claims in the `AspNetUserClaims` (Entity name is `IdentityUserClaim`) table. 

The User Manager Class of [Identity API](https://www.tektutorialshub.com/asp-net-core/asp-net-core-identity/) Provides the methods like `AddClaimAsync`, `ReplaceClaimAsync` & `RemoveClaimAsync`, which we will use to add/remove claims.



## Viewing the Claims of the Logged in User

AuthzExample

The Claims from the `AspNetUserClaims` table are automatically included in the cookies when we login into the system using the Identity API. But If you have built the [Cookie-based authentication system](https://www.tektutorialshub.com/asp-net-core/user-registration-login-using-cookie-authentication-asp-net-core/) or Implemented [JWT Bearer token authentication](https://www.tektutorialshub.com/asp-net-core/jwt-authentication-in-asp-net-core/), then you need to include the claims in the cookies or in the JWT Token.

The Authentication Middleware extracts the cookies (or tokens) and updates the `User` Property of the `Context` with the claims. You can read the claims from it as shown below.

```c#
app.Use(async (context, next) =>
{
    //Put a breakpoint here
    //Inspect the context.user.identies object 
    await next.Invoke();
});
```

