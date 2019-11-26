---
title: Model Binding in ASP.NET Core
author: rick-anderson
description: Learn how model binding in ASP.NET Core works and how to customize its behavior.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 823d92c279454fc6c744eebbecf4268412774eba
ms.sourcegitcommit: a104ba258ae7c0b3ee7c6fa7eaea1ddeb8b6eb73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478710"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="18d36-103">Model Binding in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18d36-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="18d36-104">This article explains what model binding is, how it works, and how to customize its behavior.</span><span class="sxs-lookup"><span data-stu-id="18d36-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="18d36-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18d36-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="18d36-106">What is Model binding</span><span class="sxs-lookup"><span data-stu-id="18d36-106">What is Model binding</span></span>

<span data-ttu-id="18d36-107">Controllers and Razor pages work with data that comes from HTTP requests.</span><span class="sxs-lookup"><span data-stu-id="18d36-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="18d36-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span><span class="sxs-lookup"><span data-stu-id="18d36-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="18d36-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span><span class="sxs-lookup"><span data-stu-id="18d36-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="18d36-110">Model binding automates this process.</span><span class="sxs-lookup"><span data-stu-id="18d36-110">Model binding automates this process.</span></span> <span data-ttu-id="18d36-111">The model binding system:</span><span class="sxs-lookup"><span data-stu-id="18d36-111">The model binding system:</span></span>

* <span data-ttu-id="18d36-112">Retrieves data from various sources such as route data, form fields, and query strings.</span><span class="sxs-lookup"><span data-stu-id="18d36-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="18d36-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span><span class="sxs-lookup"><span data-stu-id="18d36-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="18d36-114">Converts string data to .NET types.</span><span class="sxs-lookup"><span data-stu-id="18d36-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="18d36-115">Updates properties of complex types.</span><span class="sxs-lookup"><span data-stu-id="18d36-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="18d36-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="18d36-116">Example</span></span>

<span data-ttu-id="18d36-117">Suppose you have the following action method:</span><span class="sxs-lookup"><span data-stu-id="18d36-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="18d36-118">And the app receives a request with this URL:</span><span class="sxs-lookup"><span data-stu-id="18d36-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="18d36-119">Model binding goes through the following steps after the routing system selects the action method:</span><span class="sxs-lookup"><span data-stu-id="18d36-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="18d36-120">Finds the first parameter of `GetByID`, an integer named `id`.</span><span class="sxs-lookup"><span data-stu-id="18d36-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="18d36-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span><span class="sxs-lookup"><span data-stu-id="18d36-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="18d36-122">Converts the string "2" into integer 2.</span><span class="sxs-lookup"><span data-stu-id="18d36-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="18d36-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="18d36-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="18d36-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span><span class="sxs-lookup"><span data-stu-id="18d36-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="18d36-125">Name matching is not case-sensitive.</span><span class="sxs-lookup"><span data-stu-id="18d36-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="18d36-126">Converts the string "true" into boolean `true`.</span><span class="sxs-lookup"><span data-stu-id="18d36-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="18d36-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span><span class="sxs-lookup"><span data-stu-id="18d36-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="18d36-128">In the preceding example, the model binding targets are method parameters that are simple types.</span><span class="sxs-lookup"><span data-stu-id="18d36-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="18d36-129">Targets may also be the properties of a complex type.</span><span class="sxs-lookup"><span data-stu-id="18d36-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="18d36-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span><span class="sxs-lookup"><span data-stu-id="18d36-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="18d36-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="18d36-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="18d36-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span><span class="sxs-lookup"><span data-stu-id="18d36-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="18d36-133">Obiekty docelowe</span><span class="sxs-lookup"><span data-stu-id="18d36-133">Targets</span></span>

<span data-ttu-id="18d36-134">Model binding tries to find values for the following kinds of targets:</span><span class="sxs-lookup"><span data-stu-id="18d36-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="18d36-135">Parameters of the controller action method that a request is routed to.</span><span class="sxs-lookup"><span data-stu-id="18d36-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="18d36-136">Parameters of the Razor Pages handler method that a request is routed to.</span><span class="sxs-lookup"><span data-stu-id="18d36-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="18d36-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span><span class="sxs-lookup"><span data-stu-id="18d36-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="18d36-138">[BindProperty] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-138">[BindProperty] attribute</span></span>

