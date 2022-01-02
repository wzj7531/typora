# ASP.NET Core 中间件

中间件是组成应用程序管道的组件，用于处理请求和响应。对于每一个中间件组件(以下简称中间件)：

- 可以选择是否将请求传递到管道中的下一个中间件；
- 可以在调用下一个中间件之前或之后执行某个动作。

请求管道是由请求委托(Request delegate)组成的。请求委托用于处理 HTTP 请求。

可以通过 Run、Map 和 Use 这三个扩展方法添加请求委托。请求委托可以是一个匿名方法(称为内联-中间件)，也可以是一个可重用类。这些匿名方法或者可重用类即为中间件，亦可称之为中间件组件。管道中的中间件要么调用下一个中间件，要么就对管道进行短路。所谓短路，指的就是不调用后续的中间件，此时最后一个中间件称之为终点中间件。

## 使用 WebApplication 创建中间件管道

Asp.Net Core 的请求管道是由一个一个的中间件组成的，它们依次调用。下图展示了 HTTP 请求在管道中的执行过程：

![](C:\Users\Administrator\Documents\Typora\AspNetCore\中间件\request-delegate-pipeline.png)

每个中间件都可以在调用下一个中间件之前或之后执行操作。处理异常的中间件应位于管道靠前的位置，这样它就可捕获在它之后发生的所有异常。

下面的代码段展示了一个最简单的 Asp.Net Core 应用，它只有一个中间件，并未形成真正的管道，只是调用了一个匿名方法来响应所有的 HTTP 请求。

```c#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello world!");
});

app.Run();
```

使用 Use 可将多个中间件链接起来，next 参数表示管道中的下一个中间件。如果不调用 next 参数，则管道短路。通常可以在下一个委托之前和之后执行操作，如下例所示：

```c#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Use(async (context, next) =>
{
    // Do work that doesn't write to the Response.
    await next.Invoke();
    // Do logging or other work that doesn't write to the Response.
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from 2nd delegate.");
});

app.Run();
```

