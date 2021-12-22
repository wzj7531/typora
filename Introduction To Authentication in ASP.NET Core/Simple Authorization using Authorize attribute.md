# Simple Authorization using Authorize attribute

How to implement basic Authorization using Authorize attribute.

The `Authorize` attribute restricts the unauthenticated user from accessing the URL. You can override it with `AllowAnonymous` attribute.



## Authorize Attribute

We use [`Authorize` attribute](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-5.0) to decorate a controller, action, or Razor page. This will limit access to that only to the authenticated user. The unauthenticated users are redirected to the login page.

```c#
[Authorize]
public class AccountController : Controller
{
    ...
}
```



## Securing a MVC/API Controller

Example Project [AuthzExample]

Add the `[Authorize]` attribute to the `MVCProductsController` class. This will stop the unauthenticated users from accessing the `MVCProductsController`.

When we apply the `Authorize` class, all the action methods of that controller automatically inherit it. The Authorize attribute lives in the namespace `Microsoft.AspNetCore.Authorization;`

```c#
[Authorize]
public class ProductsController : Controller
{
```

Now, when you navigate to `/Products` page, you are redirected to `/Identity/Account/Login?ReturnUrl=%2FProducts` page.

Note that the `MVCProducts` is passed as the `ReturnUrl` to the login page. Once you are completed the login you are redirected back to the login page.

Now, what if we allow unauthenticated users to view the Product List & Details page, but restrict Create & Edit Page.



## AllowAnonymous Attribute

`AllowAnonymous` attribute overrides `Authorize` attribute and allows access to unauthenticated users.

Appy the `AllowAnonymous` attribute on the `Index` action method. Now you can navigate to the Products page. But you cannot navigate to `Products/Create` page.

```c#
[Authorize]
public class MVCProductsController : Controller
{
    ......
    // GET: MVCProducts
    [AllowAnonymous]
    public async Task<IActionResult> Index()
    {
        var applicationDbContext = _context.Products.Include(p => p.User);
        return View(await applicationDbContext.ToListAsync());
    }
    ......
```

**`AllowAnonymous` overrides everything**.

If we apply the `AllowAnonymous` controller class access to all the methods are allowed, even if you decorate the action methods with `Authorize` attribute.

```c#
[AllowAnonymous]
public class MVCProductsController : Controller
{
    private readonly ApplicationDbContext _context;
 
    public MVCProductsController(ApplicationDbContext context)
    {
        _context = context;
    }
 
    // GET: MVCProducts
    [Authorize]
    public async Task<IActionResult> Index()
    {
        var applicationDbContext = _context.Products.Include(p => p.User);
        return View(await applicationDbContext.ToListAsync());
    }
```

If you apply both `AllowAnonymous` and `Authorize` attribute, then `Authorize` is ignored.



## Securing a Razor Page

Apply `Authorize` attribute to the Page Model.

```c#
[Authorize]
public class CreateModel : PageModel
{
    ......
    public IActionResult OnGet()
    {
    ViewData["userId"] = new SelectList(_context.Users, "Id", "Id");
        return Page();
    }
    ......
```

We cannot apply the `Authorize` attribute to Razor Page handlers.

For Example, in the code above, we have `OnGet` & `OnPost` page handlers. We cannot use `Authorize` attribute on them.

In case, you want to apply attributes on Razor Page Handlers, then Create a separate page for each page handler.



## Adding Authorize attribute globally

We can apply authorization globally using the `RequireAuthorization` extension method. We apply this method while configuring the end points.

```c#
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}"
        ).RequireAuthorization(); 
 
    endpoints.MapRazorPages().RequireAuthorization(); 
});
```

Note, that this will apply the `Authorize` attribute on all the pages and controllers. Hence you need to add the **`AllowAnonymous`** attribute to those publically accessible pages. For Example, login, home & register pages.

```c#
[AllowAnonymous]
public class HomeController : Controller
{
```

Login & Register pages come from the identity API and already has the `AllowAnonymous` attribute.



## Razor Pages Authorization Conventions

By using the `AuthorizePage`, `AuthorizeFolder`, `AllowAnonymousToPage`, `AllowAnonymousToFolder` ,`AuthorizeAreaPage` & `AuthorizeAreaFolder` options, we can add the `Authorize` or `AllowAnonymous` attributes to the pages.

```c#
services.AddRazorPages(options =>
{
    options.Conventions.AuthorizePage("/Contact");
    options.Conventions.AuthorizeFolder("/Private");
    options.Conventions.AllowAnonymousToPage("/Private/PublicPage");
    options.Conventions.AllowAnonymousToFolder("/Private/PublicPages");
});
```

Applying the method names that ends with `Folder` (ex: `AuthorizeFolder` or `AuthorizeAreaFolder`) will apply the attribute to all the pages under the folder. While method names that end with `Page` will apply the attribute to only that page.

