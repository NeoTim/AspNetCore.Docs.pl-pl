---
title: Formatowanie danych odpowiedzi w ASP.NET Core Web API
author: ardalis
description: Dowiedz się, jak sformatować dane odpowiedzi w ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 78fe620ea8fdd681a276253f77939bcb2a56ebb9
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391289"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="cf925-103">Formatowanie danych odpowiedzi w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="cf925-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="cf925-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cf925-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cf925-105">ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cf925-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="cf925-106">Dane odpowiedzi można sformatować przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.</span><span class="sxs-lookup"><span data-stu-id="cf925-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="cf925-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf925-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="cf925-108">Wyniki akcji specyficzne dla formatu</span><span class="sxs-lookup"><span data-stu-id="cf925-108">Format-specific Action Results</span></span>

<span data-ttu-id="cf925-109">Niektóre typy wyników akcji są specyficzne dla określonego formatu, takiego jak <xref:Microsoft.AspNetCore.Mvc.JsonResult> i <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="cf925-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="cf925-110">Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta.</span><span class="sxs-lookup"><span data-stu-id="cf925-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="cf925-111">Na przykład zwrócenie `JsonResult` zwraca dane w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="cf925-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="cf925-112">Zwrócenie `ContentResult` lub ciągu zwraca dane ciągu w formacie zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="cf925-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="cf925-113">Akcja nie jest wymagana do zwrócenia żadnego określonego typu.</span><span class="sxs-lookup"><span data-stu-id="cf925-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="cf925-114">ASP.NET Core obsługuje dowolną wartość zwracaną przez obiekt.</span><span class="sxs-lookup"><span data-stu-id="cf925-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="cf925-115">Wyniki akcji, które zwracają obiekty, które nie są typu <xref:Microsoft.AspNetCore.Mvc.IActionResult> są serializowane przy użyciu odpowiedniej implementacji <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="cf925-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="cf925-116">Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="cf925-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="cf925-117">Wbudowana metoda pomocnika <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> zwraca dane sformatowane w formacie JSON: [!code-csharp @ no__t-2</span><span class="sxs-lookup"><span data-stu-id="cf925-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="cf925-118">Pobieranie próbek zwraca listę autorów.</span><span class="sxs-lookup"><span data-stu-id="cf925-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="cf925-119">Za pomocą narzędzi deweloperskich przeglądarki F12 lub [po](https://www.getpostman.com/tools) powyższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="cf925-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="cf925-120">Zostanie wyświetlony nagłówek odpowiedzi zawierający **Typ zawartości:** `application/json; charset=utf-8`.</span><span class="sxs-lookup"><span data-stu-id="cf925-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="cf925-121">Wyświetlane są nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="cf925-121">The request headers are displayed.</span></span> <span data-ttu-id="cf925-122">Na przykład nagłówek `Accept`.</span><span class="sxs-lookup"><span data-stu-id="cf925-122">For example, the `Accept` header.</span></span> <span data-ttu-id="cf925-123">Nagłówek `Accept` jest ignorowany przez poprzedni kod.</span><span class="sxs-lookup"><span data-stu-id="cf925-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="cf925-124">Aby zwrócić dane sformatowane przy użyciu zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> i pomocnika <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="cf925-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="cf925-125">W poprzednim kodzie zwracana wartość `Content-Type` jest `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="cf925-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="cf925-126">Zwracanie ciągu zapewnia `Content-Type` z `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="cf925-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="cf925-127">W przypadku akcji z wieloma zwracanymi typami zwraca `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="cf925-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="cf925-128">Na przykład zwrócenie różnych kodów stanu HTTP w oparciu o wynik wykonanych operacji.</span><span class="sxs-lookup"><span data-stu-id="cf925-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="cf925-129">Negocjowanie zawartości</span><span class="sxs-lookup"><span data-stu-id="cf925-129">Content negotiation</span></span>

<span data-ttu-id="cf925-130">Negocjowanie zawartości odbywa się, gdy klient określi [nagłówek Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="cf925-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="cf925-131">Domyślny format używany przez ASP.NET Core to [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="cf925-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="cf925-132">Negocjowanie zawartości:</span><span class="sxs-lookup"><span data-stu-id="cf925-132">Content negotiation is:</span></span>

* <span data-ttu-id="cf925-133">Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="cf925-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="cf925-134">Wbudowane wyniki akcji specyficzne dla kodu stanu zwracane z metod pomocnika.</span><span class="sxs-lookup"><span data-stu-id="cf925-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="cf925-135">Metody pomocnika wyników akcji są oparte na `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="cf925-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="cf925-136">Po zwróceniu typu modelu typem zwracanym jest `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="cf925-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="cf925-137">Poniższa metoda działania używa metod pomocnika `Ok` i `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="cf925-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="cf925-138">Domyślnie ASP.NET Core obsługuje typy nośników `application/json`, `text/json` i `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="cf925-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="cf925-139">Narzędzia takie jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/tools) mogą ustawić nagłówek żądania `Accept` w celu określenia formatu powrotu.</span><span class="sxs-lookup"><span data-stu-id="cf925-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="cf925-140">Gdy nagłówek `Accept` zawiera typ obsługiwany przez serwer, zwracany jest ten typ.</span><span class="sxs-lookup"><span data-stu-id="cf925-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="cf925-141">W następnej sekcji pokazano, jak dodać dodatkowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="cf925-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="cf925-142">Akcje kontrolera mogą zwracać POCOs (zwykłe stare obiekty CLR).</span><span class="sxs-lookup"><span data-stu-id="cf925-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="cf925-143">Gdy POCO jest zwracany, środowisko uruchomieniowe automatycznie tworzy `ObjectResult`, które zawija obiekt.</span><span class="sxs-lookup"><span data-stu-id="cf925-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="cf925-144">Klient pobiera sformatowany obiekt Zserializowany.</span><span class="sxs-lookup"><span data-stu-id="cf925-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="cf925-145">Jeśli zwracany obiekt jest `null`, zwracana jest odpowiedź `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="cf925-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="cf925-146">Zwracanie typu obiektu:</span><span class="sxs-lookup"><span data-stu-id="cf925-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="cf925-147">W poprzednim kodzie żądanie poprawnego aliasu autora zwraca odpowiedź `200 OK` z danymi autora.</span><span class="sxs-lookup"><span data-stu-id="cf925-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="cf925-148">Żądanie dotyczące nieprawidłowego aliasu zwraca odpowiedź `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="cf925-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="cf925-149">Nagłówek Accept</span><span class="sxs-lookup"><span data-stu-id="cf925-149">The Accept header</span></span>

<span data-ttu-id="cf925-150">*Negocjowanie* zawartości odbywa się, gdy w żądaniu pojawi się nagłówek `Accept`.</span><span class="sxs-lookup"><span data-stu-id="cf925-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="cf925-151">Gdy żądanie zawiera nagłówek Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cf925-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="cf925-152">Wylicza typy nośników w nagłówku Accept w kolejności preferencji.</span><span class="sxs-lookup"><span data-stu-id="cf925-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="cf925-153">Próbuje znaleźć program formatujący, który może wygenerować odpowiedź w jednym z określonych formatów.</span><span class="sxs-lookup"><span data-stu-id="cf925-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="cf925-154">Jeśli nie zostanie znaleziony żaden program formatujący, który może spełnić żądanie klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cf925-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="cf925-155">Zwraca wartość `406 Not Acceptable`, jeśli ustawiono <xref:Microsoft.AspNetCore.Mvc.MvcOptions>, lub-</span><span class="sxs-lookup"><span data-stu-id="cf925-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="cf925-156">Próbuje znaleźć pierwszy program formatujący, który może wygenerować odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="cf925-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="cf925-157">Jeśli nie skonfigurowano programu formatującego dla żądanego formatu, jest używany pierwszy program formatujący, który może formatować obiekt.</span><span class="sxs-lookup"><span data-stu-id="cf925-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="cf925-158">Jeśli w żądaniu nie pojawi się nagłówek `Accept`:</span><span class="sxs-lookup"><span data-stu-id="cf925-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="cf925-159">Pierwszy program formatujący, który może obsłużyć obiekt, jest używany do serializacji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cf925-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="cf925-160">Nie ma żadnej negocjacji.</span><span class="sxs-lookup"><span data-stu-id="cf925-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="cf925-161">Serwer określa format do zwrócenia.</span><span class="sxs-lookup"><span data-stu-id="cf925-161">The server is determining what format to return.</span></span>

<span data-ttu-id="cf925-162">Jeśli nagłówek Accept zawiera `*/*`, nagłówek jest ignorowany, chyba że `RespectBrowserAcceptHeader` ma wartość true w <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="cf925-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="cf925-163">Przeglądarki i negocjacje zawartości</span><span class="sxs-lookup"><span data-stu-id="cf925-163">Browsers and content negotiation</span></span>

<span data-ttu-id="cf925-164">W przeciwieństwie do typowych klientów interfejsu API, przeglądarki sieci Web dostarczają nagłówki `Accept`.</span><span class="sxs-lookup"><span data-stu-id="cf925-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="cf925-165">Przeglądarka sieci Web określa wiele formatów, w tym symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="cf925-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="cf925-166">Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="cf925-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="cf925-167">Nagłówek `Accept` jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="cf925-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="cf925-168">Zawartość jest zwracana w formacie JSON, o ile nie została skonfigurowana inaczej.</span><span class="sxs-lookup"><span data-stu-id="cf925-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="cf925-169">Zapewnia to bardziej spójne środowisko w przeglądarkach podczas używania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="cf925-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="cf925-170">Aby skonfigurować aplikację do honorowania nagłówków akceptowanych przez przeglądarkę, ustaw wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true`:</span><span class="sxs-lookup"><span data-stu-id="cf925-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="cf925-171">Konfigurowanie elementów formatujących</span><span class="sxs-lookup"><span data-stu-id="cf925-171">Configure formatters</span></span>

<span data-ttu-id="cf925-172">Aplikacje, które muszą obsługiwać dodatkowe formaty, mogą dodać odpowiednie pakiety NuGet i skonfigurować obsługę.</span><span class="sxs-lookup"><span data-stu-id="cf925-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="cf925-173">Istnieją osobne elementy formatujące dla danych wejściowych i wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="cf925-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="cf925-174">Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="cf925-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="cf925-175">Wyjściowe elementy formatujące są używane do formatowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cf925-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="cf925-176">Aby uzyskać informacje na temat tworzenia niestandardowego programu formatującego, zobacz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="cf925-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="cf925-177">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="cf925-177">Add XML format support</span></span>

<span data-ttu-id="cf925-178">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="cf925-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="cf925-179">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="cf925-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="cf925-180">W przypadku korzystania z powyższego kodu metody kontrolera powinny zwrócić odpowiedni format na podstawie nagłówka `Accept` żądania.</span><span class="sxs-lookup"><span data-stu-id="cf925-180">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="cf925-181">Skonfiguruj elementy formatujące system. Text. JSON w oparciu o</span><span class="sxs-lookup"><span data-stu-id="cf925-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="cf925-182">Funkcje dla @no__t programu formatującego oparte na -0 można skonfigurować przy użyciu `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="cf925-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="cf925-183">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="cf925-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="cf925-184">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf925-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="cf925-185">Dodawanie obsługi formatu JSON opartego na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="cf925-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="cf925-186">Przed ASP.NET Core 3,0, domyślne używane elementy formatujące JSON zaimplementowane przy użyciu pakietu `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="cf925-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="cf925-187">W ASP.NET Core 3,0 lub nowszych domyślne elementy formatujące JSON są oparte na `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="cf925-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="cf925-188">Obsługa programów formatującego i funkcji opartych na `Newtonsoft.Json` jest dostępna przez zainstalowanie pakietu NuGet [Microsoft. AspNetCore. MVC. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) i skonfigurowanie go w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cf925-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="cf925-189">Niektóre funkcje mogą nie współpracować z @no__tami programu formatującego opartymi na -0 i wymagają odwołania do programu formatującego @no__t opartego na -1.</span><span class="sxs-lookup"><span data-stu-id="cf925-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="cf925-190">Kontynuuj korzystanie z @no__t programu formatującego opartych na -0, jeśli aplikacja:</span><span class="sxs-lookup"><span data-stu-id="cf925-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="cf925-191">Używa atrybutów `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="cf925-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="cf925-192">Na przykład `[JsonProperty]` lub `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="cf925-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="cf925-193">Dostosowuje ustawienia serializacji.</span><span class="sxs-lookup"><span data-stu-id="cf925-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="cf925-194">Opiera się na funkcjach, które są dostępne `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="cf925-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="cf925-195">Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="cf925-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="cf925-196">Przed ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje wystąpienie `JsonSerializerSettings`, które jest specyficzne dla `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="cf925-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="cf925-197">Generuje dokumentację [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="cf925-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="cf925-198">Funkcje dla @no__t programu formatującego oparte na -0 można skonfigurować przy użyciu `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="cf925-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="cf925-199">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="cf925-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="cf925-200">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf925-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="cf925-201">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="cf925-201">Add XML format support</span></span>

<span data-ttu-id="cf925-202">Formatowanie XML wymaga pakietu NuGet [Microsoft. AspNetCore. MVC. formatującegos. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="cf925-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="cf925-203">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="cf925-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="cf925-204">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="cf925-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="cf925-205">W przypadku korzystania z powyższego kodu metody kontrolera powinny zwrócić odpowiedni format na podstawie nagłówka `Accept` żądania.</span><span class="sxs-lookup"><span data-stu-id="cf925-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="cf925-206">Określ format</span><span class="sxs-lookup"><span data-stu-id="cf925-206">Specify a format</span></span>

<span data-ttu-id="cf925-207">Aby ograniczyć formaty odpowiedzi, Zastosuj filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="cf925-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="cf925-208">Podobnie jak większość [filtrów](xref:mvc/controllers/filters), `[Produces]` można zastosować do akcji, kontrolera lub zakresu globalnego:</span><span class="sxs-lookup"><span data-stu-id="cf925-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="cf925-209">Poprzedni filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cf925-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="cf925-210">Wymusza, aby wszystkie akcje w ramach kontrolera zwracały odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="cf925-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="cf925-211">Jeśli inne elementy formatujące są skonfigurowane, a klient określi inny format, zwracany jest kod JSON.</span><span class="sxs-lookup"><span data-stu-id="cf925-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="cf925-212">Aby uzyskać więcej informacji, zobacz [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="cf925-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="cf925-213">Specjalne elementy formatujące Case</span><span class="sxs-lookup"><span data-stu-id="cf925-213">Special case formatters</span></span>

<span data-ttu-id="cf925-214">Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych elementów formatujących.</span><span class="sxs-lookup"><span data-stu-id="cf925-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="cf925-215">Domyślnie typy zwracane `string` są formatowane jako *tekst/zwykły* (*tekst/HTML* , jeśli żąda się za pośrednictwem nagłówka `Accept`).</span><span class="sxs-lookup"><span data-stu-id="cf925-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="cf925-216">To zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="cf925-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="cf925-217">Elementy formatujące są usuwane w metodzie `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cf925-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="cf925-218">Akcje z typem zwracanym obiektu modelu zwracają `204 No Content` podczas zwracania `null`.</span><span class="sxs-lookup"><span data-stu-id="cf925-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="cf925-219">To zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="cf925-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="cf925-220">Poniższy kod usuwa `StringOutputFormatter` i `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="cf925-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="cf925-221">Bez `StringOutputFormatter` wbudowane typy programu formatującego JSON `string` zwracanych typów.</span><span class="sxs-lookup"><span data-stu-id="cf925-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="cf925-222">Jeśli wbudowany program formatujący JSON jest usuwany, a element formatujący XML jest dostępny, format XML programu formatującego jest formatowany `string` typów zwracanych.</span><span class="sxs-lookup"><span data-stu-id="cf925-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="cf925-223">W przeciwnym razie zwracane typy `string` zwracają `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="cf925-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="cf925-224">Bez `HttpNoContentOutputFormatter` obiekty o wartości null są formatowane przy użyciu skonfigurowanego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="cf925-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="cf925-225">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf925-225">For example:</span></span>

* <span data-ttu-id="cf925-226">Program formatujący JSON zwraca odpowiedź z treścią `null`.</span><span class="sxs-lookup"><span data-stu-id="cf925-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="cf925-227">Program formatujący XML zwraca pusty element XML z atrybutem `xsi:nil="true"` Set.</span><span class="sxs-lookup"><span data-stu-id="cf925-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="cf925-228">Mapowania adresów URL w formacie odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="cf925-228">Response format URL mappings</span></span>

<span data-ttu-id="cf925-229">Klienci mogą zażądać określonego formatu w ramach adresu URL, na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf925-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="cf925-230">W ciągu zapytania lub części ścieżki.</span><span class="sxs-lookup"><span data-stu-id="cf925-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="cf925-231">Przy użyciu rozszerzenia pliku specyficznego dla formatu, takiego jak. XML lub. JSON.</span><span class="sxs-lookup"><span data-stu-id="cf925-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="cf925-232">Mapowanie ze ścieżki żądania należy określić w marszrucie używanej przez interfejs API.</span><span class="sxs-lookup"><span data-stu-id="cf925-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="cf925-233">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf925-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="cf925-234">Poprzednia trasa pozwala określić żądany format jako opcjonalne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="cf925-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="cf925-235">Atrybut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) sprawdza obecność wartości format w `RouteData` i mapuje format odpowiedzi do odpowiedniego programu formatującego podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cf925-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="cf925-236">Szlak</span><span class="sxs-lookup"><span data-stu-id="cf925-236">Route</span></span>        |             <span data-ttu-id="cf925-237">EQ</span><span class="sxs-lookup"><span data-stu-id="cf925-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="cf925-238">Domyślny program formatujący dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="cf925-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="cf925-239">Program formatujący JSON (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="cf925-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="cf925-240">Program formatujący XML (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="cf925-240">The XML formatter (if configured)</span></span>  |
