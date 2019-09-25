---
title: Host ogólny .NET
author: tdykstra
description: Dowiedz się więcej o hoście ogólnym programu .NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 75af6dc58d31aaad888b14640268bf05c193272d
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248284"
---
# <a name="net-generic-host"></a>Host ogólny .NET

::: moniker range=">= aspnetcore-3.0"

W tym artykule przedstawiono hosta ogólnego platformy .NET Core<xref:Microsoft.Extensions.Hosting.HostBuilder>() i przedstawiono wskazówki dotyczące korzystania z niego.

## <a name="whats-a-host"></a>Co to jest host?

*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:

* Iniekcja zależności (DI)
* Rejestrowanie
* Konfiguracja
* `IHostedService`metod

Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` on każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze di. W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).

Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.

W wersjach ASP.NET Core wcześniejszych niż 3,0 [host sieci Web](xref:fundamentals/host/web-host) jest używany do obciążeń http. Host sieci Web nie jest już zalecany dla aplikacji sieci Web i pozostaje dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami.

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie. `Main` Metody:

* `CreateHostBuilder` Wywołuje metodę, aby utworzyć i skonfigurować obiekt konstruktora.
* Wywołania `Build` i`Run` metody w obiekcie konstruktora.

Oto kod *program.cs* dla obciążenia innego niż HTTP z jedną `IHostedService` implementacją dodaną do kontenera di. 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

W przypadku obciążeń `Main` http Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody. [Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Ustawienia domyślnego konstruktora 

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:

* Ustawia katalog główny zawartości na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory*>.
* Ładuje konfigurację hosta z:
  * Zmienne środowiskowe poprzedzone prefiksem "DOTNET_".
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji z:
  * *appsettings.json*.
  * *appSettings. {Environment}. JSON*.
  * [Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana `Development` w środowisku.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :
  * Konsola
  * Debugowanie
  * EventSource
  * EventLog (tylko w przypadku uruchamiania w systemie Windows)
* Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.

`ConfigureWebHostDefaults` Metody:

* Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych prefiksem "ASPNETCORE_".
* Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji. Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options>.
* Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).
* Dodaje [przekazane nagłówki pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.
* Włącza integrację usług IIS. Aby poznać domyślne opcje usług IIS, <xref:host-and-deploy/iis/index#iis-options>Zobacz.

[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.

## <a name="framework-provided-services"></a>Usługi udostępniane przez platformę

Usługi zarejestrowane automatycznie obejmują następujące elementy:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Aby uzyskać więcej informacji na temat usług udostępnianych przez <xref:fundamentals/dependency-injection#framework-provided-services>platformę, zobacz.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

`IApplicationLifetime`Wstrzyknąć usługę <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dawniej) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania. Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji. Interfejs zawiera `StopApplication` również metodę.

Poniższy przykład to `IHostedService` implementacja, która `IApplicationLifetime` rejestruje zdarzenia:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania. Używana jest Ostatnia zarejestrowana implementacja.

<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>jest implementacją `IHostLifetime` domyślną. `ConsoleLifetime`:

* nasłuchuje kombinacji klawiszy CTRL + C/SIGINT lub SIGTERM <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> i wywołań, aby rozpocząć proces zamykania.
* Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

<xref:Microsoft.Extensions.Hosting.IHostEnvironment> Wsuń usługę do klasy, aby uzyskać informacje o następujących elementach:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentrootpath)

Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje:

* [WebRootPath](#webroot)

## <a name="host-configuration"></a>Konfiguracja hosta

Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.

Konfiguracja hosta jest dostępna z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. Po `ConfigureAppConfiguration` program`HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.

Aby dodać konfigurację hosta, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> Wywołaj `IHostBuilder`polecenie. `ConfigureHostConfiguration`może być wywoływana wiele razy z wynikami. Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.

Dostawca zmiennych środowiskowych z prefiksem `DOTNET_` i argumentami wiersza polecenia są dołączone przez CreateDefaultBuilder. W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` . Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe. Na przykład wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej jest wartością konfiguracji hosta `environment` dla klucza.

Poniższy przykład umożliwia utworzenie konfiguracji hosta:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfiguracja aplikacji

Konfiguracja aplikacji jest tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder`on. `ConfigureAppConfiguration`może być wywoływana wiele razy z wynikami. Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza. 