<span data-ttu-id="18d36-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span><span class="sxs-lookup"><span data-stu-id="18d36-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="18d36-140">[BindProperties] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-140">[BindProperties] attribute</span></span>

<span data-ttu-id="18d36-141">Available in ASP.NET Core 2.1 and later.</span><span class="sxs-lookup"><span data-stu-id="18d36-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="18d36-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span><span class="sxs-lookup"><span data-stu-id="18d36-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="18d36-143">Model binding for HTTP GET requests</span><span class="sxs-lookup"><span data-stu-id="18d36-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="18d36-144">By default, properties are not bound for HTTP GET requests.</span><span class="sxs-lookup"><span data-stu-id="18d36-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="18d36-145">Typically, all you need for a GET request is a record ID parameter.</span><span class="sxs-lookup"><span data-stu-id="18d36-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="18d36-146">The record ID is used to look up the item in the database.</span><span class="sxs-lookup"><span data-stu-id="18d36-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="18d36-147">Therefore, there is no need to bind a property that holds an instance of the model.</span><span class="sxs-lookup"><span data-stu-id="18d36-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="18d36-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span><span class="sxs-lookup"><span data-stu-id="18d36-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="18d36-149">Sources</span><span class="sxs-lookup"><span data-stu-id="18d36-149">Sources</span></span>

<span data-ttu-id="18d36-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span><span class="sxs-lookup"><span data-stu-id="18d36-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="18d36-151">Form fields</span><span class="sxs-lookup"><span data-stu-id="18d36-151">Form fields</span></span>
1. <span data-ttu-id="18d36-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="18d36-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="18d36-153">Route data</span><span class="sxs-lookup"><span data-stu-id="18d36-153">Route data</span></span>
1. <span data-ttu-id="18d36-154">Query string parameters</span><span class="sxs-lookup"><span data-stu-id="18d36-154">Query string parameters</span></span>
1. <span data-ttu-id="18d36-155">Uploaded files</span><span class="sxs-lookup"><span data-stu-id="18d36-155">Uploaded files</span></span>

<span data-ttu-id="18d36-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span><span class="sxs-lookup"><span data-stu-id="18d36-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="18d36-157">There are a few exceptions:</span><span class="sxs-lookup"><span data-stu-id="18d36-157">There are a few exceptions:</span></span>

* <span data-ttu-id="18d36-158">Route data and query string values are used only for simple types.</span><span class="sxs-lookup"><span data-stu-id="18d36-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="18d36-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="18d36-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="18d36-160">If the default source is not correct, use one of the following attributes to specify the source:</span><span class="sxs-lookup"><span data-stu-id="18d36-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="18d36-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span><span class="sxs-lookup"><span data-stu-id="18d36-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="18d36-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span><span class="sxs-lookup"><span data-stu-id="18d36-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="18d36-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span><span class="sxs-lookup"><span data-stu-id="18d36-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="18d36-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span><span class="sxs-lookup"><span data-stu-id="18d36-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="18d36-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span><span class="sxs-lookup"><span data-stu-id="18d36-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="18d36-166">These attributes:</span><span class="sxs-lookup"><span data-stu-id="18d36-166">These attributes:</span></span>

* <span data-ttu-id="18d36-167">Are added to model properties individually (not to the model class), as in the following example:</span><span class="sxs-lookup"><span data-stu-id="18d36-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="18d36-168">Optionally accept a model name value in the constructor.</span><span class="sxs-lookup"><span data-stu-id="18d36-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="18d36-169">This option is provided in case the property name doesn't match the value in the request.</span><span class="sxs-lookup"><span data-stu-id="18d36-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="18d36-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span><span class="sxs-lookup"><span data-stu-id="18d36-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="18d36-171">[FromBody] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-171">[FromBody] attribute</span></span>

