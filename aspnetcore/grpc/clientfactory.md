---
title: gRPC integrację klienta w programie .NET Core
author: jamesnk
description: Dowiedz się, jak tworzyć klientów gRPC przy użyciu fabryki klienta.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: a52fd397a7ed3e327938b0847af7f4e6a4a79400
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310647"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="ca7ee-103">gRPC integrację klienta w programie .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca7ee-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="ca7ee-104">Integracja gRPC z `HttpClientFactory` programem oferuje scentralizowany sposób tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="ca7ee-105">Może służyć jako alternatywa do [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="ca7ee-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="ca7ee-106">Integracja z fabryką jest dostępna w pakiecie NuGet [GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="ca7ee-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="ca7ee-107">Fabryka oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="ca7ee-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="ca7ee-108">Zapewnia centralną lokalizację do konfigurowania wystąpień klienta logicznego gRPC</span><span class="sxs-lookup"><span data-stu-id="ca7ee-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="ca7ee-109">Zarządza okresem istnienia bazowego`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="ca7ee-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="ca7ee-110">Automatyczne propagowanie terminu ostatecznego i anulowanie w ASP.NET Core usłudze gRPC</span><span class="sxs-lookup"><span data-stu-id="ca7ee-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="ca7ee-111">Rejestrowanie klientów gRPC</span><span class="sxs-lookup"><span data-stu-id="ca7ee-111">Register gRPC clients</span></span>

<span data-ttu-id="ca7ee-112">W celu zarejestrowania klienta gRPC można użyć `AddGrpcClient` metody rozszerzenia generycznego w programie `Startup.ConfigureServices`, określając klasę klienta z określonym gRPC i adres usługi:</span><span class="sxs-lookup"><span data-stu-id="ca7ee-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("http://localhost:5001");
});
```

<span data-ttu-id="ca7ee-113">Typ klienta gRPC jest rejestrowany jako przejściowy z iniekcją zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="ca7ee-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="ca7ee-114">Klient może teraz zostać dodany i wykorzystany bezpośrednio w typach utworzonych przez DI.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="ca7ee-115">ASP.NET Core kontrolery MVC, centra sygnałów i usługi gRPC są umieszczane w miejscach, w których można automatycznie dodawać klientów gRPC:</span><span class="sxs-lookup"><span data-stu-id="ca7ee-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="ca7ee-116">Konfigurowanie HttpClient</span><span class="sxs-lookup"><span data-stu-id="ca7ee-116">Configure HttpClient</span></span>

<span data-ttu-id="ca7ee-117">`HttpClientFactory``HttpClient` tworzy używany przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="ca7ee-118">Przy `HttpClientFactory` użyciu metod standardowych można dodać wychodzące oprogramowanie pośredniczące lub skonfigurować podstawową `HttpClientHandler` wartość `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ca7ee-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.BaseAddress = new Uri("http://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="ca7ee-119">Aby uzyskać więcej informacji, zobacz [Tworzenie żądań HTTP przy użyciu IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="ca7ee-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="ca7ee-120">Konfigurowanie kanałów i przechwyceń</span><span class="sxs-lookup"><span data-stu-id="ca7ee-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="ca7ee-121">metody specyficzne dla gRPC są dostępne dla:</span><span class="sxs-lookup"><span data-stu-id="ca7ee-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="ca7ee-122">Skonfiguruj kanał bazowy klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="ca7ee-123">Dodaj `Interceptor` wystąpienia, które będą używane przez klienta podczas wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="ca7ee-124">Termin ostateczny i Propagacja anulowania</span><span class="sxs-lookup"><span data-stu-id="ca7ee-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="ca7ee-125">klientów gRPC utworzonych przez fabrykę w usłudze gRPC można skonfigurować `EnableCallContextPropagation()` w celu automatycznego propagowania terminu ostatecznego i tokenu anulowania do wywołań podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="ca7ee-126">Metoda rozszerzenia jest dostępna w pakiecie NuGet [GRPC. AspNetCore. Server. ClientFactory.](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) `EnableCallContextPropagation()`</span><span class="sxs-lookup"><span data-stu-id="ca7ee-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="ca7ee-127">Propagacja kontekstu wywołania działa, odczytując termin ostateczny i token anulowania z bieżącego kontekstu żądania gRPC i automatycznie propaguje je do wywołań wychodzących wykonywanych przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="ca7ee-128">Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.</span><span class="sxs-lookup"><span data-stu-id="ca7ee-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="ca7ee-129">Aby uzyskać więcej informacji na temat terminów i anulowania wywołania RPC, zobacz [cykl życia usługi RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="ca7ee-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca7ee-130">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ca7ee-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
