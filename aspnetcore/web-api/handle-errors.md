---
title: Handle errors in ASP.NET Core web APIs
author: pranavkm
description: Learn about error handling with ASP.NET Core web APIs.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/27/2019
uid: web-api/handle-errors
ms.openlocfilehash: 457ad7449c608c3b1b0acd729626e07808f55897
ms.sourcegitcommit: ddc813f0f1fb293861a01597532919945b0e7fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74412101"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="18014-103">Handle errors in ASP.NET Core web APIs</span><span class="sxs-lookup"><span data-stu-id="18014-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="18014-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span><span class="sxs-lookup"><span data-stu-id="18014-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="18014-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18014-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="18014-106">Developer Exception Page</span><span class="sxs-lookup"><span data-stu-id="18014-106">Developer Exception Page</span></span>

<span data-ttu-id="18014-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span><span class="sxs-lookup"><span data-stu-id="18014-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="18014-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span><span class="sxs-lookup"><span data-stu-id="18014-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="18014-109">To illustrate, consider the following controller action:</span><span class="sxs-lookup"><span data-stu-id="18014-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="18014-110">Run the following `curl` command to test the preceding action:</span><span class="sxs-lookup"><span data-stu-id="18014-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18014-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span><span class="sxs-lookup"><span data-stu-id="18014-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="18014-112">The following output appears:</span><span class="sxs-lookup"><span data-stu-id="18014-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="18014-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span><span class="sxs-lookup"><span data-stu-id="18014-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="18014-114">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="18014-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="18014-115">Consider the following excerpt from the HTTP response:</span><span class="sxs-lookup"><span data-stu-id="18014-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="18014-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span><span class="sxs-lookup"><span data-stu-id="18014-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="18014-117">For example, consider the following excerpt from the HTTP response:</span><span class="sxs-lookup"><span data-stu-id="18014-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18014-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span><span class="sxs-lookup"><span data-stu-id="18014-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="18014-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span><span class="sxs-lookup"><span data-stu-id="18014-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Developer Exception Page testing in Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="18014-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span><span class="sxs-lookup"><span data-stu-id="18014-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="18014-122">You don't want to share detailed exception information publicly when the app runs in production.</span><span class="sxs-lookup"><span data-stu-id="18014-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="18014-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="18014-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="18014-124">Exception handler</span><span class="sxs-lookup"><span data-stu-id="18014-124">Exception handler</span></span>

<span data-ttu-id="18014-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span><span class="sxs-lookup"><span data-stu-id="18014-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="18014-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span><span class="sxs-lookup"><span data-stu-id="18014-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="18014-127">Configure a controller action to respond to the `/error` route:</span><span class="sxs-lookup"><span data-stu-id="18014-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="18014-128">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span><span class="sxs-lookup"><span data-stu-id="18014-128">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="18014-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span><span class="sxs-lookup"><span data-stu-id="18014-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="18014-130">Use the following steps to produce a consistent payload format across development and production environments:</span><span class="sxs-lookup"><span data-stu-id="18014-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="18014-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span><span class="sxs-lookup"><span data-stu-id="18014-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="18014-132">In the preceding code, the middleware is registered with:</span><span class="sxs-lookup"><span data-stu-id="18014-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="18014-133">A route of `/error-local-development` in the Development environment.</span><span class="sxs-lookup"><span data-stu-id="18014-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="18014-134">A route of `/error` in environments that aren't Development.</span><span class="sxs-lookup"><span data-stu-id="18014-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="18014-135">Apply attribute routing to controller actions:</span><span class="sxs-lookup"><span data-stu-id="18014-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="18014-136">Use exceptions to modify the response</span><span class="sxs-lookup"><span data-stu-id="18014-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="18014-137">The contents of the response can be modified from outside of the controller.</span><span class="sxs-lookup"><span data-stu-id="18014-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="18014-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span><span class="sxs-lookup"><span data-stu-id="18014-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="18014-139">ASP.NET Core doesn't include an equivalent type.</span><span class="sxs-lookup"><span data-stu-id="18014-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="18014-140">Support for `HttpResponseException` can be added with the following steps:</span><span class="sxs-lookup"><span data-stu-id="18014-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="18014-141">Create a well-known exception type named `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="18014-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="18014-142">Create an action filter named `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="18014-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="18014-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span><span class="sxs-lookup"><span data-stu-id="18014-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="18014-144">Validation failure error response</span><span class="sxs-lookup"><span data-stu-id="18014-144">Validation failure error response</span></span>

<span data-ttu-id="18014-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span><span class="sxs-lookup"><span data-stu-id="18014-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="18014-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span><span class="sxs-lookup"><span data-stu-id="18014-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="18014-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18014-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="18014-148">Client error response</span><span class="sxs-lookup"><span data-stu-id="18014-148">Client error response</span></span>

<span data-ttu-id="18014-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span><span class="sxs-lookup"><span data-stu-id="18014-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="18014-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="18014-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18014-151">The error response can be configured in one of the following ways:</span><span class="sxs-lookup"><span data-stu-id="18014-151">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="18014-152">Implement ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="18014-152">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="18014-153">Use ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="18014-153">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="18014-154">Implement ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="18014-154">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="18014-155">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="18014-155">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="18014-156">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span><span class="sxs-lookup"><span data-stu-id="18014-156">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="18014-157">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18014-157">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="18014-158">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span><span class="sxs-lookup"><span data-stu-id="18014-158">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="18014-159">Use ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="18014-159">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="18014-160">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span><span class="sxs-lookup"><span data-stu-id="18014-160">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="18014-161">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span><span class="sxs-lookup"><span data-stu-id="18014-161">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