Konfiguracja utworzona przez `ConfigureAppConfiguration` program jest dostępna pod adresem [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z programu di. Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.

Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Ustawienia dla wszystkich typów aplikacji

Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` prefiks lub. `ASPNETCORE_`

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.

**Klucz**: ApplicationName  
**Typ**: *ciąg*  
**Wartość domyślna**: Nazwa zestawu, który zawiera punkt wejścia aplikacji.
**Zmienna środowiskowa**:`<PREFIX_>APPLICATIONNAME`

Aby ustawić tę wartość, należy użyć zmiennej środowiskowej. 

### <a name="contentrootpath"></a>ContentRootPath

Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości. Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.

**Klucz**: contentRoot  
**Typ**: *ciąg*  
**Wartość domyślna**: Folder, w którym znajduje się zestaw aplikacji.  
**Zmienna środowiskowa**:`<PREFIX_>CONTENTROOT`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub `UseContentRoot` Wywołaj `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a>EnvironmentName

Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość. Wartości zdefiniowane przez platformę `Development`obejmują `Staging`,, `Production`i. W wartościach nie jest rozróżniana wielkość liter.

**Klucz**: środowisko  
**Typ**: *ciąg*  
**Wartość domyślna**: Narzędzi  
**Zmienna środowiskowa**:`<PREFIX_>ENVIRONMENT`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub `UseEnvironment` Wywołaj `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Wartość domyślna to pięć sekund.  Podczas okresu przekroczenia limitu czasu Host:

* Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.

Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.

**Klucz**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Wartość domyślna**: **zmienna środowiskowa**5 sekund:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`. Poniższy przykład ustawia limit czasu na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Ustawienia usługi Web Apps

Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` prefiks lub. `ASPNETCORE_`

Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień. Przykłady kodu, które pokazują, jak wywołać metody `webBuilder` rozszerzane jest `IWebHostBuilder`wystąpieniem, tak jak w poniższym przykładzie:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Gdy `false`, błędy podczas uruchamiania, kończy się hostem. Gdy `true`Host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.

**Klucz**: captureStartupErrors  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: Wartość domyślna to, `true` chybażeaplikacjabędziedziałaćzKestrelzausługamiIIS,gdziedomyślniejestto.`false`  
**Zmienna środowiskowa**:`<PREFIX_>CAPTURESTARTUPERRORS`

Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Po włączeniu lub gdy środowisko jest `Development`, aplikacja przechwytuje szczegółowe błędy.

**Klucz**: detailedErrors  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: FAŁSZ  
**Zmienna środowiskowa**:`<PREFIX_>_DETAILEDERRORS`

Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania. Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji. W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.

**Klucz**: hostingStartupAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: Pusty ciąg  
**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.

**Klucz**: hostingStartupExcludeAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: Pusty ciąg  
**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port przekierowania protokołu HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Klucz**: https_port  
**Typ**: *ciąg*  
**Wartość domyślna**: Nie ustawiono wartości domyślnej.  
**Zmienna środowiskowa**:`<PREFIX_>HTTPS_PORT`

Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy `IWebHostBuilder` użyciu zamiast tych skonfigurowanych `IServer` przy użyciu implementacji.

**Klucz**: preferHostingUrls  
**Typ**: *bool* (`true` or `1`)  
Wartość **Domyślna**: prawda  
**Zmienna środowiskowa**:`<PREFIX_>_PREFERHOSTINGURLS`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls`:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz**: preventHostingStartup  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: FAŁSZ  
**Zmienna środowiskowa**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Zestaw do wyszukiwania `Startup` klasy.

**Klucz**: startupAssembly  
**Typ**: *ciąg*  
**Wartość domyślna**: Zestaw aplikacji  
**Zmienna środowiskowa**:`<PREFIX_>STARTUPASSEMBLY`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup`. `UseStartup`może przyjmować nazwę zestawu (`string`) lub typ (`TStartup`). Jeśli wywoływana `UseStartup` jest wiele metod, pierwszeństwo ma Ostatnia.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>adresy URL

Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań. Na przykład `http://localhost:123`. Użyj "\*", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu ( `http://*:5000`na przykład). Protokół (`http://` lub `https://`) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się między serwerami.

**Klucz**: adresy URL  
**Typ**: *ciąg*  
**Wartość domyślna**: `http://localhost:5000` i`https://localhost:5001`  
**Zmienna środowiskowa**:`<PREFIX_>URLS`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls`:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Ścieżka względna do statycznych zasobów aplikacji.

**Klucz**: Webroot  
**Typ**: *ciąg*  
**Wartość domyślna**: *(Katalog zawartości)/wwwroot*, jeśli ścieżka istnieje. Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.  
**Zmienna środowiskowa**:`<PREFIX_>WEBROOT`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot`:

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a>Zarządzanie okresem istnienia hosta

Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację. Te metody mają wpływ <xref:Microsoft.Extensions.Hosting.IHostedService> na wszystkie implementacje zarejestrowane w kontenerze usługi.

### <a name="run"></a>Uruchom

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta i czeka na zamknięcie klawiszy CTRL + C/SIGINT lub SIGTERM.

### <a name="start"></a>Uruchamianie

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na początku `StartAsync`, który czeka na zakończenie przed kontynuowaniem. Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w ramach podanego limitu czasu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład za pośrednictwem kombinacji klawiszy CTRL + C/SIGINT lub SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Zwraca wartość <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>, która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań. <xref:System.Threading.Tasks.Task>

### <a name="external-control"></a>Zewnętrzna kontrola

Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core aplikacje konfigurują i uruchamiają hosta. Host jest odpowiedzialny za zarządzanie uruchamiania i czasu życia aplikacji.

W tym artykule opisano ASP.NET Core hosta ogólnego (<xref:Microsoft.Extensions.Hosting.HostBuilder>), który jest używany w przypadku aplikacji, które nie przetwarzają żądań HTTP.

Przeznaczeniem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web w celu umożliwienia szerszej tablicy scenariuszy hostów. Obsługa komunikatów, zadań w tle i innych obciążeń innych niż HTTP na podstawie ogólnej korzyści z hosta, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.

Host ogólny jest nowy w ASP.NET Core 2,1 i nie jest odpowiedni dla scenariuszy hostingu w sieci Web. W przypadku scenariuszy hostingu w sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host). Host ogólny zastąpi hosta sieci Web w przyszłej wersji i będzie pełnić rolę podstawowego interfejsu API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([sposobu pobierania](xref:index#how-to-download-a-sample))

Podczas uruchamiania przykładowej aplikacji w [Visual Studio Code](https://code.visualstudio.com/)należy użyć *zewnętrznego lub zintegrowanego terminalu*. Nie uruchamiaj próbki w `internalConsole`.

Aby ustawić konsolę w Visual Studio Code:

1. Otwórz plik *. programu vscode/Launch. JSON* .
1. W konfiguracji **uruchamiania programu .NET Core (konsoli)** zlokalizuj wpis **konsoli** . Ustaw wartość na `externalTerminal` lub `integratedTerminal`.

## <a name="introduction"></a>Wprowadzenie

Ogólna Biblioteka hostów jest dostępna w <xref:Microsoft.Extensions.Hosting> przestrzeni nazw i udostępniana przez pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) . Pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub nowszym). `Microsoft.Extensions.Hosting`

<xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu. Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana przy każdym <xref:Microsoft.Extensions.Hosting.IHostedService> uruchomieniu hosta i <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> jest wywoływana w odwrotnej kolejności rejestracji, gdy host jest zamykany prawidłowo.

## <a name="set-up-a-host"></a>Konfigurowanie hosta

<xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, kompilowania i uruchamiania hosta:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Opcje

<xref:Microsoft.Extensions.Hosting.HostOptions>Skonfiguruj opcje <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Limit czasu zamykania

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Wartość domyślna to pięć sekund.

Następująca konfiguracja opcji w programie `Program.Main` zwiększa domyślny pięć sekund limitu czasu zamykania na 20 s:

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Usługi domyślne

Podczas inicjowania hosta zarejestrowano następujące usługi:

* [Środowisko](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfiguracja](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Opcje](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Rejestrowanie](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konfiguracja hosta

Konfiguracja hosta jest tworzona przez:

* Wywoływanie metod rozszerzenia <xref:Microsoft.Extensions.Hosting.IHostBuilder> w celu ustawienia [katalogu głównego zawartości](#content-root) i [środowiska](#environment).
* Odczytywanie konfiguracji od dostawców konfiguracji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>w programie.

### <a name="extension-methods"></a>Metody rozszerzające

### <a name="application-key-name"></a>Klucz aplikacji (nazwa)

Właściwość [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta. Aby jawnie ustawić wartość, użyj [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klucz**: ApplicationName  
**Typ**: *ciąg*  
**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Ustaw przy użyciu**:`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Zmienna środowiskowa** `<PREFIX_>APPLICATIONNAME` :`<PREFIX_>` (jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))

### <a name="content-root"></a>Katalog główny zawartości

To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.

**Klucz**: contentRoot  
**Typ**: *ciąg*  
**Wartość domyślna**: Domyślnie znajduje się w folderze, w którym znajduje się zestaw aplikacji.  
**Ustaw przy użyciu**:`UseContentRoot`  
**Zmienna środowiskowa** `<PREFIX_>CONTENTROOT` :`<PREFIX_>` (jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))

Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a>Środowisko

Ustawia [środowisko](xref:fundamentals/environments)aplikacji.

**Klucz**: środowisko  
**Typ**: *ciąg*  
**Wartość domyślna**: Narzędzi  
**Ustaw przy użyciu**:`UseEnvironment`  
**Zmienna środowiskowa** `<PREFIX_>ENVIRONMENT` :`<PREFIX_>` (jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))

Dla środowiska można ustawić dowolną wartość. Wartości zdefiniowane przez platformę `Development`obejmują `Staging`,, `Production`i. W wartościach nie jest rozróżniana wielkość liter.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfiguration> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> za pomocą programu można utworzyć dla hosta. Konfiguracja hosta służy do inicjowania <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> do użycia w procesie kompilacji aplikacji.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>może być wywoływana wiele razy z wynikami. Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.

Żaden dostawca nie jest domyślnie uwzględniany. Należy jawnie określić dostawców konfiguracji, których wymaga aplikacja, w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>tym:

* Konfiguracja pliku (na przykład z pliku *HostSettings. JSON* ).
* Konfiguracja zmiennej środowiskowej.
* Konfiguracja argumentu wiersza polecenia.
* Każdy inny wymagany dostawca konfiguracji.

Konfiguracja pliku hosta jest włączana przez określenie ścieżki `SetBasePath` podstawowej aplikacji, po której następuje wywołanie jednego z [dostawców konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider). Przykładowa aplikacja używa pliku JSON, *HostSettings. JSON*oraz wywołań <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> , aby użyć ustawień konfiguracji hosta pliku.

Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> polecenie na konstruktorze hosta. `AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika. Przykładowa aplikacja używa prefiksu `PREFIX_`. Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe. Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej dla `PREFIX_ENVIRONMENT` `environment` klucza będzie wartością konfiguracji hosta.

Podczas programowania podczas korzystania z [programu Visual Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji `dotnet run`w programie zmienne środowiskowe mogą być ustawiane w pliku *Properties/profilu launchsettings. JSON* . W [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *. programu vscode/Launch. JSON* podczas tworzenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) jest dodawana przez wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. Konfiguracja wiersza polecenia jest dodawana jako Ostatnia, aby zezwolić na argumenty wiersza polecenia w celu przesłonięcia konfiguracji udostępnionej przez wcześniejszych dostawców konfiguracji.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Dodatkową konfigurację można uzyskać za pomocą programu [ApplicationName](#application-key-name) i kluczy [contentRoot](#content-root) .

Przykładowa `HostBuilder` konfiguracja <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>przy użyciu:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Konfiguracja aplikacji jest tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfiguration> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> za pomocą programu można utworzyć dla aplikacji. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>może być wywoływana wiele razy z wynikami. Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza. Konfiguracja utworzona przez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> program jest dostępna pod adresem [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i <xref:Microsoft.Extensions.Hosting.IHost.Services*>w.

Konfiguracja aplikacji automatycznie otrzymuje konfigurację hosta dostarczoną przez [ConfigureHostConfiguration](#configurehostconfiguration).

Przykładowa konfiguracja aplikacji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>przy użyciu:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu. Aplikacja Przykładowa przenosi pliki ustawień aplikacji JSON i *HostSettings. JSON* z następującym `<Content>` elementem:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozszerzenia konfiguracji, takie jak <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> i <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> wymagające dodatkowych pakietów NuGet, takie jak [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Jeśli aplikacja nie używa [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), należy dodać te pakiety do projektu oprócz podstawowego pakietu [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) . Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>może być wywoływana wiele razy z wynikami.

Usługa hostowana jest klasą z logiką zadań w tle, <xref:Microsoft.Extensions.Hosting.IHostedService> która implementuje interfejs. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.

[Przykładowa aplikacja](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzającej, aby dodać usługę dla zdarzeń okresu istnienia, `LifetimeEventsHostedService` `TimedHostedService`oraz zadanie w tle czasu, do aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje delegata do konfigurowania podanego <xref:Microsoft.Extensions.Logging.ILoggingBuilder>elementu. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>może być wywoływana wiele razy z wynikami.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje kombinacji klawiszy CTRL + C/SIGINT lub SIGTERM <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> i wywołań, aby rozpocząć proces zamykania. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync). <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia. Używany jest ostatni zarejestrowany okres istnienia.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfiguracja kontenera

Aby zapewnić obsługę podłączania w innych kontenerach, host może <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>akceptować. Dostarczenie fabryki nie jest częścią rejestracji typu DI Container, ale zamiast tego jest wewnętrznym hostem używanym do tworzenia konkretnych kontenerów DI. [UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.

Niestandardowa konfiguracja kontenera jest zarządzana przez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodę. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>Program zapewnia silnie wpisaną funkcję konfigurowania kontenera na podstawie podstawowego interfejsu API hosta. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>może być wywoływana wiele razy z wynikami.

Utwórz kontener usługi dla aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Podaj fabrykę kontenera usługi:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Użyj fabryki i skonfiguruj niestandardowy kontener usługi dla aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozszerzalność

Rozszerzalność hosta jest wykonywana z metodami <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozszerzenia w systemie. Poniższy przykład pokazuje, jak Metoda rozszerzania rozszerza <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementację z przykładem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionym w <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikacja ustanowi `UseHostedService` metodę rozszerzenia w celu zarejestrowania `T`przesyłanej usługi hostowanej:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Zarządzanie hostem

Implementacja jest odpowiedzialna za uruchamianie i <xref:Microsoft.Extensions.Hosting.IHostedService> zatrzymywanie implementacji, które są zarejestrowane w kontenerze usługi. <xref:Microsoft.Extensions.Hosting.IHost>

### <a name="run"></a>Uruchom

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta i czeka na zamknięcie klawiszy CTRL + C/SIGINT lub SIGTERM.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Uruchom i StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w ramach podanego limitu czasu.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync i StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>kończy działanie aplikacji.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany za <xref:Microsoft.Extensions.Hosting.IHostLifetime>pośrednictwem, <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> na przykład (nasłuchuje w przypadku kombinacji klawiszy CTRL + C/SIGINT lub SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>wywołania <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Zwraca wartość <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>, która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań. <xref:System.Threading.Tasks.Task>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Zewnętrzna kontrola

Kontrolę zewnętrzną hosta można osiągnąć przy użyciu metod, które mogą być wywoływane zewnętrznie:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na początku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, który czeka na zakończenie przed kontynuowaniem. Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment, interfejs

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingu aplikacji. Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> Aby uzyskać w celu użycia jej właściwości i metod rozszerzających:

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime, interfejs

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zezwala na działania po uruchomieniu i zamknięcia, w tym bezpieczne żądania zamknięcia. Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania <xref:System.Action> metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania | Wyzwalane, gdy&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Host został w pełni uruchomiony. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Host kończy bezpieczne zamknięcie. Wszystkie żądania powinny być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Host wykonuje bezpieczne zamknięcie. Żądania mogą nadal być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |

Konstruktor — wstrzyknięcie <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługi do dowolnej klasy. [Przykładowa aplikacja](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora do `LifetimeEventsHostedService` klasy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji), aby zarejestrować zdarzenia.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda zakończenia aplikacji. Następująca Klasa służy <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> do bezpiecznego zamykania aplikacji w przypadku wywołania `Shutdown` metody klasy:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/host/hosted-services>
