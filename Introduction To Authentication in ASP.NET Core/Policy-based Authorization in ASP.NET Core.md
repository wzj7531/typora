# Policy-based Authorization in ASP.NET Core

In this tutorial. we are going to look at the Policy-based Authorization in [ASP.NET Core](https://www.tektutorialshub.com/asp-net-core-tutorial/). We learned [Claim based authorization](https://www.tektutorialshub.com/asp-net-core/claims-based-authorization-in-asp-net-core/) in the previous tutorial. We can create simple policies using that approach and it is sufficient to handle most of the situations. But if we run into complex authorization requirements, then we need to build a custom policy-based authorization.



## Authorization Policy

Authorization Policies are the backbone of the [ASP.NET Core Authorization](https://www.tektutorialshub.com/asp-net-core/introduction-to-authorization-in-asp-net-core/) Framework. Even when you use [claim-based](https://www.tektutorialshub.com/asp-net-core/claims-based-authorization-in-asp-net-core/) or role-based authorization, you are actually using Policy-based Authorization.

A Policy defines a **collection of requirements**, that the user must satisfy in order to access a resource. The user must satisfy all the requirements.

We Add the policy using the `AddAuthorization` method in the `ConfigureServices` of the [startup class](https://www.tektutorialshub.com/asp-net-core/asp-net-core-startup-class/).

```c#
services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy => policy.RequireClaim("Admin"));
 
});
```

The policies can be simple as calling `RequireClaim` on policy builder. You can also create complex policies by creating requirements and handlers.



## Simple Authorization Policies

The following policy creates an `AdminOnly` Policy. The user must have the `Admin` [claim](https://www.tektutorialshub.com/asp-net-core/authentication-in-asp-net-core/). The value of the Claim does not matter here.

```c#
options.AddPolicy("AdminOnly", policy => policy.RequireClaim("Admin"));
```



### Policy with Single Claim & Value

The following is the `ITOnly` Policy, which requires the user the have a claim `Permission` with the value `IT`. The `Permission` claim with any other value is not allowed.

```c#
options.AddPolicy("ITOnly", policy => policy.RequireClaim("Permission", "IT"));
```



### Policy with Multiple Claims

You can chain multiple Claims together as shown below. The `SuperIT` policy requires user the have `Permission` claim with Value `IT` and separate `IT` claim. The **user must satisfy both conditions**.

```c#
options.AddPolicy("SuperIT", 
        policy => policy.RequireClaim("Permission", "IT")
                        .RequireClaim("IT"));
```



### Policy only for Authorized Users

This policy is similar to adding `Authorize` attribute.

```c#
options.AddPolicy("AuthUsers", policy => policy.RequireAuthenticatedUser());
```



### Policy using a Role

```c#
options.AddPolicy("AdminRole", policy => policy.RequireRole("AdminRole"));
```



### Policy using a User Name

```c#
options.AddPolicy("BobOnly", policy => policy.RequireUserName("Bob"));
```



## Applying the Authorization Policy

We apply the policy using the Authorize attribute.

The following code applies the `AdminOnly` policy to the `SomeSecureAction`

```c#
[Authorize(Policy = "AdminOnly")]
public IActionResult SomeSecureAction()
{
    return View();
}
```



## Custom Policy using a Func

The Policies, we created above are simple and straightforward. But if you have little complex authorization requirements, then you can create a custom function and use the method in `RequireAssertion`.

The custom lambda function must return a `true` if the policy is satisfied.

```c#
options.AddPolicy(
        "SuperUser",
        policyBuilder => policyBuilder.RequireAssertion(
            context => context.User.HasClaim(claim => claim.Type == "Admin")
                || context.User.HasClaim(claim => claim.Type == "IT")
                || context.User.IsInRole("CEO"))
    );
```



## Custom Policy using requirement & Handlers

We can create a custom Policy by using the **Authorization Requirement** & **Authorization Handler**.



### Authorization Requirement

The Authorization Requirement defines the collection of conditions that the policy must evaluate.  For the Policy to be successful, it must satisfy all the requirements. ***It is similar to AND Condition. If one of the requirements fails. then the policy fails.***

The following diagram describes the structure of a Policy using Requirement. The Policy contains three requirements.

For The policy to be successful all three requirements must return success. It is like a AND Condition. If one of them fails, then the Policy fails.

![Authorization Policy Using Requirement and Handler in ASP.NET Core](C:\Users\03010673\Documents\typora\Introduction To Authentication in ASP.NET Core\Authorization-Policy-Using-Requirement-and-Handler-in-ASP.NET-Core.png)



### Authorization Handler

The authorization handler contains the logic to checks to see if the **requirement** is Valid. An Authorization Requirement can contain more than one Authorization Handler.

An Authorization Handler can return one of the three values

1. Fail
2. Succeded
3. Do Nothing.

***If one of the handlers return `fail`, then the requirement `fails`.*** It Fails irrespective of the results of other handlers.

***If none of the handlers fails, but at least one of handler returns Success then the requirement is `Success`.***

***If none of the handlers return a value, then the requirement `fails`.***

Note that all the handlers are invoked, irrespective of the outcome.



## Example of Requirement & Requirement handler

Consider the following example.

To create a new product in a portal, the user must satisfy the following conditions.

1. The user is an employee of the Organization. Carry an `Employee` claim.
2. The user is a VIP Customer. Carry an `VIP` Claim.
3. But, the user is not allowed to post if his account is disabled due to bad reviews. The disabled account carry `Disabled` claim.

The above conditions represent a policy. We call it as `canManageProduct` policy

Each of the above statements is our Handlers. Hence we have three handlers.

1. IsEmployeeHandler
2. IsVIPCustomerHandler
3. IsAccountNotDisabledHandler

We can, in fact, create a single handler and test all of the above. That will serve our purpose for this tutorial. But in a real-world application, you need to look at reusing the handlers in another requirement. Hence it makes sense to split it up into smaller pieces.

In the above, if `IsEmployeeHandler` OR `IsVIPCustomerHandler` returns true, then the user can edit the Product. Hence these two falls under a single requirement.

But the `IsAccountNotDisabledHandler` is different. If the Account is disabled, then the user must not be allowed to create the Product. Hence it must return `success`.

- `IsAllowedToManageProductRequirement`
  - `IsEmployeeHandler` returns `sucess` else nothing
  - `IsVIPCustomerHandler` returns `sucess` else nothing
- `IsAccountEnabledRequirement`
  - `IsAccountNotDisabledHandler` returns `success` else nothing



***See AuthzExample*** 

