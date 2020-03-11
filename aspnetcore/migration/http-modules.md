---
title: Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659685"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="7250a-102">Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="7250a-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="7250a-103">W tym artykule przedstawiono sposób migrowania istniejących [modułów i programów ASP.net http z programu System. WebServer](/iis/configuration/system.webserver/) do ASP.NET Core [oprogramowania pośredniczącego](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="7250a-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="7250a-104">Moduły i programy obsługi oglądany</span><span class="sxs-lookup"><span data-stu-id="7250a-104">Modules and handlers revisited</span></span>

<span data-ttu-id="7250a-105">Przed przystąpieniem do ASP.NET Core oprogramowania pośredniczącego najpierw podsumowanie sposób działania modułów i programów obsługi HTTP:</span><span class="sxs-lookup"><span data-stu-id="7250a-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Procedura obsługi modułów](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="7250a-107">**Programy obsługi:**</span><span class="sxs-lookup"><span data-stu-id="7250a-107">**Handlers are:**</span></span>

* <span data-ttu-id="7250a-108">Klasy implementujące [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="7250a-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="7250a-109">Służy do obsługi żądań o danej nazwie pliku lub rozszerzeniu, takich jak *. Report*</span><span class="sxs-lookup"><span data-stu-id="7250a-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="7250a-110">[Skonfigurowane](/iis/configuration/system.webserver/handlers/) w *pliku Web. config*</span><span class="sxs-lookup"><span data-stu-id="7250a-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="7250a-111">**Moduły to:**</span><span class="sxs-lookup"><span data-stu-id="7250a-111">**Modules are:**</span></span>

* <span data-ttu-id="7250a-112">Klasy implementujące [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="7250a-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="7250a-113">Wywołane dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="7250a-113">Invoked for every request</span></span>

* <span data-ttu-id="7250a-114">Możliwość krótkiego obwodu (zaprzestanie przetwarzania żądania)</span><span class="sxs-lookup"><span data-stu-id="7250a-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="7250a-115">Można dodać do odpowiedzi HTTP lub utworzyć własne</span><span class="sxs-lookup"><span data-stu-id="7250a-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="7250a-116">[Skonfigurowane](/iis/configuration/system.webserver/modules/) w *pliku Web. config*</span><span class="sxs-lookup"><span data-stu-id="7250a-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="7250a-117">**Kolejność, w której moduły przetwarzają żądania przychodzące, jest określana na podstawie:**</span><span class="sxs-lookup"><span data-stu-id="7250a-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="7250a-118">[Cykl życia aplikacji](https://msdn.microsoft.com/library/ms227673.aspx), czyli zdarzenia serii wywoływane przez ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)itd. Każdy moduł może utworzyć procedurę obsługi dla jednego lub wielu zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="7250a-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="7250a-119">Dla tego samego zdarzenia kolejność, w jakiej są skonfigurowane w *pliku Web. config*.</span><span class="sxs-lookup"><span data-stu-id="7250a-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="7250a-120">Oprócz modułów można dodać programy obsługi dla zdarzeń cyklu życia do pliku *Global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="7250a-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="7250a-121">Te programy obsługi są uruchamiane po programach obsługi w skonfigurowanych modułach.</span><span class="sxs-lookup"><span data-stu-id="7250a-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="7250a-122">Z programów obsługi i modułów do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="7250a-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="7250a-123">**Oprogramowanie pośredniczące jest prostsze niż moduły HTTP i programy obsługi:**</span><span class="sxs-lookup"><span data-stu-id="7250a-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="7250a-124">Moduły, programy obsługi, *Global.asax.cs*, *Web. config* (z wyjątkiem konfiguracji usług IIS) i cykl życia aplikacji zostały utracone</span><span class="sxs-lookup"><span data-stu-id="7250a-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="7250a-125">Role obu modułów i programów obsługi zostały przejęte przez oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="7250a-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="7250a-126">Oprogramowanie pośredniczące jest konfigurowane przy użyciu kodu, a nie *pliku Web. config*</span><span class="sxs-lookup"><span data-stu-id="7250a-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="7250a-127">[Rozgałęzianie potokowe](xref:fundamentals/middleware/index#use-run-and-map) umożliwia wysyłanie żądań do określonego oprogramowania pośredniczącego, w oparciu o nie tylko adres URL, ale również w nagłówkach żądań, ciągach zapytań itd.</span><span class="sxs-lookup"><span data-stu-id="7250a-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="7250a-128">**Oprogramowanie pośredniczące jest bardzo podobne do modułów:**</span><span class="sxs-lookup"><span data-stu-id="7250a-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="7250a-129">Wywoływane w zasadzie dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="7250a-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="7250a-130">Możliwość krótkiego obwodu żądania przez [nie przekazanie żądania do następnego oprogramowania pośredniczącego](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="7250a-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="7250a-131">Możliwość utworzenia własnej odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="7250a-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="7250a-132">**Oprogramowanie pośredniczące i moduły są przetwarzane w innej kolejności:**</span><span class="sxs-lookup"><span data-stu-id="7250a-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="7250a-133">Kolejność oprogramowania pośredniczącego opiera się na kolejności, w której są wstawiane do potoku żądania, natomiast kolejność modułów opiera się głównie na zdarzeniach [cyklu życia aplikacji](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="7250a-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="7250a-134">Kolejność oprogramowania pośredniczącego w odniesieniu do odpowiedzi to odwrotność od żądania, natomiast kolejność modułów jest taka sama dla żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="7250a-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="7250a-135">Zobacz [Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="7250a-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Oprogramowanie pośredniczące](http-modules/_static/middleware.png)

<span data-ttu-id="7250a-137">Należy zwrócić uwagę na to, jak na powyższym obrazie oprogramowanie pośredniczące uwierzytelniania krótko obobwóduje żądanie.</span><span class="sxs-lookup"><span data-stu-id="7250a-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="7250a-138">Migrowanie kodu modułu do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="7250a-138">Migrating module code to middleware</span></span>

<span data-ttu-id="7250a-139">Istniejący moduł HTTP będzie wyglądać podobnie do tego:</span><span class="sxs-lookup"><span data-stu-id="7250a-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="7250a-140">Jak pokazano na stronie [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) , ASP.NET Core oprogramowanie pośredniczące jest klasą, która uwidacznia metodę `Invoke` pobierającą `HttpContext` i zwracającą `Task`.</span><span class="sxs-lookup"><span data-stu-id="7250a-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="7250a-141">Nowe oprogramowanie pośredniczące będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="7250a-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="7250a-142">Poprzedni szablon oprogramowania pośredniczącego został pobrany z sekcji podczas [pisania oprogramowania pośredniczącego](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="7250a-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="7250a-143">Klasa pomocnika *MyMiddlewareExtensions* ułatwia skonfigurowanie oprogramowania pośredniczącego w klasie `Startup`.</span><span class="sxs-lookup"><span data-stu-id="7250a-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="7250a-144">Metoda `UseMyMiddleware` dodaje klasę oprogramowania pośredniczącego do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="7250a-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="7250a-145">Usługi wymagane przez oprogramowanie pośredniczące są wprowadzane w konstruktorze oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="7250a-146">Moduł może zakończyć żądanie, na przykład jeśli użytkownik nie ma autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="7250a-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="7250a-147">Oprogramowanie pośredniczące obsługuje to nie wywołując `Invoke` w następnym oprogramowaniu pośredniczącym w potoku.</span><span class="sxs-lookup"><span data-stu-id="7250a-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="7250a-148">Należy pamiętać, że to nie przerywa w pełni żądania, ponieważ poprzednie middlewares nadal będą wywoływane, gdy odpowiedź przejdzie do tyłu przez potok.</span><span class="sxs-lookup"><span data-stu-id="7250a-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="7250a-149">Po przeprowadzeniu migracji funkcjonalności modułu do nowego oprogramowania pośredniczącego, może się okazać, że Twój kod nie kompiluje się, ponieważ Klasa `HttpContext` została znacząco zmieniona w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7250a-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="7250a-150">[Później](#migrating-to-the-new-httpcontext)zobaczysz, jak przeprowadzić migrację do nowego ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="7250a-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="7250a-151">Migrowanie wstawiania modułu do potoku żądania</span><span class="sxs-lookup"><span data-stu-id="7250a-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="7250a-152">Moduły HTTP są zazwyczaj dodawane do potoku żądania przy użyciu *pliku Web. config*:</span><span class="sxs-lookup"><span data-stu-id="7250a-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="7250a-153">Przekształć to, [dodając nowe oprogramowanie pośredniczące](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) do potoku żądania w klasie `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7250a-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="7250a-154">Dokładne miejsce w potoku, w którym wstawiasz nowe oprogramowanie pośredniczące, zależy od zdarzenia, które zostało obsłużone jako moduł (`BeginRequest`, `EndRequest`itp.) i jego kolejność na liście modułów w *pliku Web. config*.</span><span class="sxs-lookup"><span data-stu-id="7250a-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="7250a-155">Jak wspomniano wcześniej, nie ma cyklu życia aplikacji w ASP.NET Core i kolejności, w której odpowiedzi są przetwarzane przez oprogramowanie pośredniczące, różnią się od kolejności używanej przez moduły.</span><span class="sxs-lookup"><span data-stu-id="7250a-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="7250a-156">Może to spowodować, że decyzje dotyczące porządkowania są bardziej trudne.</span><span class="sxs-lookup"><span data-stu-id="7250a-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="7250a-157">Jeśli porządkowanie stanie się problemem, można podzielić moduł na wiele składników oprogramowania pośredniczącego, które mogą być uporządkowane niezależnie.</span><span class="sxs-lookup"><span data-stu-id="7250a-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="7250a-158">Migrowanie kodu programu obsługi do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="7250a-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="7250a-159">Procedura obsługi protokołu HTTP wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="7250a-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="7250a-160">W projekcie ASP.NET Core można je przetłumaczyć na oprogramowanie pośredniczące podobne do tego:</span><span class="sxs-lookup"><span data-stu-id="7250a-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="7250a-161">To oprogramowanie pośredniczące jest bardzo podobne do oprogramowania pośredniczącego odpowiadającego modułom.</span><span class="sxs-lookup"><span data-stu-id="7250a-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="7250a-162">Jedyną rzeczywistą różnicą jest to, że nie ma żadnych wywołań do `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="7250a-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="7250a-163">Ma to sens, ponieważ program obsługi znajduje się na końcu potoku żądania, więc nie będzie można wywołać następnego oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="7250a-164">Migrowanie wstawiania procedury obsługi do potoku żądania</span><span class="sxs-lookup"><span data-stu-id="7250a-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="7250a-165">Konfigurowanie obsługi protokołu HTTP jest wykonywane w *pliku Web. config* i wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="7250a-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="7250a-166">Można to przekonwertować, dodając nowe oprogramowanie pośredniczące programu obsługi do potoku żądania w klasie `Startup`, podobnie jak oprogramowanie pośredniczące konwertowane z modułów.</span><span class="sxs-lookup"><span data-stu-id="7250a-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="7250a-167">Problem z tym podejściem polega na tym, że wyśle wszystkie żądania do nowego oprogramowania pośredniczącego programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="7250a-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="7250a-168">Jednak tylko żądania z danym rozszerzeniem mogą dotrzeć do oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="7250a-169">Zapewnia to takie same funkcje, jak w przypadku programu obsługi protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7250a-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="7250a-170">Jednym z rozwiązań jest rozgałęzienie potoku dla żądań z danym rozszerzeniem przy użyciu metody rozszerzenia `MapWhen`.</span><span class="sxs-lookup"><span data-stu-id="7250a-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="7250a-171">Należy to zrobić w tej samej metodzie `Configure`, w której można dodać inne oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="7250a-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="7250a-172">`MapWhen` przyjmuje następujące parametry:</span><span class="sxs-lookup"><span data-stu-id="7250a-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="7250a-173">Lambda, która pobiera `HttpContext` i zwraca `true`, jeśli żądanie powinno przejść do gałęzi.</span><span class="sxs-lookup"><span data-stu-id="7250a-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="7250a-174">Oznacza to, że można rozgałęziać żądania nie tylko na podstawie ich rozszerzenia, ale także w nagłówkach żądań, parametrach ciągu zapytania itd.</span><span class="sxs-lookup"><span data-stu-id="7250a-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="7250a-175">Lambda, która pobiera `IApplicationBuilder` i dodaje wszystkie oprogramowanie pośredniczące dla gałęzi.</span><span class="sxs-lookup"><span data-stu-id="7250a-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="7250a-176">Oznacza to, że można dodać dodatkowe oprogramowanie pośredniczące do gałęzi przed programem obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="7250a-177">Oprogramowanie pośredniczące dodane do potoku, zanim gałąź zostanie wywołana na wszystkich żądaniach; Rozgałęzienie nie będzie miało wpływu na te elementy.</span><span class="sxs-lookup"><span data-stu-id="7250a-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="7250a-178">Ładowanie opcji oprogramowania pośredniczącego przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="7250a-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="7250a-179">Niektóre moduły i programy obsługi mają opcje konfiguracji, które są przechowywane w *pliku Web. config*. Jednak w ASP.NET Core nowy model konfiguracji jest używany zamiast *pliku Web. config*.</span><span class="sxs-lookup"><span data-stu-id="7250a-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="7250a-180">Nowy [system konfiguracji](xref:fundamentals/configuration/index) zapewnia następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="7250a-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="7250a-181">Bezpośrednio wstrzyknąć opcje do oprogramowania pośredniczącego, jak pokazano w [następnej sekcji](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="7250a-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="7250a-182">Użyj [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="7250a-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="7250a-183">Utwórz klasę zawierającą Opcje oprogramowania pośredniczącego, na przykład:</span><span class="sxs-lookup"><span data-stu-id="7250a-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="7250a-184">Przechowywanie wartości opcji</span><span class="sxs-lookup"><span data-stu-id="7250a-184">Store the option values</span></span>

   <span data-ttu-id="7250a-185">System konfiguracji umożliwia przechowywanie wartości opcji w dowolnym miejscu.</span><span class="sxs-lookup"><span data-stu-id="7250a-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="7250a-186">Jednak większość witryn korzysta z pliku *appSettings. JSON*, dlatego zajmiemy się tym podejściem:</span><span class="sxs-lookup"><span data-stu-id="7250a-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="7250a-187">*MyMiddlewareOptionsSection* tutaj to nazwa sekcji.</span><span class="sxs-lookup"><span data-stu-id="7250a-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="7250a-188">Nie musi być taka sama jak nazwa klasy Options.</span><span class="sxs-lookup"><span data-stu-id="7250a-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="7250a-189">Skojarz wartości opcji z klasą opcji</span><span class="sxs-lookup"><span data-stu-id="7250a-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="7250a-190">Wzorzec opcji używa struktury wstrzykiwania zależności ASP.NET Core do kojarzenia typu opcji (na przykład `MyMiddlewareOptions`) z obiektem `MyMiddlewareOptions`, który ma rzeczywiste opcje.</span><span class="sxs-lookup"><span data-stu-id="7250a-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="7250a-191">Aktualizowanie klasy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7250a-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="7250a-192">Jeśli używasz pliku *appSettings. JSON*, Dodaj go do konstruktora konfiguracji w konstruktorze `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7250a-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="7250a-193">Skonfiguruj usługę opcji:</span><span class="sxs-lookup"><span data-stu-id="7250a-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="7250a-194">Skojarz swoje opcje z klasą opcji:</span><span class="sxs-lookup"><span data-stu-id="7250a-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="7250a-195">Wsuń opcje do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="7250a-196">Jest to podobne do iniekcji opcji do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7250a-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="7250a-197">Metoda rozszerzenia [UseMiddleware](#http-modules-usemiddleware) , która dodaje oprogramowanie pośredniczące do `IApplicationBuilder`, bierze pod uwagę iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="7250a-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="7250a-198">Nie jest to ograniczone do `IOptions` obiektów.</span><span class="sxs-lookup"><span data-stu-id="7250a-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="7250a-199">Każdy inny obiekt, którego potrzebuje oprogramowanie pośredniczące, można wprowadzić w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="7250a-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="7250a-200">Ładowanie opcji oprogramowania pośredniczącego za pośrednictwem bezpośredniego wtrysku</span><span class="sxs-lookup"><span data-stu-id="7250a-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="7250a-201">Wzorzec opcji ma zalety tworzenia swobodnego sprzężenia między wartościami opcji i ich konsumentami.</span><span class="sxs-lookup"><span data-stu-id="7250a-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="7250a-202">Po skojarzeniu klasy opcji z rzeczywistymi wartościami opcji każda inna Klasa może uzyskać dostęp do opcji za pomocą struktury iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7250a-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="7250a-203">Nie ma potrzeby przekazywania żadnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="7250a-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="7250a-204">Ten podział działa inaczej, jeśli chcesz użyć tego samego oprogramowania pośredniczącego dwa razy, z innymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="7250a-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="7250a-205">Na przykład oprogramowanie pośredniczące autoryzacji używane w różnych gałęziach, które zezwalają na różne role.</span><span class="sxs-lookup"><span data-stu-id="7250a-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="7250a-206">Nie można skojarzyć dwóch różnych obiektów Options z jedną klasą opcji.</span><span class="sxs-lookup"><span data-stu-id="7250a-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="7250a-207">Rozwiązaniem jest uzyskanie obiektów Options z rzeczywistymi wartościami opcji w klasie `Startup` i przekazywanie ich bezpośrednio do każdego wystąpienia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="7250a-208">Dodawanie drugiego klucza do pliku *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="7250a-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="7250a-209">Aby dodać drugi zestaw opcji do pliku *appSettings. JSON* , Użyj nowego klucza w celu jego jednoznacznej identyfikacji:</span><span class="sxs-lookup"><span data-stu-id="7250a-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="7250a-210">Pobierz wartości opcji i przekaż je do oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="7250a-211">Metoda rozszerzenia `Use...` (która dodaje oprogramowanie pośredniczące do potoku) jest miejscem logicznym do przekazania w wartościach opcji:</span><span class="sxs-lookup"><span data-stu-id="7250a-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="7250a-212">Włącz oprogramowanie pośredniczące, aby pobrać parametr Options.</span><span class="sxs-lookup"><span data-stu-id="7250a-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="7250a-213">Podaj Przeciążenie metody rozszerzenia `Use...` (która przyjmuje parametr Options i przekazuje ją do `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="7250a-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="7250a-214">Gdy `UseMiddleware` jest wywoływana z parametrami, przekazuje parametry do konstruktora oprogramowania pośredniczącego podczas tworzenia wystąpienia obiektu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="7250a-215">Zwróć uwagę, jak spowoduje to Zawijanie obiektu options w obiekcie `OptionsWrapper`.</span><span class="sxs-lookup"><span data-stu-id="7250a-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="7250a-216">To implementuje `IOptions`, zgodnie z oczekiwaniami przez Konstruktor oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="7250a-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="7250a-217">Migrowanie do nowego obiektu HttpContext</span><span class="sxs-lookup"><span data-stu-id="7250a-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="7250a-218">Wcześniej wywiesz, że metoda `Invoke` w oprogramowaniu pośredniczącym przyjmuje parametr typu `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="7250a-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="7250a-219">`HttpContext` został znacząco zmieniony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7250a-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="7250a-220">W tej sekcji pokazano, jak przetłumaczyć najczęściej używane właściwości elementu [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) na nowe `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="7250a-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="7250a-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="7250a-221">HttpContext</span></span>

<span data-ttu-id="7250a-222">**Element HttpContext. Items** Wykonuje translację do:</span><span class="sxs-lookup"><span data-stu-id="7250a-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="7250a-223">**Unikatowy identyfikator żądania (brak odpowiednika system. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="7250a-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="7250a-224">Zapewnia unikatowy identyfikator dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="7250a-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="7250a-225">Bardzo przydatne do uwzględnienia w dziennikach.</span><span class="sxs-lookup"><span data-stu-id="7250a-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="7250a-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="7250a-226">HttpContext.Request</span></span>

<span data-ttu-id="7250a-227">Element **HttpContext. Request. HttpMethod** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="7250a-228">Element **HttpContext. Request. QueryString** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="7250a-229">**Właściwość HttpContext. Request. URL** i **HttpContext. Request. RawUrl** są tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="7250a-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="7250a-230">Element **HttpContext. Request. IsSecureConnection** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="7250a-231">Element **HttpContext. Request. UserHostAddress** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="7250a-232">Element **HttpContext. Request. cookies** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="7250a-233">Element **HttpContext. Request. RequestContext. RouteData** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="7250a-234">Element **HttpContext. Request. Heads** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="7250a-235">Element **HttpContext. Request. userAgent** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="7250a-236">Element **HttpContext. Request. UrlReferrer** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="7250a-237">Element **HttpContext. Request. ContentType** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="7250a-238">Element **HttpContext. Request. form** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="7250a-239">Odczytaj wartości formularza tylko wtedy, gdy podtyp zawartości to *x-www-form-urlencoded* lub *form-Data*.</span><span class="sxs-lookup"><span data-stu-id="7250a-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="7250a-240">Element **HttpContext. Request. InputStream** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="7250a-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="7250a-241">Użyj tego kodu tylko w oprogramowaniu pośredniczącym typu programu obsługi, na końcu potoku.</span><span class="sxs-lookup"><span data-stu-id="7250a-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="7250a-242">Nieprzetworzoną treść można odczytać, jak pokazano powyżej tylko raz dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="7250a-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="7250a-243">Oprogramowanie pośredniczące próbujące odczytać treść po pierwszym odczytaniu odczyta pustą treść.</span><span class="sxs-lookup"><span data-stu-id="7250a-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="7250a-244">Nie dotyczy to odczytywania formularza jak pokazano wcześniej, ponieważ jest to wykonywane z bufora.</span><span class="sxs-lookup"><span data-stu-id="7250a-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="7250a-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="7250a-245">HttpContext.Response</span></span>

<span data-ttu-id="7250a-246">**Właściwość HttpContext. Response. status** i **HttpContext. Response. StatusDescription** są tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="7250a-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="7250a-247">**Właściwość HttpContext. Response. ContentEncoding** i **HttpContext. Response. ContentType** przekładają się na:</span><span class="sxs-lookup"><span data-stu-id="7250a-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="7250a-248">**Właściwość HttpContext. Response. ContentType** jest również tłumaczona na:</span><span class="sxs-lookup"><span data-stu-id="7250a-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="7250a-249">Element **HttpContext. Response. Output** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="7250a-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="7250a-250">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="7250a-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="7250a-251">W [tym miejscu](../fundamentals/request-features.md#middleware-and-request-features)omówiono obsługę pliku.</span><span class="sxs-lookup"><span data-stu-id="7250a-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="7250a-252">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="7250a-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="7250a-253">Wysyłanie nagłówków odpowiedzi jest skomplikowane przez fakt, że jeśli ustawisz je po zapisaniu jakichkolwiek elementów w treści odpowiedzi, nie będą one wysyłane.</span><span class="sxs-lookup"><span data-stu-id="7250a-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="7250a-254">Rozwiązanie polega na ustawieniu metody wywołania zwrotnego, która będzie wywoływana w prawo przed rozpoczęciem zapisywania do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7250a-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="7250a-255">Jest to najlepsze rozwiązanie na początku metody `Invoke` w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="7250a-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="7250a-256">Jest to metoda wywołania zwrotnego, która ustawia nagłówki odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7250a-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="7250a-257">Poniższy kod ustawia metodę wywołania zwrotnego o nazwie `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="7250a-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="7250a-258">Metoda wywołania zwrotnego `SetHeaders` będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="7250a-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="7250a-259">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="7250a-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="7250a-260">Pliki cookie są przesyłane do przeglądarki w nagłówku odpowiedzi *Set-Cookie* .</span><span class="sxs-lookup"><span data-stu-id="7250a-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="7250a-261">W związku z tym wysyłanie plików cookie wymaga tego samego wywołania zwrotnego, które są używane do wysyłania nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="7250a-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="7250a-262">Metoda wywołania zwrotnego `SetCookies` będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="7250a-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="7250a-263">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7250a-263">Additional resources</span></span>

* [<span data-ttu-id="7250a-264">Obsługa protokołu HTTP i moduły HTTP — Omówienie</span><span class="sxs-lookup"><span data-stu-id="7250a-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="7250a-265">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="7250a-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="7250a-266">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="7250a-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="7250a-267">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="7250a-267">Middleware</span></span>](xref:fundamentals/middleware/index)
