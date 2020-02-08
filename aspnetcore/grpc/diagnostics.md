---
title: Rejestrowanie i Diagnostyka w programie gRPC na platformie .NET
author: jamesnk
description: Dowiedz się, jak zbierać diagnostykę z aplikacji gRPC na platformie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074526"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Rejestrowanie i Diagnostyka w programie gRPC na platformie .NET

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Ten artykuł zawiera wskazówki dotyczące zbierania diagnostyki z aplikacji gRPC w celu ułatwienia rozwiązywania problemów. Omawiane tematy to m.in.:

* **Rejestrowanie** dzienników strukturalnych Zapisano do [rejestrowania w programie .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger> jest używany przez platformy aplikacji do pisania dzienników oraz przez użytkowników w celu ich własnego rejestrowania w aplikacji.
* **Śledzenie** — zdarzenia związane z operacją zapisaną przy użyciu `DiaganosticSource` i `Activity`. Ślady ze źródła diagnostycznego są często używane do zbierania danych telemetrycznych aplikacji według bibliotek takich jak [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) i [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Metryki** — reprezentacja miar danych przez przedziały czasu, na przykład żądania na sekundę. Metryki są emitowane przy użyciu `EventCounter` i mogą być zaobserwowane przy użyciu narzędzia wiersza polecenia usługi [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) lub z [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Rejestrowanie

usługi gRPC i dzienniki zapisu klienta gRPC przy użyciu funkcji [rejestrowania w programie .NET Core](xref:fundamentals/logging/index). Dzienniki są dobrym miejscem do uruchomienia, gdy zachodzi potrzeba debugowania nieoczekiwanego zachowania w aplikacjach.

### <a name="grpc-services-logging"></a>Rejestrowanie usług gRPC Services

> [!WARNING]
> Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji. **Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Ponieważ usługi gRPC Services są hostowane na ASP.NET Core, korzysta z systemu rejestrowania ASP.NET Core. W konfiguracji domyślnej gRPC rejestruje bardzo mało informacji, ale można je skonfigurować. Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET Core](xref:fundamentals/logging/index#configuration) .

gRPC dodaje dzienniki w kategorii `Grpc`. Aby włączyć szczegółowe dzienniki z gRPC, skonfiguruj prefiksy `Grpc` do poziomu `Debug` w pliku *appSettings. JSON* przez dodanie następujących elementów do podsekcji `LogLevel` w `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Można to również skonfigurować w *Startup.cs* z `ConfigureLogging`:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Jeśli nie korzystasz z konfiguracji opartej na notacji JSON, ustaw w systemie konfiguracji następującą wartość konfiguracji:

* `Logging:LogLevel:Grpc` = `Debug`

Zapoznaj się z dokumentacją systemu konfiguracyjnego, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych. Na przykład w przypadku używania zmiennych środowiskowych zamiast `:` są używane dwa `_` znaki (na przykład `Logging__LogLevel__Grpc`).

Zalecamy użycie poziomu `Debug` podczas zbierania bardziej szczegółowych informacji diagnostycznych dla aplikacji. Na poziomie `Trace` powstaje Diagnostyka bardzo niskiego poziomu i jest rzadko wymagana do diagnozowania problemów w aplikacji.

#### <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

Oto przykład danych wyjściowych konsoli na poziomie `Debug` usługi gRPC:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Dostęp do dzienników po stronie serwera

Sposób dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiony program.

#### <a name="as-a-console-app"></a>Jako Aplikacja konsolowa

Jeśli używasz programu w aplikacji konsolowej, [Rejestrator konsoli](xref:fundamentals/logging/index#console-provider) powinien być domyślnie włączony. Dzienniki gRPC będą wyświetlane w konsoli programu.

#### <a name="other-environments"></a>Inne środowiska

Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz <xref:fundamentals/logging/index>, aby uzyskać więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednie dla danego środowiska.

### <a name="grpc-client-logging"></a>Rejestrowanie klienta gRPC

> [!WARNING]
> Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji. **Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Aby uzyskać dzienniki z klienta platformy .NET, można ustawić właściwość `GrpcChannelOptions.LoggerFactory` podczas tworzenia kanału klienta. Jeśli wywołujesz usługę gRPC z aplikacji ASP.NET Core, fabryka rejestratorów może zostać rozpoznana z iniekcji zależności (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Alternatywnym sposobem włączenia rejestrowania klienta jest użycie [fabryki klienta gRPC](xref:grpc/clientfactory) do utworzenia klienta programu. Klient gRPC zarejestrowany w fabryce klienta i rozwiązany przez program DI automatycznie użyje skonfigurowanego rejestrowania aplikacji.

Jeśli aplikacja nie korzysta z programu DI, można utworzyć nowe wystąpienie `ILoggerFactory` za pomocą [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Aby uzyskać dostęp do tej metody, Dodaj pakiet [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) do aplikacji.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC zakresy dziennika klienta

Klient gRPC dodaje [zakres rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) do dzienników wykonywanych podczas wywołania gRPC. Zakres zawiera metadane dotyczące wywołania gRPC:

* **GrpcMethodType** — typ metody gRPC. Możliwe wartości to nazwy z `Grpc.Core.MethodType` Wyliczenie, np. jednoargumentowy
* **GrpcUri** — WZGLĘDNY identyfikator URI metody gRPC, np./Greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

Oto przykład danych wyjściowych konsoli na poziomie `Debug` klienta gRPC:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Śledzenie

usługi gRPC oraz klient gRPC dostarczają informacji o wywołaniach gRPC za pomocą [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) i [działania](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC używa działania do reprezentowania wywołania gRPC.
* Zdarzenia śledzenia są zapisywane w źródle diagnostycznym na początek i zatrzymanie działania wywołania gRPC.
* Śledzenie nie przechwytuje informacji o wysyłaniu komunikatów przez okres istnienia wywołań przesyłania strumieniowego gRPC.

### <a name="grpc-service-tracing"></a>Śledzenie usługi gRPC

usługi gRPC Services są hostowane na ASP.NET Core które raportują zdarzenia dotyczące przychodzących żądań HTTP. gRPC określone metadane są dodawane do istniejącej diagnostyki żądania HTTP, która zapewnia ASP.NET Core.

* Nazwa źródła diagnostyki jest `Microsoft.AspNetCore`.
* Nazwa działania jest `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako tag o nazwie `grpc.method`.
  * Kod stanu wywołania gRPC po zakończeniu zostanie dodany jako tag o nazwie `grpc.status_code`.

### <a name="grpc-client-tracing"></a>Śledzenie klienta gRPC

Klient .NET gRPC używa `HttpClient` do wykonywania wywołań gRPC. Mimo że `HttpClient` zapisuje zdarzenia diagnostyczne, klient .NET gRPC udostępnia niestandardowe źródło diagnostyki, działanie i zdarzenia, dzięki czemu można zbierać kompletne informacje dotyczące wywołania gRPC.

* Nazwa źródła diagnostyki jest `Grpc.Net.Client`.
* Nazwa działania jest `Grpc.Net.Client.GrpcOut`.
  * Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako tag o nazwie `grpc.method`.
  * Kod stanu wywołania gRPC po zakończeniu zostanie dodany jako tag o nazwie `grpc.status_code`.

### <a name="collecting-tracing"></a>Zbieranie śledzenia

Najprostszym sposobem korzystania z `DiagnosticSource` jest skonfigurowanie biblioteki telemetrii, takiej jak [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) lub [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) w aplikacji. Biblioteka będzie przetwarzać informacje o wywołaniach gRPC wraz z inną telemetrią aplikacji.

Śledzenie można wyświetlać w zarządzanej usłudze, takiej jak Application Insights, lub można uruchomić własny system śledzenia rozproszonego. OpenTelemetry obsługuje eksportowanie danych śledzenia do [Jaeger](https://www.jaegertracing.io/) i [Zipkin](https://zipkin.io/).

`DiagnosticSource` może zużywać zdarzenia śledzenia w kodzie przy użyciu `DiagnosticListener`. Aby uzyskać informacje o nasłuchiwaniu źródła diagnostycznego z kodem, zobacz [Podręcznik użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Biblioteki telemetrii nie przechwytują gRPC `Grpc.Net.Client.GrpcOut` konkretnych danych telemetrycznych. Działa w celu usprawnienia bibliotek telemetrycznych przechwytujących to śledzenie.

## <a name="metrics"></a>Metryki

Metryki są reprezentacją miar danych przez przedziały czasu, na przykład żądania na sekundę. Dane metryk umożliwiają obserwację stanu aplikacji na wysokim poziomie. Metryki programu .NET gRPC są emitowane przy użyciu `EventCounter`.

### <a name="grpc-service-metrics"></a>metryki usługi gRPC

metryki serwera gRPC są zgłaszane w `Grpc.AspNetCore.Server` źródle zdarzeń.

| Nazwa                      | Opis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Łączna liczba wywołań                   |
| `current-calls`           | Bieżące wywołania                 |
| `calls-failed`            | Łączna liczba nieudanych wywołań            |
| `calls-deadline-exceeded` | Przekroczono łączny termin ostateczny wywołań |
| `messages-sent`           | Całkowita liczba wysłanych komunikatów           |
| `messages-received`       | Całkowita liczba odebranych komunikatów       |
| `calls-unimplemented`     | Łączna liczba wywołań niezaimplementowanych     |

ASP.NET Core również udostępnia własne metryki `Microsoft.AspNetCore.Hosting` źródła zdarzeń.

### <a name="grpc-client-metrics"></a>metryki klienta gRPC

metryki klienta gRPC są zgłaszane w `Grpc.Net.Client` źródle zdarzeń.

| Nazwa                      | Opis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Łączna liczba wywołań                   |
| `current-calls`           | Bieżące wywołania                 |
| `calls-failed`            | Łączna liczba nieudanych wywołań            |
| `calls-deadline-exceeded` | Przekroczono łączny termin ostateczny wywołań |
| `messages-sent`           | Całkowita liczba wysłanych komunikatów           |
| `messages-received`       | Całkowita liczba odebranych komunikatów       |

### <a name="observe-metrics"></a>Obserwuj metryki

[dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) to narzędzie do monitorowania wydajności dla monitorowania kondycji ad hoc i badania wydajności pierwszego poziomu. Monitoruj aplikację .NET przy użyciu `Grpc.AspNetCore.Server` lub `Grpc.Net.Client` jako nazwy dostawcy.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Innym sposobem na obserwowanie metryk gRPC jest przechwycenie danych licznika przy Application Insights użyciu [pakietu Microsoft. ApplicationInsights. EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters). Po zakończeniu instalacji Application Insights zbiera typowe liczniki platformy .NET w czasie wykonywania. Liczniki gRPC nie są zbierane domyślnie, ale usługi App Insights można [dostosować w celu uwzględnienia dodatkowych liczników](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Określ gRPC Liczniki usługi Application Insights do zebrania w *Startup.cs*:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
