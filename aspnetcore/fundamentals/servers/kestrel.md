---
title: Implementacja serwera sieci web kestrel w programie ASP.NET Core
author: guardrex
description: Więcej informacji na temat Kestrel, serwer sieci web dla wielu platform dla platformy ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/17/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 6f9eee1ed46f02232bed977f8f60a3d77db48784
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223158"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="0b211-103">Implementacja serwera sieci web kestrel w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b211-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="0b211-104">Przez [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), i [Halter Autor: Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="0b211-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

<span data-ttu-id="0b211-105">Kestrel jest dla wielu platform [serwera sieci web dla platformy ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="0b211-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="0b211-106">Kestrel jest serwer sieci web, który znajduje się domyślnie w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b211-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="0b211-107">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="0b211-107">Kestrel supports the following scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="0b211-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="0b211-108">HTTPS</span></span>
* <span data-ttu-id="0b211-109">Nieprzezroczysty uaktualnienia, które umożliwiają [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="0b211-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="0b211-110">Gniazda systemu UNIX, dla wysoko wydajnych za serwera Nginx</span><span class="sxs-lookup"><span data-stu-id="0b211-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="0b211-111">Protokołu HTTP/2 (z wyjątkiem w systemie macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="0b211-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="0b211-112">&dagger;Protokołu HTTP/2 będą obsługiwane w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="0b211-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="0b211-113">HTTPS</span><span class="sxs-lookup"><span data-stu-id="0b211-113">HTTPS</span></span>
* <span data-ttu-id="0b211-114">Nieprzezroczysty uaktualnienia, które umożliwiają [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="0b211-114">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="0b211-115">Gniazda systemu UNIX, dla wysoko wydajnych za serwera Nginx</span><span class="sxs-lookup"><span data-stu-id="0b211-115">Unix sockets for high performance behind Nginx</span></span>

::: moniker-end

<span data-ttu-id="0b211-116">Kestrel jest obsługiwane na wszystkich platformach i wersjach, które obsługuje platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b211-116">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="0b211-117">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b211-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a><span data-ttu-id="0b211-118">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="0b211-118">HTTP/2 support</span></span>

<span data-ttu-id="0b211-119">[Protokołu HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępna dla aplikacji platformy ASP.NET Core, jeśli następujące podstawowa wymagania zostały spełnione:</span><span class="sxs-lookup"><span data-stu-id="0b211-119">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="0b211-120">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="0b211-120">Operating system&dagger;</span></span>
  * <span data-ttu-id="0b211-121">Windows Server 2016 i Windows 10 lub nowszym&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0b211-121">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="0b211-122">Linux z protokołem OpenSSL 1.0.2 lub nowszej (na przykład Ubuntu 16.04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="0b211-122">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="0b211-123">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0b211-123">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="0b211-124">[Negocjowania protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) połączenia</span><span class="sxs-lookup"><span data-stu-id="0b211-124">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="0b211-125">Protokół TLS 1.2 lub nowszej połączenia</span><span class="sxs-lookup"><span data-stu-id="0b211-125">TLS 1.2 or later connection</span></span>

<span data-ttu-id="0b211-126">&dagger;Protokołu HTTP/2 będą obsługiwane w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="0b211-126">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="0b211-127">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2, Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0b211-127">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0b211-128">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="0b211-128">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0b211-129">Certyfikat wygenerowany za pomocą Elliptic krzywej cyfrowego Signature Algorithm (ECDSA) może być konieczne bezpiecznych połączeń TLS.</span><span class="sxs-lookup"><span data-stu-id="0b211-129">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="0b211-130">Jeśli zostanie nawiązane połączenie HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporty `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="0b211-130">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="0b211-131">Protokołu HTTP/2 jest domyślnie wyłączona.</span><span class="sxs-lookup"><span data-stu-id="0b211-131">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="0b211-132">Aby uzyskać więcej informacji na temat konfigurowania, zobacz [opcje Kestrel](#kestrel-options) i [ListenOptions.Protocols](#listenoptionsprotocols) sekcje.</span><span class="sxs-lookup"><span data-stu-id="0b211-132">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="0b211-133">Kiedy należy używać Kestrel przy użyciu zwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="0b211-133">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="0b211-134">Można użyć Kestrel, samodzielnie lub z *zwrotnego serwera proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0b211-134">You can use Kestrel by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="0b211-135">Serwer proxy odwrotnej odbiera żądania HTTP z sieci i przekazuje je do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-135">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="0b211-136">Kestrel używany jako serwer sieci web do krawędzi (dostępnym z Internetu):</span><span class="sxs-lookup"><span data-stu-id="0b211-136">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetu bez zwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="0b211-138">Kestrel używane w konfiguracji zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="0b211-138">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem za pośrednictwem serwera zwrotny serwer proxy, na przykład serwer Nginx, Apache lub IIS](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="0b211-140">Każda konfiguracja&mdash;z lub bez serwera proxy odwrotnej&mdash;jest obsługiwana konfiguracja hostingu platformy ASP.NET Core 2.1 lub nowszej aplikacje, które odbierać żądania z Internetu.</span><span class="sxs-lookup"><span data-stu-id="0b211-140">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration for ASP.NET Core 2.1 or later apps that receive requests from the Internet.</span></span>

<span data-ttu-id="0b211-141">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnej nie obsługuje udostępniania tych samych adresów IP i port między różne procesy.</span><span class="sxs-lookup"><span data-stu-id="0b211-141">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="0b211-142">Gdy Kestrel jest skonfigurowana do nasłuchiwania na porcie, Kestrel obsługuje cały ruch do tego portu, niezależnie od tego, żądania `Host` nagłówków.</span><span class="sxs-lookup"><span data-stu-id="0b211-142">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="0b211-143">Zwrotny serwer proxy, który można udostępniać porty zdolność do przekazywania żądań do Kestrel na unikatowych adresów IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="0b211-143">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="0b211-144">Nawet jeśli zwrotnego serwera proxy nie jest wymagane, przy użyciu zwrotnego serwera proxy może być dobrym wyborem.</span><span class="sxs-lookup"><span data-stu-id="0b211-144">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="0b211-145">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="0b211-145">A reverse proxy:</span></span>

* <span data-ttu-id="0b211-146">Można ograniczyć narażonych publiczny obszar powierzchni aplikacji, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="0b211-146">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="0b211-147">Zapewniają dodatkową warstwę konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="0b211-147">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="0b211-148">Może lepiej zintegrować z istniejącą infrastrukturą.</span><span class="sxs-lookup"><span data-stu-id="0b211-148">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="0b211-149">Uprość Równoważenie obciążenia i konfiguracji bezpiecznej komunikacji (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="0b211-149">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="0b211-150">Tylko zwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej za pomocą zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b211-150">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="0b211-151">Hosting w konfiguracji zwrotny serwer proxy wymaga [filtrowania host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0b211-151">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="0b211-152">Jak używać Kestrel w aplikacji platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b211-152">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="0b211-153">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) pakietu znajduje się w [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (platformy ASP.NET Core 2.1 lub nowszej).</span><span class="sxs-lookup"><span data-stu-id="0b211-153">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="0b211-154">Szablony projektów programu ASP.NET Core używa Kestrel domyślnie.</span><span class="sxs-lookup"><span data-stu-id="0b211-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="0b211-155">W *Program.cs*, kod wywołuje szablon <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, która wywołuje metodę <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> w tle.</span><span class="sxs-lookup"><span data-stu-id="0b211-155">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0b211-156">Zapewnienie dodatkowej konfiguracji po wywołaniu `CreateDefaultBuilder`, użyj `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="0b211-156">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

<span data-ttu-id="0b211-157">Jeśli aplikacja nie wywołać `CreateDefaultBuilder` Aby skonfigurować hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **przed** wywoływania `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="0b211-157">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0b211-158">Zapewnienie dodatkowej konfiguracji po wywołaniu `CreateDefaultBuilder`, wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="0b211-158">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a><span data-ttu-id="0b211-159">Opcje kestrel</span><span class="sxs-lookup"><span data-stu-id="0b211-159">Kestrel options</span></span>

<span data-ttu-id="0b211-160">Serwer sieci web Kestrel ma ograniczenie opcje konfiguracji, które są szczególnie przydatne w przypadku wdrożeń z Internetu.</span><span class="sxs-lookup"><span data-stu-id="0b211-160">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="0b211-161">Ustawianie ograniczeń na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwość <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="0b211-161">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="0b211-162">`Limits` Właściwość posiada wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="0b211-162">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="0b211-163">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="0b211-163">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="0b211-164">Pobiera lub ustawia [limitu czasu podtrzymania](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="0b211-164">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="0b211-165">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="0b211-165">Defaults to 2 minutes.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

::: moniker-end

### <a name="maximum-client-connections"></a><span data-ttu-id="0b211-166">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="0b211-166">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="0b211-167">Można ustawić maksymalną liczbę równoczesnych otwarte połączenia TCP dla całej aplikacji, używając następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="0b211-167">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="0b211-168">Ma osobnych limitu połączeń, które zostały uaktualnione do innego protokołu (na przykład na żądanie funkcji WebSockets) z protokołu HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0b211-168">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="0b211-169">Po uaktualnieniu połączenie nie jest uwzględniane `MaxConcurrentConnections` limit.</span><span class="sxs-lookup"><span data-stu-id="0b211-169">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="0b211-170">Maksymalna liczba połączeń jest nieograniczona (null) domyślnie.</span><span class="sxs-lookup"><span data-stu-id="0b211-170">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="0b211-171">Rozmiar treści żądania maksymalna</span><span class="sxs-lookup"><span data-stu-id="0b211-171">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="0b211-172">Domyślny rozmiar treści żądania maksymalna jest 30,000,000 bajtów, czyli około 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="0b211-172">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="0b211-173">Zalecane podejście do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu metody akcji:</span><span class="sxs-lookup"><span data-stu-id="0b211-173">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="0b211-174">Oto przykład pokazujący sposób konfigurowania ograniczeń aplikacji na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="0b211-174">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="0b211-175">Można zastąpić ustawienia dla konkretnego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="0b211-175">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

<span data-ttu-id="0b211-176">Wyjątek jest generowany, Jeśli spróbujesz skonfigurować limit na żądanie, po uruchomieniu aplikacji do odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="0b211-176">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="0b211-177">Brak `IsReadOnly` właściwość, która wskazuje, czy `MaxRequestBodySize` właściwość jest w stanie tylko do odczytu, co oznacza, jest za późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="0b211-177">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="0b211-178">Gdy aplikacja jest uruchamiana [spoza procesu](xref:fundamentals/servers/index#out-of-process-hosting-model) za [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru w treści żądania Kestrel firmy jest wyłączony, ponieważ usługi IIS ustawiają już limit.</span><span class="sxs-lookup"><span data-stu-id="0b211-178">When an app is run [out-of-process](xref:fundamentals/servers/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="0b211-179">Szybkość danych treści żądania minimalne</span><span class="sxs-lookup"><span data-stu-id="0b211-179">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="0b211-180">Kestrel sprawdza co sekundę, jeśli danych jest odebranych zgodnie ze stawką określoną w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="0b211-180">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="0b211-181">Jeśli szybkość spadnie poniżej minimum, jest upłynął limit czasu połączenia. Okres prolongaty to ilość czasu, że Kestrel daje klienta, aby zwiększyć jej szybkość wysyłania do minimum; wskaźnik nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="0b211-181">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="0b211-182">Okres prolongaty pomaga uniknąć porzucenie połączeń, które początkowo wysyłania danych stawki wolno z powodu start wolne TCP.</span><span class="sxs-lookup"><span data-stu-id="0b211-182">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="0b211-183">Minimalna szybkość domyślna jest 240 bajtów na sekundę z 5-sekundowego okresu prolongaty.</span><span class="sxs-lookup"><span data-stu-id="0b211-183">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="0b211-184">Minimalna szybkość dotyczy również odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0b211-184">A minimum rate also applies to the response.</span></span> <span data-ttu-id="0b211-185">Kod, aby ustawić limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem o `RequestBody` lub `Response` w nazwach właściwości i interfejsu.</span><span class="sxs-lookup"><span data-stu-id="0b211-185">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="0b211-186">Oto przykład pokazujący sposób konfigurowania kursów minimalną ilość danych w *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0b211-186">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

<span data-ttu-id="0b211-187">Można zastąpić minimalne limitom na żądanie w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="0b211-187">You can override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0b211-188">Ani funkcji szybkości, do którego odwołuje się poprzedniego przykładu znajdują się w `HttpContext.Features` dla żądania HTTP/2, ponieważ modyfikowanie limitów szybkości na podstawie danego żądania nie jest obsługiwane ze względu na obsługę protokołu Multipleksowanie żądania protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0b211-188">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="0b211-189">Limity szybkości całego serwera, konfigurować za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń zarówno HTTP/1.x, jak i protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0b211-189">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

### <a name="request-headers-timeout"></a><span data-ttu-id="0b211-190">Limit czasu nagłówki żądania</span><span class="sxs-lookup"><span data-stu-id="0b211-190">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="0b211-191">Pobiera lub ustawia maksymalną ilość czasu serwer zużywa odbieranie nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="0b211-191">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="0b211-192">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="0b211-192">Defaults to 30 seconds.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="0b211-193">Maksymalna strumienie na połączenie</span><span class="sxs-lookup"><span data-stu-id="0b211-193">Maximum streams per connection</span></span>

<span data-ttu-id="0b211-194">`Http2.MaxStreamsPerConnection` ogranicza liczbę jednoczesnych żądań strumieni na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0b211-194">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="0b211-195">Nadmiarowe strumieni zostaną odrzucone.</span><span class="sxs-lookup"><span data-stu-id="0b211-195">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="0b211-196">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="0b211-196">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="0b211-197">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="0b211-197">Header table size</span></span>

<span data-ttu-id="0b211-198">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0b211-198">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="0b211-199">`Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówek, który używa dekodera HPACK.</span><span class="sxs-lookup"><span data-stu-id="0b211-199">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="0b211-200">Wartość znajduje się w oktetach i musi być większa niż zero (0).</span><span class="sxs-lookup"><span data-stu-id="0b211-200">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="0b211-201">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="0b211-201">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="0b211-202">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="0b211-202">Maximum frame size</span></span>

<span data-ttu-id="0b211-203">`Http2.MaxFrameSize` Określa maksymalny rozmiar ładunku ramki połączenia protokołu HTTP/2 do odbierania.</span><span class="sxs-lookup"><span data-stu-id="0b211-203">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="0b211-204">Wartość znajduje się w oktetach i musi mieć długość od 2 ^ 14 (16 384) i 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="0b211-204">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="0b211-205">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="0b211-205">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="0b211-206">Rozmiar nagłówka żądania maksymalna</span><span class="sxs-lookup"><span data-stu-id="0b211-206">Maximum request header size</span></span>

<span data-ttu-id="0b211-207">`Http2.MaxRequestHeaderFieldSize` Określa maksymalny dopuszczalny rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="0b211-207">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="0b211-208">Ten limit dotyczy zarówno nazwę, jak i wartości ze sobą w ich skompresowanym i nieskompresowanym oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="0b211-208">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="0b211-209">Wartość musi być większa niż zero (0).</span><span class="sxs-lookup"><span data-stu-id="0b211-209">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="0b211-210">Wartość domyślna to 8192.</span><span class="sxs-lookup"><span data-stu-id="0b211-210">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="0b211-211">Rozmiar okna początkowego połączenia</span><span class="sxs-lookup"><span data-stu-id="0b211-211">Initial connection window size</span></span>

<span data-ttu-id="0b211-212">`Http2.InitialConnectionWindowSize` Wskazuje danych treści żądania maksymalny w bajtach bufory serwera w tym samym czasie agregowana dla wszystkich żądań (strumienie) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="0b211-212">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="0b211-213">Żądania są również ograniczyć, używając `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="0b211-213">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="0b211-214">Wartość musi być mniejsza niż 65 535 i mniej niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="0b211-214">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="0b211-215">Wartość domyślna to 128 KB (131072).</span><span class="sxs-lookup"><span data-stu-id="0b211-215">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="0b211-216">Rozmiar okna początkowej strumienia</span><span class="sxs-lookup"><span data-stu-id="0b211-216">Initial stream window size</span></span>

<span data-ttu-id="0b211-217">`Http2.InitialStreamWindowSize` Wskazuje danych treści żądania maksymalny w bajtach bufory serwera, w tym samym czasie na żądanie (strumień).</span><span class="sxs-lookup"><span data-stu-id="0b211-217">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="0b211-218">Żądania są również ograniczyć, używając `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="0b211-218">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="0b211-219">Wartość musi być mniejsza niż 65 535 i mniej niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="0b211-219">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="0b211-220">Wartość domyślna to (98304) o rozmiarze 96 KB.</span><span class="sxs-lookup"><span data-stu-id="0b211-220">The default value is 96 KB (98,304).</span></span>

::: moniker-end

<span data-ttu-id="0b211-221">Aby uzyskać informacje o innych opcjach Kestrel i limity zobacz:</span><span class="sxs-lookup"><span data-stu-id="0b211-221">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="0b211-222">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="0b211-222">Endpoint configuration</span></span>

<span data-ttu-id="0b211-223">Domyślnie platforma ASP.NET Core wiąże:</span><span class="sxs-lookup"><span data-stu-id="0b211-223">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="0b211-224">`https://localhost:5001` (gdy certyfikat rozwoju lokalnego znajduje się)</span><span class="sxs-lookup"><span data-stu-id="0b211-224">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="0b211-225">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="0b211-225">Specify URLs using the:</span></span>

* <span data-ttu-id="0b211-226">`ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0b211-226">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="0b211-227">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="0b211-227">`--urls` command-line argument.</span></span>
* <span data-ttu-id="0b211-228">`urls` Klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="0b211-228">`urls` host configuration key.</span></span>
* <span data-ttu-id="0b211-229">`UseUrls` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="0b211-229">`UseUrls` extension method.</span></span>

<span data-ttu-id="0b211-230">Podana wartość przy użyciu tych metod może być co najmniej jeden protokół HTTP i HTTPS punktów końcowych (HTTPS Jeśli dostępny jest certyfikatu z domyślną).</span><span class="sxs-lookup"><span data-stu-id="0b211-230">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="0b211-231">Konfiguracja uwzględnia wartość będącą rozdzielonej średnikami liście (na przykład `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="0b211-231">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="0b211-232">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwerów](xref:fundamentals/host/web-host#server-urls) i [zastępczą konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="0b211-232">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="0b211-233">Certyfikat programistyczny jest tworzony:</span><span class="sxs-lookup"><span data-stu-id="0b211-233">A development certificate is created:</span></span>

* <span data-ttu-id="0b211-234">Gdy [zestawu .NET Core SDK](/dotnet/core/sdk) jest zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="0b211-234">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="0b211-235">[Narzędzia dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="0b211-235">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="0b211-236">Niektóre przeglądarki wymagają przyznanie jawnych uprawnień w przeglądarce ufać certyfikatowi rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0b211-236">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="0b211-237">Nowsze szablony projektów i ASP.NET Core 2.1 Konfigurowanie aplikacji są domyślnie uruchamiane przy użyciu protokołu HTTPS i dołączenie [przekierowania protokołu HTTPS i HSTS obsługują](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="0b211-237">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0b211-238">Wywołaj <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metod <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> skonfigurować przedrostki adresów URL i portów dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-238">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="0b211-239">`UseUrls`, `--urls` argument wiersza polecenia `urls` klucz konfiguracji hosta, a `ASPNETCORE_URLS` zmiennej środowiskowej również działają, ale ma ograniczenia, zauważyć później, w tej sekcji (domyślny certyfikat musi być dostępny dla punktu końcowego protokołu HTTPS Konfiguracja).</span><span class="sxs-lookup"><span data-stu-id="0b211-239">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="0b211-240">Platforma ASP.NET Core 2.1 lub nowszej `KestrelServerOptions` konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="0b211-240">ASP.NET Core 2.1 or later `KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a><span data-ttu-id="0b211-241">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="0b211-241">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span></span>

<span data-ttu-id="0b211-242">Określa konfigurację `Action` do uruchamiania dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="0b211-242">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="0b211-243">Wywoływanie `ConfigureEndpointDefaults` wielokrotnie zastępuje starszy `Action`s w ostatnich `Action` określony.</span><span class="sxs-lookup"><span data-stu-id="0b211-243">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
```

::: moniker-end

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a><span data-ttu-id="0b211-244">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="0b211-244">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span></span>

<span data-ttu-id="0b211-245">Określa konfigurację `Action` do uruchamiania dla każdego punktu końcowego protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0b211-245">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="0b211-246">Wywoływanie `ConfigureHttpsDefaults` wielokrotnie zastępuje starszy `Action`s w ostatnich `Action` określony.</span><span class="sxs-lookup"><span data-stu-id="0b211-246">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureHttpsDefaults(options =>
            {
                // certificate is an X509Certificate2
                options.ServerCertificate = certificate;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        });
```

::: moniker-end

### <a name="configureiconfiguration"></a><span data-ttu-id="0b211-247">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="0b211-247">Configure(IConfiguration)</span></span>

<span data-ttu-id="0b211-248">Tworzy moduł ładujący konfiguracji dotyczące konfigurowania Kestrel, która przyjmuje <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="0b211-248">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="0b211-249">Konfiguracja musi należeć do sekcji konfiguracji zakresu dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-249">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="0b211-250">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="0b211-250">ListenOptions.UseHttps</span></span>

<span data-ttu-id="0b211-251">Konfigurowanie usługi Kestrel do używania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0b211-251">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="0b211-252">`ListenOptions.UseHttps` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="0b211-252">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="0b211-253">`UseHttps` &ndash; Konfigurowanie usługi Kestrel do używania protokołu HTTPS przy użyciu domyślnego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="0b211-253">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="0b211-254">Zgłasza wyjątek, jeśli nie domyślnego certyfikatu jest skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="0b211-254">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="0b211-255">`ListenOptions.UseHttps` Parametry:</span><span class="sxs-lookup"><span data-stu-id="0b211-255">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="0b211-256">`filename` jest ścieżka i nazwa pliku certyfikatu, względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0b211-256">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="0b211-257">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.</span><span class="sxs-lookup"><span data-stu-id="0b211-257">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="0b211-258">`configureOptions` jest `Action` skonfigurować `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="0b211-258">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="0b211-259">Zwraca `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="0b211-259">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="0b211-260">`storeName` jest magazyn certyfikatów, z którego można załadować certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="0b211-260">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="0b211-261">`subject` jest to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="0b211-261">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="0b211-262">`allowInvalid` Wskazuje, jeżeli nieprawidłowe certyfikaty należy rozważyć, takich jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="0b211-262">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="0b211-263">`location` jest to lokalizacja magazynu można załadować certyfikatu z.</span><span class="sxs-lookup"><span data-stu-id="0b211-263">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="0b211-264">`serverCertificate` jest to certyfikat X.509.</span><span class="sxs-lookup"><span data-stu-id="0b211-264">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="0b211-265">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0b211-265">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="0b211-266">Jako minimum należy podać certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="0b211-266">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="0b211-267">Obsługiwane konfiguracje opisane w dalszej części:</span><span class="sxs-lookup"><span data-stu-id="0b211-267">Supported configurations described next:</span></span>

* <span data-ttu-id="0b211-268">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="0b211-268">No configuration</span></span>
* <span data-ttu-id="0b211-269">Zamień domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="0b211-269">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="0b211-270">Zmiana wartości domyślnych w kodzie</span><span class="sxs-lookup"><span data-stu-id="0b211-270">Change the defaults in code</span></span>

<span data-ttu-id="0b211-271">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="0b211-271">*No configuration*</span></span>

<span data-ttu-id="0b211-272">Nasłuchuje kestrel `http://localhost:5000` i `https://localhost:5001` (jeśli jest to domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="0b211-272">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="0b211-273">*Zamień domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="0b211-273">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="0b211-274"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie można załadować konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-274"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="0b211-275">Schemat konfiguracji ustawień aplikacji domyślnej HTTPS jest dostępna dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-275">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="0b211-276">Skonfigurować wiele punktów końcowych, w tym adresy URL i certyfikaty, aby korzystać z pliku na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="0b211-276">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="0b211-277">W następującym *appsettings.json* przykładu:</span><span class="sxs-lookup"><span data-stu-id="0b211-277">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="0b211-278">Ustaw **AllowInvalid** do `true` Aby zezwolić na korzystanie z certyfikatów nieprawidłowa (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="0b211-278">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="0b211-279">Punkt końcowy HTTPS, który nie określa certyfikat (**HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowane w obszarze **certyfikaty** > **domyślne** lub certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="0b211-279">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
"Kestrel": {
  "EndPoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; defaults to My>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="0b211-280">Alternatywa dla użycia **ścieżki** i **hasło** dla każdego certyfikatu węzeł jest aby określić certyfikat przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="0b211-280">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="0b211-281">Na przykład **certyfikaty** > **domyślne** certyfikat może być określony jako:</span><span class="sxs-lookup"><span data-stu-id="0b211-281">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="0b211-282">Informacje o schemacie:</span><span class="sxs-lookup"><span data-stu-id="0b211-282">Schema notes:</span></span>

* <span data-ttu-id="0b211-283">Nazwy punktów końcowych jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0b211-283">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="0b211-284">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="0b211-284">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="0b211-285">`Url` Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="0b211-285">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="0b211-286">Format dla tego parametru jest taka sama jak najwyższego poziomu `Urls` parametru konfiguracji, ale jego ograniczone do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="0b211-286">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="0b211-287">Zastąp te punkty końcowe, identyczne ze zdefiniowanymi w najwyższego poziomu `Urls` konfiguracji zamiast dodawać do nich.</span><span class="sxs-lookup"><span data-stu-id="0b211-287">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="0b211-288">Punkty końcowe zdefiniowane w kodzie za pomocą `Listen` kumulują się z punktami końcowymi, zdefiniowane w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="0b211-288">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="0b211-289">`Certificate` Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="0b211-289">The `Certificate` section is optional.</span></span> <span data-ttu-id="0b211-290">Jeśli `Certificate` sekcji nie jest określona, używane są zdefiniowane w scenariuszach wcześniej wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="0b211-290">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="0b211-291">Jeśli dostępnych żadnych ustawień domyślnych, serwer zgłasza wyjątek i nie można uruchomić.</span><span class="sxs-lookup"><span data-stu-id="0b211-291">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="0b211-292">`Certificate` Sekcji obsługuje zarówno **ścieżki**&ndash;**hasło** i **podmiotu**&ndash;**Store** certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="0b211-292">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="0b211-293">Tak długo, jak one nie powodują konfliktów portów można zdefiniować dowolną liczbę punktów końcowych w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="0b211-293">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="0b211-294">`options.Configure(context.Configuration.GetSection("Kestrel"))` Zwraca `KestrelConfigurationLoader` z `.Endpoint(string name, options => { })` metodę, która może służyć jako uzupełnienie ustawienia skonfigurowanego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="0b211-294">`options.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="0b211-295">Można również uzyskać dostęp do `KestrelServerOptions.ConfigurationLoader` można zachować iteracja na istniejące modułu ładującego, takiego jak dostarczone przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="0b211-295">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="0b211-296">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna na temat opcji w `Endpoint` metody, aby ustawienia niestandardowe, które mogą być odczytywane.</span><span class="sxs-lookup"><span data-stu-id="0b211-296">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="0b211-297">Wiele konfiguracji mogą być ładowane przez wywołanie metody `options.Configure(context.Configuration.GetSection("Kestrel"))` ponownie, używając innej sekcji.</span><span class="sxs-lookup"><span data-stu-id="0b211-297">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="0b211-298">Ostatnia konfiguracja jest używany, chyba że `Load` zostanie jawnie wywołany w poprzednich wystąpień.</span><span class="sxs-lookup"><span data-stu-id="0b211-298">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="0b211-299">Nie wywołuje meta Microsoft.aspnetcore.all `Load` tak, aby jego sekcji konfiguracji domyślnej może zostać zastąpiony.</span><span class="sxs-lookup"><span data-stu-id="0b211-299">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="0b211-300">`KestrelConfigurationLoader` wstecznych `Listen` rodziny interfejsów API z `KestrelServerOptions` jako `Endpoint` przeciążeń, dzięki czemu kod i konfiguracji punktów końcowych może zostać skonfigurowana w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="0b211-300">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="0b211-301">Te przeciążenia, nie używaj nazw i tylko używać domyślnych ustawień konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="0b211-301">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="0b211-302">*Zmiana wartości domyślnych w kodzie*</span><span class="sxs-lookup"><span data-stu-id="0b211-302">*Change the defaults in code*</span></span>

<span data-ttu-id="0b211-303">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions`, tym przesłanianie domyślnego certyfikatu określone w poprzednich scenariusza.</span><span class="sxs-lookup"><span data-stu-id="0b211-303">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="0b211-304">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinna być wywoływana przed wszystkie punkty końcowe zostały skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="0b211-304">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="0b211-305">*Obsługa kestrel SNI*</span><span class="sxs-lookup"><span data-stu-id="0b211-305">*Kestrel support for SNI*</span></span>

<span data-ttu-id="0b211-306">[Oznaczaniem nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) mogą być używane do obsługi wielu domen na tym samym adresie IP i port.</span><span class="sxs-lookup"><span data-stu-id="0b211-306">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="0b211-307">Dla SNI do funkcji Klient wysyła nazwę hosta dla bezpiecznej sesji na serwerze podczas uzgadniania TLS tak, aby serwer może zapewnić poprawny certyfikat.</span><span class="sxs-lookup"><span data-stu-id="0b211-307">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="0b211-308">Klient korzysta z certyfikatu złożonych szyfrowaną komunikację z serwerem podczas nawiązywania bezpiecznej sesji, który następuje po TLS handshake.</span><span class="sxs-lookup"><span data-stu-id="0b211-308">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="0b211-309">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="0b211-309">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="0b211-310">Wywołanie zwrotne jest wywoływana jeden raz na połączenie, aby umożliwić aplikacji do sprawdzania nazwy hosta i wybierz odpowiedni certyfikat.</span><span class="sxs-lookup"><span data-stu-id="0b211-310">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="0b211-311">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="0b211-311">SNI support requires:</span></span>

* <span data-ttu-id="0b211-312">Uruchamianie na platformie docelowej `netcoreapp2.1`.</span><span class="sxs-lookup"><span data-stu-id="0b211-312">Running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="0b211-313">Na `netcoreapp2.0` i `net461`, wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null`.</span><span class="sxs-lookup"><span data-stu-id="0b211-313">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="0b211-314">`name` Jest również `null` Jeśli klient nie udostępnia hosta, nazwy parametru w uzgadniania TLS.</span><span class="sxs-lookup"><span data-stu-id="0b211-314">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="0b211-315">Wszystkie witryny sieci Web uruchamiane na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-315">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="0b211-316">Kestrel nie obsługuje udostępniania adresu IP i portów w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="0b211-316">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="0b211-317">Powiąż z gniazda TCP</span><span class="sxs-lookup"><span data-stu-id="0b211-317">Bind to a TCP socket</span></span>

<span data-ttu-id="0b211-318"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda wiąże gniazda TCP i lambda opcje zezwala na konfigurację certyfikatu X.509:</span><span class="sxs-lookup"><span data-stu-id="0b211-318">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

<span data-ttu-id="0b211-319">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="0b211-319">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="0b211-320">Skonfiguruj inne ustawienia Kestrel do określonych punktów końcowych za pomocą tego samego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0b211-320">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="0b211-321">Powiązany z gniazdem systemu Unix</span><span class="sxs-lookup"><span data-stu-id="0b211-321">Bind to a Unix socket</span></span>

<span data-ttu-id="0b211-322">Nasłuchiwanie na gniazdo systemu Unix za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> w celu zwiększenia wydajności przy użyciu serwera Nginx, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0b211-322">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a><span data-ttu-id="0b211-323">Port 0</span><span class="sxs-lookup"><span data-stu-id="0b211-323">Port 0</span></span>

<span data-ttu-id="0b211-324">Gdy numer portu `0` określono Kestrel dynamicznie wiąże dostępny port.</span><span class="sxs-lookup"><span data-stu-id="0b211-324">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="0b211-325">Poniższy przykład pokazuje, jak określić port, który Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="0b211-325">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="0b211-326">Gdy aplikacja jest uruchamiana, dane wyjściowe z okna konsoli wskazuje port dynamiczny, gdzie można połączyć aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0b211-326">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="0b211-327">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="0b211-327">Limitations</span></span>

<span data-ttu-id="0b211-328">Konfigurowanie punktów końcowych przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0b211-328">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="0b211-329">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="0b211-329">`--urls` command-line argument</span></span>
* <span data-ttu-id="0b211-330">`urls` Klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="0b211-330">`urls` host configuration key</span></span>
* <span data-ttu-id="0b211-331">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="0b211-331">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="0b211-332">Te metody są przydatne do tworzenia kodu, pracy z serwerami innych niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-332">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="0b211-333">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="0b211-333">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="0b211-334">Nie można użyć protokołu HTTPS przy użyciu tych metod, jeśli domyślny certyfikat znajduje się w konfiguracji punktu końcowego protokołu HTTPS (na przykład za pomocą `KestrelServerOptions` konfiguracji lub plik konfiguracji, jak pokazano we wcześniejszej części tego tematu).</span><span class="sxs-lookup"><span data-stu-id="0b211-334">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="0b211-335">Gdy zarówno `Listen` i `UseUrls` podejścia są używane równocześnie, `Listen` zastąpienia punktów końcowych `UseUrls` punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="0b211-335">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="0b211-336">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="0b211-336">IIS endpoint configuration</span></span>

<span data-ttu-id="0b211-337">Korzystając z usług IIS, powiązania adres URL dla zastąpienia IIS powiązania są ustawiane przez `Listen` lub `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="0b211-337">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="0b211-338">Aby uzyskać więcej informacji, zobacz [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tematu.</span><span class="sxs-lookup"><span data-stu-id="0b211-338">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a><span data-ttu-id="0b211-339">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="0b211-339">ListenOptions.Protocols</span></span>

<span data-ttu-id="0b211-340">`Protocols` Właściwość ustala protokołów HTTP (`HttpProtocols`) włączone w punkcie końcowym połączenia dla serwera.</span><span class="sxs-lookup"><span data-stu-id="0b211-340">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="0b211-341">Przypisanie wartości do `Protocols` właściwość `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="0b211-341">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="0b211-342">`HttpProtocols` Wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="0b211-342">`HttpProtocols` enum value</span></span> | <span data-ttu-id="0b211-343">Protokół połączenia dozwolone</span><span class="sxs-lookup"><span data-stu-id="0b211-343">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="0b211-344">HTTP/1.1 tylko.</span><span class="sxs-lookup"><span data-stu-id="0b211-344">HTTP/1.1 only.</span></span> <span data-ttu-id="0b211-345">Można używać z lub bez protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="0b211-345">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="0b211-346">Protokołu HTTP/2 tylko.</span><span class="sxs-lookup"><span data-stu-id="0b211-346">HTTP/2 only.</span></span> <span data-ttu-id="0b211-347">Głównie używane z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="0b211-347">Primarily used with TLS.</span></span> <span data-ttu-id="0b211-348">Można używać bez protokołu TLS tylko wtedy, gdy klient obsługuje [tryb uprzednia](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="0b211-348">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="0b211-349">HTTP/1.1 i protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0b211-349">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="0b211-350">Wymaga szyfrowania TLS i [negocjowania protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) połączenia w celu negocjowania protokołu HTTP/2; w przeciwnym razie Domyślnie połączenie HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0b211-350">Requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection to negotiate HTTP/2; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="0b211-351">Protokół domyślny to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0b211-351">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="0b211-352">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="0b211-352">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="0b211-353">Protokół TLS w wersji 1.2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="0b211-353">TLS version 1.2 or later</span></span>
* <span data-ttu-id="0b211-354">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="0b211-354">Renegotiation disabled</span></span>
* <span data-ttu-id="0b211-355">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="0b211-355">Compression disabled</span></span>
* <span data-ttu-id="0b211-356">Rozmiary minimalne efemeryczne wymiany kluczy:</span><span class="sxs-lookup"><span data-stu-id="0b211-356">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="0b211-357">Krzywej eliptycznej Diffie'ego-Hellmana (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 usługi bits, minimalne</span><span class="sxs-lookup"><span data-stu-id="0b211-357">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="0b211-358">Pole skończoną Diffie'ego-Hellmana (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bitów minimalne</span><span class="sxs-lookup"><span data-stu-id="0b211-358">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="0b211-359">Mechanizmy szyfrowania nie na czarnej liście</span><span class="sxs-lookup"><span data-stu-id="0b211-359">Cipher suite not blacklisted</span></span>

<span data-ttu-id="0b211-360">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; z krzywej eliptycznej p-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="0b211-360">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="0b211-361">Poniższy przykład pozwala protokołu HTTP/1.1 i połączeń protokołu HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="0b211-361">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="0b211-362">Połączenia są zabezpieczone przez protokół TLS z dostarczony certyfikat:</span><span class="sxs-lookup"><span data-stu-id="0b211-362">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="0b211-363">Opcjonalnie można utworzyć `IConnectionAdapter` implementacji, aby filtrować uzgodnienia protokołu TLS na podstawie poszczególnych połączeń dla określonych mechanizmów szyfrowania:</span><span class="sxs-lookup"><span data-stu-id="0b211-363">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
                listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
            });
        });
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that you don't 
        // wish to support. Alternatively, define and compare 
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher 
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null 
        // indicates that no cipher algorithm supported by Kestrel matches the 
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="0b211-364">*Ustaw protokół z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="0b211-364">*Set the protocol from configuration*</span></span>

<span data-ttu-id="0b211-365"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie można załadować konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b211-365"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="0b211-366">W następującym *appsettings.json* przykład domyślnym protokołem połączenia (HTTP/1.1 i protokołu HTTP/2) zostanie nawiązane dla wszystkich punktów końcowych Kestrel firmy:</span><span class="sxs-lookup"><span data-stu-id="0b211-366">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndPointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="0b211-367">W poniższym przykładzie plik konfiguracji nawiązuje połączenie protokół dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="0b211-367">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "EndPoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="0b211-368">Protokoły określić w kodzie zastępują wartości ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="0b211-368">Protocols specified in code override values set by configuration.</span></span>

::: moniker-end

## <a name="transport-configuration"></a><span data-ttu-id="0b211-369">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="0b211-369">Transport configuration</span></span>

<span data-ttu-id="0b211-370">W wersji platformy ASP.NET Core 2.1 transport domyślny serwera Kestrel nie jest już oparty na bibliotece libuv, ale na zarządzanych gniazdach.</span><span class="sxs-lookup"><span data-stu-id="0b211-370">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="0b211-371">Jest to istotną zmianę dla aplikacji ASP.NET Core 2.0, uaktualnienie do wersji 2.1, który <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i są zależne od jednej z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="0b211-371">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="0b211-372">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="0b211-372">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="0b211-373">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="0b211-373">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="0b211-374">Dla platformy ASP.NET Core 2.1 lub nowszej projektów używających [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) i wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="0b211-374">For ASP.NET Core 2.1 or later projects that use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and require the use of Libuv:</span></span>

* <span data-ttu-id="0b211-375">Dodawanie zależności dla [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) pakietu do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0b211-375">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                      Version="<LATEST_VERSION>" />
    ```

* <span data-ttu-id="0b211-376">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="0b211-376">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

### <a name="url-prefixes"></a><span data-ttu-id="0b211-377">Przedrostki adresów URL</span><span class="sxs-lookup"><span data-stu-id="0b211-377">URL prefixes</span></span>

<span data-ttu-id="0b211-378">Korzystając z `UseUrls`, `--urls` argument wiersza polecenia `urls` klucz konfiguracji hosta, lub `ASPNETCORE_URLS` zmiennej środowiskowej przedrostki adresów URL może być w dowolnym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="0b211-378">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="0b211-379">Prawidłowe są tylko prefiksy URL protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b211-379">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="0b211-380">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązania adresu URL używającego `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="0b211-380">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="0b211-381">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="0b211-381">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="0b211-382">`0.0.0.0` jest szczególnym przypadkiem, która jest powiązywana z wszystkich adresów IPv4.</span><span class="sxs-lookup"><span data-stu-id="0b211-382">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="0b211-383">Numer portu w adresie IPv6</span><span class="sxs-lookup"><span data-stu-id="0b211-383">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="0b211-384">`[::]` jest to równoważne IPv6 IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="0b211-384">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="0b211-385">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="0b211-385">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="0b211-386">Nazwy, hostów `*`, i `+`, nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="0b211-386">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="0b211-387">Niczego nie został rozpoznany jako prawidłowy adres IP lub `localhost` wiąże wszystkich protokołów IPv4 i IPv6, adresy IP.</span><span class="sxs-lookup"><span data-stu-id="0b211-387">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="0b211-388">Aby powiązać różne nazwy hostów do różnych aplikacji platformy ASP.NET Core przy użyciu tego samego portu, należy użyć [HTTP.sys](xref:fundamentals/servers/httpsys) lub serwer zwrotny serwer proxy, na przykład serwer Nginx, Apache lub IIS.</span><span class="sxs-lookup"><span data-stu-id="0b211-388">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="0b211-389">Hosting w konfiguracji zwrotny serwer proxy wymaga [filtrowania host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0b211-389">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="0b211-390">Host `localhost` nazwa portu numer lub sprzężenia zwrotnego protokołu IP z numerem portu</span><span class="sxs-lookup"><span data-stu-id="0b211-390">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="0b211-391">Gdy `localhost` określono Kestrel próbuje powiązać interfejsów sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="0b211-391">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="0b211-392">Jeśli żądany port jest używany przez inną usługę na albo interfejsu sprzężenia zwrotnego, Kestrel uruchomienie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="0b211-392">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="0b211-393">Jeśli albo interfejsu sprzężenia zwrotnego jest niedostępny z innego powodu (większość często, ponieważ nie jest obsługiwany protokół IPv6), Kestrel dzienniki ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="0b211-393">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="0b211-394">Filtrowanie hosta</span><span class="sxs-lookup"><span data-stu-id="0b211-394">Host filtering</span></span>

<span data-ttu-id="0b211-395">Natomiast Kestrel obsługuje konfigurację oparte na prefiksy takich jak `http://example.com:5000`, Kestrel ignoruje głównie nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="0b211-395">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="0b211-396">Host `localhost` stanowią specjalny przypadek, używane do powiązania z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="0b211-396">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="0b211-397">Hostowanie dowolnego, inne niż jawnych adresów IP, który tworzy powiązanie z wszystkich publicznych adresów IP.</span><span class="sxs-lookup"><span data-stu-id="0b211-397">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="0b211-398">`Host` nagłówki nie są weryfikowane.</span><span class="sxs-lookup"><span data-stu-id="0b211-398">`Host` headers aren't validated.</span></span>

<span data-ttu-id="0b211-399">Obejść ten problem użyj filtrowania hosta w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="0b211-399">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="0b211-400">Oprogramowanie pośredniczące filtrowania w hoście są dostarczane przez [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) pakiet, który znajduje się w [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (platformy ASP.NET Core 2.1 lub nowszej).</span><span class="sxs-lookup"><span data-stu-id="0b211-400">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="0b211-401">Oprogramowanie pośredniczące jest dodawany przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, która wywołuje metodę <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="0b211-401">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="0b211-402">Oprogramowanie pośredniczące filtrowania w hosta jest domyślnie wyłączona.</span><span class="sxs-lookup"><span data-stu-id="0b211-402">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="0b211-403">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` w *appsettings.json*/*appsettings.\< EnvironmentName > .json*.</span><span class="sxs-lookup"><span data-stu-id="0b211-403">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="0b211-404">Wartość jest rozdzieloną średnikami listę nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="0b211-404">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="0b211-405">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0b211-405">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="0b211-406">[Oprogramowanie pośredniczące nagłówki przekazywane](xref:host-and-deploy/proxy-load-balancer) ma również <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcji.</span><span class="sxs-lookup"><span data-stu-id="0b211-406">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="0b211-407">Przekazane oprogramowanie pośredniczące nagłówków i hosta filtrowanie oprogramowanie pośredniczące mają podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="0b211-407">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="0b211-408">Ustawienie `AllowedHosts` jest odpowiednie, gdy za pomocą przekazywanych oprogramowania pośredniczącego nagłówki `Host` nagłówka nie są zachowywane podczas przekazywania żądań przy użyciu zwrotnego serwera proxy lub moduł równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="0b211-408">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="0b211-409">Ustawienie `AllowedHosts` z hosta filtrowania w oprogramowaniu pośredniczącym przydaje się, gdy Kestrel jest używany jako serwer graniczny publicznego lub `Host` nagłówek bezpośrednio jest przekazywany.</span><span class="sxs-lookup"><span data-stu-id="0b211-409">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="0b211-410">Aby uzyskać więcej informacji na temat przekazywane oprogramowania pośredniczącego nagłówków, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="0b211-410">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b211-411">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0b211-411">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="0b211-412">Kod źródłowy kestrel</span><span class="sxs-lookup"><span data-stu-id="0b211-412">Kestrel source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)
* [<span data-ttu-id="0b211-413">RFC 7230: Składnia i Routing komunikatów (ppkt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="0b211-413">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
