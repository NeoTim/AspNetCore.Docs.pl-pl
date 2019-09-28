---
title: Hostowanie i wdrażanie ASP.NET Core Blazor Server
author: guardrex
description: Dowiedz się, jak hostować i wdrażać aplikację Blazor Server przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211610"
---
# <a name="host-and-deploy-blazor-server"></a>Hostowanie i wdrażanie serwera Blazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[Aplikacje serwera Blazor](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>wdrażania

Korzystając z [modelu hostingu serwera Blazor](xref:blazor/hosting-models#blazor-server), Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [](xref:signalr/introduction) połączenie sygnalizujące.

Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core. Program Visual Studio zawiera szablon projektu **aplikacji Blazor Server** (`blazorserverside` szablon w przypadku używania polecenia [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Skalowalność

Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla aplikacji serwera Blazor. Zapoznaj się z poniższymi zasobami, aby rozwiązać o skalowalność aplikacji serwera Blazor:

* [Podstawy aplikacji serwera Blazor](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Serwer wdrażania

Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika. Dostępna pamięć na serwerze ma wpływ na:

* Liczba aktywnych obwodów obsługiwanych przez serwer.
* Opóźnienie interfejsu użytkownika na kliencie.

Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych aplikacji serwera Blazor <xref:security/blazor/server>, zobacz.

Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*. Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami. Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: Jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 użytkowników współbieżnych, rozważ budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).

### <a name="signalr-configuration"></a>Konfiguracja sygnalizującego

Aplikacje serwera Blazor używają sygnalizacji ASP.NET Core do komunikowania się z przeglądarką. [Warunki hostingu i skalowania sygnalizującego](xref:signalr/publish-to-azure-web-app) dotyczą aplikacji serwera Blazor.

Blazor najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako transportu sygnalizującego ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security). Długi sondowanie jest używane przez program sygnalizujący, gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długiego sondowania. Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi. Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [wskazówki dotyczące publikowania sygnałów](xref:signalr/publish-to-azure-web-app).

Zalecamy korzystanie z [usługi Azure Signal Service](/azure/azure-signalr) dla aplikacji serwera Blazor. Usługa umożliwia skalowanie aplikacji serwera Blazor na dużą liczbę współbieżnych połączeń sygnałów. Ponadto globalne zasięgi i wysokiej wydajności centrów danych usługi sygnalizujących znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.

### <a name="measure-network-latency"></a>Mierzenie opóźnienia sieci

Przy użyciu kodu [js Interop](xref:blazor/javascript-interop) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.