<span data-ttu-id="18d36-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span><span class="sxs-lookup"><span data-stu-id="18d36-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="18d36-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span><span class="sxs-lookup"><span data-stu-id="18d36-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="18d36-174">Input formatters are explained [later in this article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="18d36-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="18d36-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span><span class="sxs-lookup"><span data-stu-id="18d36-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="18d36-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span><span class="sxs-lookup"><span data-stu-id="18d36-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="18d36-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span><span class="sxs-lookup"><span data-stu-id="18d36-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="18d36-178">In the preceding example:</span><span class="sxs-lookup"><span data-stu-id="18d36-178">In the preceding example:</span></span>

* <span data-ttu-id="18d36-179">The `[FromQuery]` attribute is ignored.</span><span class="sxs-lookup"><span data-stu-id="18d36-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="18d36-180">The `Breed` property is not populated from a query string parameter.</span><span class="sxs-lookup"><span data-stu-id="18d36-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="18d36-181">Input formatters read only the body and don't understand binding source attributes.</span><span class="sxs-lookup"><span data-stu-id="18d36-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="18d36-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span><span class="sxs-lookup"><span data-stu-id="18d36-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="18d36-183">Don't apply `[FromBody]` to more than one parameter per action method.</span><span class="sxs-lookup"><span data-stu-id="18d36-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="18d36-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span><span class="sxs-lookup"><span data-stu-id="18d36-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="18d36-185">Additional sources</span><span class="sxs-lookup"><span data-stu-id="18d36-185">Additional sources</span></span>

<span data-ttu-id="18d36-186">Source data is provided to the model binding system by *value providers*.</span><span class="sxs-lookup"><span data-stu-id="18d36-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="18d36-187">You can write and register custom value providers that get data for model binding from other sources.</span><span class="sxs-lookup"><span data-stu-id="18d36-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="18d36-188">For example, you might want data from cookies or session state.</span><span class="sxs-lookup"><span data-stu-id="18d36-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="18d36-189">To get data from a new source:</span><span class="sxs-lookup"><span data-stu-id="18d36-189">To get data from a new source:</span></span>

* <span data-ttu-id="18d36-190">Create a class that implements `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="18d36-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="18d36-191">Create a class that implements `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="18d36-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="18d36-192">Register the factory class in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18d36-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="18d36-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span><span class="sxs-lookup"><span data-stu-id="18d36-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="18d36-194">Here's the registration code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18d36-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="18d36-195">The code shown puts the custom value provider after all the built-in value providers.</span><span class="sxs-lookup"><span data-stu-id="18d36-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="18d36-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span><span class="sxs-lookup"><span data-stu-id="18d36-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="18d36-197">No source for a model property</span><span class="sxs-lookup"><span data-stu-id="18d36-197">No source for a model property</span></span>

<span data-ttu-id="18d36-198">By default, a model state error isn't created if no value is found for a model property.</span><span class="sxs-lookup"><span data-stu-id="18d36-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="18d36-199">The property is set to null or a default value:</span><span class="sxs-lookup"><span data-stu-id="18d36-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="18d36-200">Nullable simple types are set to `null`.</span><span class="sxs-lookup"><span data-stu-id="18d36-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="18d36-201">Non-nullable value types are set to `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="18d36-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="18d36-202">For example, a parameter `int id` is set to 0.</span><span class="sxs-lookup"><span data-stu-id="18d36-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="18d36-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span><span class="sxs-lookup"><span data-stu-id="18d36-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="18d36-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span><span class="sxs-lookup"><span data-stu-id="18d36-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="18d36-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span><span class="sxs-lookup"><span data-stu-id="18d36-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="18d36-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span><span class="sxs-lookup"><span data-stu-id="18d36-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="18d36-207">Request body data is handled by [input formatters](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="18d36-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="18d36-208">Type conversion errors</span><span class="sxs-lookup"><span data-stu-id="18d36-208">Type conversion errors</span></span>

<span data-ttu-id="18d36-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span><span class="sxs-lookup"><span data-stu-id="18d36-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="18d36-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span><span class="sxs-lookup"><span data-stu-id="18d36-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="18d36-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span><span class="sxs-lookup"><span data-stu-id="18d36-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="18d36-212">In a Razor page, redisplay the page with an error message:</span><span class="sxs-lookup"><span data-stu-id="18d36-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="18d36-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span><span class="sxs-lookup"><span data-stu-id="18d36-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="18d36-214">This validation makes it hard to trigger the preceding highlighted code.</span><span class="sxs-lookup"><span data-stu-id="18d36-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="18d36-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span><span class="sxs-lookup"><span data-stu-id="18d36-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="18d36-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span><span class="sxs-lookup"><span data-stu-id="18d36-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="18d36-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span><span class="sxs-lookup"><span data-stu-id="18d36-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="18d36-218">This is because the model property has been set to null or a default value.</span><span class="sxs-lookup"><span data-stu-id="18d36-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="18d36-219">The invalid input does appear in an error message.</span><span class="sxs-lookup"><span data-stu-id="18d36-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="18d36-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span><span class="sxs-lookup"><span data-stu-id="18d36-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="18d36-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span><span class="sxs-lookup"><span data-stu-id="18d36-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="18d36-222">In that case, make the model property a string.</span><span class="sxs-lookup"><span data-stu-id="18d36-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="18d36-223">Simple types</span><span class="sxs-lookup"><span data-stu-id="18d36-223">Simple types</span></span>

<span data-ttu-id="18d36-224">The simple types that the model binder can convert source strings into include the following:</span><span class="sxs-lookup"><span data-stu-id="18d36-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="18d36-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="18d36-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="18d36-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="18d36-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="18d36-227">Char</span><span class="sxs-lookup"><span data-stu-id="18d36-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="18d36-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="18d36-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="18d36-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="18d36-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="18d36-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="18d36-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="18d36-231">Double</span><span class="sxs-lookup"><span data-stu-id="18d36-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="18d36-232">Enum</span><span class="sxs-lookup"><span data-stu-id="18d36-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="18d36-233">Guid</span><span class="sxs-lookup"><span data-stu-id="18d36-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="18d36-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="18d36-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="18d36-235">Single</span><span class="sxs-lookup"><span data-stu-id="18d36-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="18d36-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="18d36-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="18d36-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="18d36-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="18d36-238">Uri</span><span class="sxs-lookup"><span data-stu-id="18d36-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="18d36-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="18d36-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="18d36-240">Complex types</span><span class="sxs-lookup"><span data-stu-id="18d36-240">Complex types</span></span>

<span data-ttu-id="18d36-241">A complex type must have a public default constructor and public writable properties to bind.</span><span class="sxs-lookup"><span data-stu-id="18d36-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="18d36-242">When model binding occurs, the class is instantiated using the public default constructor.</span><span class="sxs-lookup"><span data-stu-id="18d36-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="18d36-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="18d36-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="18d36-244">If nothing is found, it looks for just *property_name* without the prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="18d36-245">For binding to a parameter, the prefix is the parameter name.</span><span class="sxs-lookup"><span data-stu-id="18d36-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="18d36-246">For binding to a `PageModel` public property, the prefix is the public property name.</span><span class="sxs-lookup"><span data-stu-id="18d36-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="18d36-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span><span class="sxs-lookup"><span data-stu-id="18d36-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="18d36-248">For example, suppose the complex type is the following `Instructor` class:</span><span class="sxs-lookup"><span data-stu-id="18d36-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="18d36-249">Prefix = parameter name</span><span class="sxs-lookup"><span data-stu-id="18d36-249">Prefix = parameter name</span></span>

<span data-ttu-id="18d36-250">If the model to be bound is a parameter named `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="18d36-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="18d36-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="18d36-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="18d36-252">If that isn't found, it looks for `ID` without a prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="18d36-253">Prefix = property name</span><span class="sxs-lookup"><span data-stu-id="18d36-253">Prefix = property name</span></span>

<span data-ttu-id="18d36-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span><span class="sxs-lookup"><span data-stu-id="18d36-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="18d36-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="18d36-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="18d36-256">If that isn't found, it looks for `ID` without a prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="18d36-257">Custom prefix</span><span class="sxs-lookup"><span data-stu-id="18d36-257">Custom prefix</span></span>

<span data-ttu-id="18d36-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span><span class="sxs-lookup"><span data-stu-id="18d36-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="18d36-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="18d36-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="18d36-260">If that isn't found, it looks for `ID` without a prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="18d36-261">Attributes for complex type targets</span><span class="sxs-lookup"><span data-stu-id="18d36-261">Attributes for complex type targets</span></span>

<span data-ttu-id="18d36-262">Several built-in attributes are available for controlling model binding of complex types:</span><span class="sxs-lookup"><span data-stu-id="18d36-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="18d36-263">These attributes affect model binding when posted form data is the source of values.</span><span class="sxs-lookup"><span data-stu-id="18d36-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="18d36-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span><span class="sxs-lookup"><span data-stu-id="18d36-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="18d36-265">Input formatters are explained [later in this article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="18d36-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="18d36-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="18d36-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="18d36-267">[BindRequired] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-267">[BindRequired] attribute</span></span>

<span data-ttu-id="18d36-268">Can only be applied to model properties, not to method parameters.</span><span class="sxs-lookup"><span data-stu-id="18d36-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="18d36-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span><span class="sxs-lookup"><span data-stu-id="18d36-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="18d36-270">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="18d36-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="18d36-271">[BindNever] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-271">[BindNever] attribute</span></span>

<span data-ttu-id="18d36-272">Can only be applied to model properties, not to method parameters.</span><span class="sxs-lookup"><span data-stu-id="18d36-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="18d36-273">Prevents model binding from setting a model's property.</span><span class="sxs-lookup"><span data-stu-id="18d36-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="18d36-274">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="18d36-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="18d36-275">[Bind] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-275">[Bind] attribute</span></span>

<span data-ttu-id="18d36-276">Can be applied to a class or a method parameter.</span><span class="sxs-lookup"><span data-stu-id="18d36-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="18d36-277">Specifies which properties of a model should be included in model binding.</span><span class="sxs-lookup"><span data-stu-id="18d36-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="18d36-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span><span class="sxs-lookup"><span data-stu-id="18d36-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="18d36-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span><span class="sxs-lookup"><span data-stu-id="18d36-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="18d36-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span><span class="sxs-lookup"><span data-stu-id="18d36-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="18d36-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span><span class="sxs-lookup"><span data-stu-id="18d36-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="18d36-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span><span class="sxs-lookup"><span data-stu-id="18d36-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="18d36-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="18d36-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="18d36-284">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="18d36-284">Collections</span></span>

<span data-ttu-id="18d36-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span><span class="sxs-lookup"><span data-stu-id="18d36-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="18d36-286">If no match is found, it looks for one of the supported formats without the prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="18d36-287">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="18d36-287">For example:</span></span>

* <span data-ttu-id="18d36-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="18d36-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="18d36-289">Form or query string data can be in one of the following formats:</span><span class="sxs-lookup"><span data-stu-id="18d36-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="18d36-290">The following format is supported only in form data:</span><span class="sxs-lookup"><span data-stu-id="18d36-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="18d36-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span><span class="sxs-lookup"><span data-stu-id="18d36-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="18d36-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="18d36-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="18d36-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="18d36-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="18d36-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span><span class="sxs-lookup"><span data-stu-id="18d36-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="18d36-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span><span class="sxs-lookup"><span data-stu-id="18d36-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="18d36-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span><span class="sxs-lookup"><span data-stu-id="18d36-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="18d36-297">Dictionaries</span><span class="sxs-lookup"><span data-stu-id="18d36-297">Dictionaries</span></span>

<span data-ttu-id="18d36-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span><span class="sxs-lookup"><span data-stu-id="18d36-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="18d36-299">If no match is found, it looks for one of the supported formats without the prefix.</span><span class="sxs-lookup"><span data-stu-id="18d36-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="18d36-300">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="18d36-300">For example:</span></span>

* <span data-ttu-id="18d36-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="18d36-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="18d36-302">The posted form or query string data can look like one of the following examples:</span><span class="sxs-lookup"><span data-stu-id="18d36-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="18d36-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span><span class="sxs-lookup"><span data-stu-id="18d36-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="18d36-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="18d36-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="18d36-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="18d36-305">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="18d36-306">Special data types</span><span class="sxs-lookup"><span data-stu-id="18d36-306">Special data types</span></span>

<span data-ttu-id="18d36-307">There are some special data types that model binding can handle.</span><span class="sxs-lookup"><span data-stu-id="18d36-307">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="18d36-308">IFormFile and IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="18d36-308">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="18d36-309">An uploaded file included in the HTTP request.</span><span class="sxs-lookup"><span data-stu-id="18d36-309">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="18d36-310">Also supported is `IEnumerable<IFormFile>` for multiple files.</span><span class="sxs-lookup"><span data-stu-id="18d36-310">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="18d36-311">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="18d36-311">CancellationToken</span></span>

<span data-ttu-id="18d36-312">Used to cancel activity in asynchronous controllers.</span><span class="sxs-lookup"><span data-stu-id="18d36-312">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="18d36-313">FormCollection</span><span class="sxs-lookup"><span data-stu-id="18d36-313">FormCollection</span></span>

<span data-ttu-id="18d36-314">Used to retrieve all the values from posted form data.</span><span class="sxs-lookup"><span data-stu-id="18d36-314">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="18d36-315">Input formatters</span><span class="sxs-lookup"><span data-stu-id="18d36-315">Input formatters</span></span>

<span data-ttu-id="18d36-316">Data in the request body can be in JSON, XML, or some other format.</span><span class="sxs-lookup"><span data-stu-id="18d36-316">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="18d36-317">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span><span class="sxs-lookup"><span data-stu-id="18d36-317">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="18d36-318">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span><span class="sxs-lookup"><span data-stu-id="18d36-318">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="18d36-319">You can add other formatters for other content types.</span><span class="sxs-lookup"><span data-stu-id="18d36-319">You can add other formatters for other content types.</span></span>

<span data-ttu-id="18d36-320">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="18d36-320">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="18d36-321">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="18d36-321">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="18d36-322">To use the built-in XML input formatters:</span><span class="sxs-lookup"><span data-stu-id="18d36-322">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="18d36-323">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span><span class="sxs-lookup"><span data-stu-id="18d36-323">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="18d36-324">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="18d36-324">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="18d36-325">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span><span class="sxs-lookup"><span data-stu-id="18d36-325">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="18d36-326">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="18d36-326">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="18d36-327">Exclude specified types from model binding</span><span class="sxs-lookup"><span data-stu-id="18d36-327">Exclude specified types from model binding</span></span>

<span data-ttu-id="18d36-328">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="18d36-328">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="18d36-329">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="18d36-329">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="18d36-330">Built-in details providers are available for disabling model binding or validation for specified types.</span><span class="sxs-lookup"><span data-stu-id="18d36-330">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="18d36-331">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18d36-331">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18d36-332">For example, to disable model binding on all models of type `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="18d36-332">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="18d36-333">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18d36-333">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18d36-334">For example, to disable validation on properties of type `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="18d36-334">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="18d36-335">Custom model binders</span><span class="sxs-lookup"><span data-stu-id="18d36-335">Custom model binders</span></span>

<span data-ttu-id="18d36-336">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span><span class="sxs-lookup"><span data-stu-id="18d36-336">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="18d36-337">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="18d36-337">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="18d36-338">Manual model binding</span><span class="sxs-lookup"><span data-stu-id="18d36-338">Manual model binding</span></span>

<span data-ttu-id="18d36-339">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span><span class="sxs-lookup"><span data-stu-id="18d36-339">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="18d36-340">The method is defined on both `ControllerBase` and `PageModel` classes.</span><span class="sxs-lookup"><span data-stu-id="18d36-340">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="18d36-341">Method overloads let you specify the prefix and value provider to use.</span><span class="sxs-lookup"><span data-stu-id="18d36-341">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="18d36-342">The method returns `false` if model binding fails.</span><span class="sxs-lookup"><span data-stu-id="18d36-342">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="18d36-343">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="18d36-343">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="18d36-344">[FromServices] attribute</span><span class="sxs-lookup"><span data-stu-id="18d36-344">[FromServices] attribute</span></span>

<span data-ttu-id="18d36-345">This attribute's name follows the pattern of model binding attributes that specify a data source.</span><span class="sxs-lookup"><span data-stu-id="18d36-345">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="18d36-346">But it's not about binding data from a value provider.</span><span class="sxs-lookup"><span data-stu-id="18d36-346">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="18d36-347">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span><span class="sxs-lookup"><span data-stu-id="18d36-347">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="18d36-348">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span><span class="sxs-lookup"><span data-stu-id="18d36-348">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18d36-349">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="18d36-349">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
