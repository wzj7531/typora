# Resource-Based Authorization in ASP.NET Core

In this article, we will show you how to implement Resource-based authorization in ASP.NET Core. In Resource-based authorization, we inject the Authorization Service in the controller or Razor Page. We then call the `AuthorizeAsync` method to run an authorization check.

## Resource-Based Authorization

The [authorization policies](https://www.tektutorialshub.com/asp-net-core/policy-based-authorization-in-asp-net-core/) that we built in the previous tutorial depend on `Authorize` attribute. The [Authorization middleware](https://www.tektutorialshub.com/asp-net-core/introduction-to-authorization-in-asp-net-core/) reads the `Authorize` attribute and runs the checks to see if it can authorize the user.

The Authorization middleware runs before data binding and before execution of the action method (or page handler). Hence it does not have access to the data (resources) on which the action method operates.

But, there are many use cases that exist, where we need to load resources first, before determining if we can allow the user to access those resources. The `Attribute`-based authorization mechanism does not handle such use cases.

For Example, we want to allow only the creator of the document to edit or delete it. The other users can only view the document. In this scenario, we need to load the document first. We will then check if the creator of the document is the same as the logged-in user. Only then we can allow users to access the document.



## How to Create resource based Authorization

Resource-based on [Authorization](https://www.tektutorialshub.com/asp-net-core/introduction-to-authorization-in-asp-net-core/) is an *imperative authorization*. Here we do not rely on the `Authorize` attribute, But we choose when to call the Authorization Service.

We do that by injecting the Authorization Service into our controller class. In the action method, we load the resource first. After that, we call the `AuthorizeAsync` method passing the resource to it along with the policy that we want to apply. Once the method returns the result, we check it and take appropriate action.

To Create a Resource-based Authorization.

1. Create an Authorization Requirement. The Requirement class must inherit from the [IAuthorizationRequirement](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement?view=aspnetcore-5.0)
2. Next, create Authorization Handlers. The class must inherits `AuthorizationHandler<SameAuthorRequirement, Document>`
3. Register the Authorization Handlers for DI
4. Create a Policy using the Authorization Requirement
5. Inject the Authorization Service in the controller class
6. Call the `AuthorizeAsync` method, wherever you want to check for Authorization.



## Resource based Authorization Example

***See AuthzExample***

