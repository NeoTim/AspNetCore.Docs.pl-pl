---
title: gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830661"
---
# <a name="grpc-in-browser-apps"></a>gRPC w aplikacjach przeglądarki

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC — obsługa sieci Web w programie .NET jest eksperymentalna**
>
> gRPC-Web for .NET to eksperymentalny projekt, a nie produkt zatwierdzony. Chcemy:
>
> * Przetestuj, że nasze podejście do implementacji gRPC-Web działa.
> * Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET w porównaniu do tradycyjnego sposobu konfigurowania gRPC-sieci Web za pośrednictwem serwera proxy.
>
> Wystaw opinię w [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , aby upewnić się, że kompilujemy coś, co deweloperzy są podobne i wydajne.

Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który umożliwia aplikacjom JavaScript i Blazor w przeglądarce wywoływanie usług gRPC Services. W tym artykule wyjaśniono, jak używać gRPC-Web w programie .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurowanie gRPC-sieci Web w ASP.NET Core

usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2. gRPC — sieć Web nie wymaga żadnych zmian w usługach. Jedyną modyfikacją jest konfiguracja uruchamiania.

Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:

* Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Skonfiguruj aplikację do używania gRPC-Web, dodając `AddGrpcWeb` i `UseGrpcWeb` do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

Powyższy kod:

* Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb`po stronie routingu i przed punktami końcowymi.
* Określa, `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web z `EnableGrpcWeb`. 

Alternatywnie można skonfigurować wszystkie usługi do obsługi gRPC-Web, dodając `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

Do wywołania gRPC-Web z przeglądarki może być wymagana dodatkowa konfiguracja, taka jak konfigurowanie ASP.NET Core do obsługi mechanizmu CORS. Aby uzyskać więcej informacji, zobacz temat [Obsługa mechanizmu CORS](xref:security/cors).

## <a name="call-grpc-web-from-the-browser"></a>Wywołaj gRPC-Web z przeglądarki

Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services. Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:

* Serwer musi być skonfigurowany do obsługi gRPC-Web.
* Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane. Przesyłanie strumieniowe serwera jest obsługiwane.
* Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC — klient sieci Web

Istnieje skrypt JavaScript gRPC-Web Client. Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC

Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web. Jest to przydatne w przypadku aplikacji [Blazor webassembly](xref:blazor/index#blazor-webassembly) , które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript. Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client). Jedyną modyfikacją jest sposób tworzenia kanału.

Aby użyć gRPC-Web:

* Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Skonfiguruj kanał, aby używał `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Powyższy kod:

* Konfiguruje kanał do korzystania z gRPC-sieci Web.
* Tworzy klienta i wywołuje połączenie przy użyciu kanału.

Podczas tworzenia `GrpcWebHandler` są dostępne następujące opcje konfiguracji:

* **InnerHandler**: podstawowy <xref:System.Net.Http.HttpMessageHandler>, który wykonuje wywołanie http, na przykład `HttpClientHandler`.
* **Tryb**: `GrpcWebMode` enum. `GrpcWebMode.GrpcWebText` konfiguruje zawartość do kodowania base64, która jest wymagana do obsługi wywołań przesyłania strumieniowego serwera.
* **HttpVersion**: protokół http `Version`. gRPC — sieć Web nie wymaga określonego protokołu i nie będzie określać go podczas wysyłania żądania, chyba że zostanie skonfigurowany.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [gRPC dla klientów sieci Web — projekt GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>