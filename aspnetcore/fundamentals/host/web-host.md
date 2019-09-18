---
title: ASP.NET Core hosta sieci Web
author: guardrex
description: Dowiedz się więcej o hoście sieci Web w ASP.NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: d387098662cc832cc0e49b6a1636f0ebcc7308de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081686"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core hosta sieci Web

Przez [Luke Latham](https://github.com/guardrex)

ASP.NET Core aplikacje konfigurują i uruchamiają *hosta*. Host jest odpowiedzialny za zarządzanie uruchamiania i czasu życia aplikacji. Host konfiguruje co najmniej serwer i potok przetwarzania żądań. Na hoście można także skonfigurować rejestrowanie, iniekcję zależności i konfigurację.

::: moniker range=">= aspnetcore-3.0"

W tym artykule omówiono hosta sieci Web, który jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami. [Host ogólny](xref:fundamentals/host/generic-host) jest zalecany dla wszystkich typów aplikacji.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

W tym artykule omówiono hosta sieci Web, który służy do hostowania aplikacji sieci Web. W przypadku innych rodzajów aplikacji należy użyć [hosta ogólnego](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Jest to zwykle wykonywane w punkcie wejścia aplikacji, w `Main` metodzie.

W szablonach `Main` projektu znajduje się w *program.cs*. Typowe wywołania aplikacji [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do rozpoczęcia konfigurowania hosta:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

Kod, który wywołuje `CreateDefaultBuilder` , znajduje się w metodzie o nazwie `CreateWebHostBuilder`, która oddziela ją od kodu `Main` w ramach `Run` tego wywołania w obiekcie konstruktora. Ta separacja jest wymagana, jeśli używasz [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/). Narzędzia oczekują na znalezienie `CreateWebHostBuilder` metody, którą mogą wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji. Alternatywą jest zaimplementowanie `IDesignTimeDbContextFactory`. Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

`CreateDefaultBuilder`wykonuje następujące zadania:

* Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji. Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options>.
* Ustawia katalog główny zawartości dla ścieżki zwróconej przez [katalog. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Ładuje [konfigurację hosta](#host-configuration-values) z:
  * Zmienne środowiskowe poprzedzone `ASPNETCORE_` prefiksem (na `ASPNETCORE_ENVIRONMENT`przykład).
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji w następującej kolejności:
  * *appsettings.json*.
  * *appSettings. {Environment}. JSON*.
  * [Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana `Development` w środowisku przy użyciu zestawu wpisów.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Konfiguruje [Rejestrowanie](xref:fundamentals/logging/index) dla konsoli i danych wyjściowych debugowania. Rejestrowanie obejmuje reguły [filtrowania dzienników](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracja rejestrowania w pliku *appSettings. JSON* lub *appSettings. { Environment} plik JSON* .
* Po uruchomieniu usług IIS za pomocą [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)program `CreateDefaultBuilder` włącza [integrację usług IIS](xref:host-and-deploy/iis/index), która konfiguruje podstawowy adres i port aplikacji. Integracja usług IIS konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors). Aby poznać domyślne opcje usług IIS, <xref:host-and-deploy/iis/index#iis-options>Zobacz.
* Ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , jeśli środowisko aplikacji jest opracowywane. Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).

`CreateDefaultBuilder` Konfiguracja zdefiniowana przez można zastąpić i rozszerzyć przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzające [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Poniżej przedstawiono kilka przykładów:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do określania dodatkowych `IConfiguration` aplikacji. Następujące `ConfigureAppConfiguration` wywołanie dodaje delegata w celu uwzględnienia konfiguracji aplikacji w pliku *appSettings. XML* . `ConfigureAppConfiguration`może być wywoływana wiele razy. Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska). Zapoznaj się z sekcją [wartości konfiguracji hosta](#host-configuration-values) .

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Następujące `ConfigureLogging` wywołanie dodaje delegata w celu skonfigurowania minimalnego poziomu rejestrowania ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na wartość [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel). To ustawienie zastępuje ustawienia w *appSettings. Development. JSON* (`LogLevel.Debug`) i *appSettings. Production. JSON* (`LogLevel.Error`) skonfigurowany `CreateDefaultBuilder`przez. `ConfigureLogging`może być wywoływana wiele razy.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Następujące wywołanie `ConfigureKestrel` przesłania domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Następujące wywołanie [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

*Katalog główny zawartości* określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC. Gdy aplikacja jest uruchamiana z folderu głównego projektu, folder główny projektu jest używany jako katalog główny zawartości. Jest to ustawienie domyślne używane w programie [Visual Studio](https://visualstudio.microsoft.com) i w [nowych szablonach dotnet](/dotnet/core/tools/dotnet-new).

Aby uzyskać więcej informacji na temat konfiguracji aplikacji <xref:fundamentals/configuration/index>, zobacz.

> [!NOTE]
> Alternatywą dla użycia metody statycznej `CreateDefaultBuilder` jest utworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście ASP.NET Core 2. x.

Podczas konfigurowania hosta można dostarczyć metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) . Jeśli klasa jest określona, musi `Configure` definiować metodę. `Startup` Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>. Wiele wywołań do `ConfigureServices` dołączenia do siebie nawzajem. Wiele wywołań `Configure` lub `UseStartup` Zastąppoprzednieustawienia`WebHostBuilder` .

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach do ustawiania wartości konfiguracji hosta:

* Konfiguracja konstruktora hostów, która zawiera zmienne środowiskowe w formacie `ASPNETCORE_{configurationKey}`. Na przykład `ASPNETCORE_ENVIRONMENT`.
* Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Konfiguracja zastąpień](#override-configuration) ).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz. Podczas ustawiania wartości za pomocą `UseSetting`, wartość jest ustawiana jako ciąg, niezależnie od typu.

Host używa dowolnej opcji ustawia wartość Last. Aby uzyskać więcej informacji, zobacz [Przesłoń konfigurację](#override-configuration) w następnej sekcji.

### <a name="application-key-name"></a>Klucz aplikacji (nazwa)

Właściwość [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta. Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji. Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

**Klucz**: ApplicationName  
**Typ**: *ciąg*  
**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Ustaw przy użyciu**:`UseSetting`  
**Zmienna środowiskowa**:`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Błędy uruchamiania przechwytywania

To ustawienie steruje przechwytywaniem błędów uruchamiania.

**Klucz**: captureStartupErrors  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: Wartość domyślna to, `true` chybażeaplikacjabędziedziałaćzKestrelzausługamiIIS,gdziedomyślniejestto.`false`  
**Ustaw przy użyciu**:`CaptureStartupErrors`  
**Zmienna środowiskowa**:`ASPNETCORE_CAPTURESTARTUPERRORS`

Gdy `false`, błędy podczas uruchamiania, kończy się hostem. Gdy `true`Host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Katalog główny zawartości

To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości, takich jak widoki MVC. 

**Klucz**: contentRoot  
**Typ**: *ciąg*  
**Wartość domyślna**: Domyślnie znajduje się w folderze, w którym znajduje się zestaw aplikacji.  
**Ustaw przy użyciu**:`UseContentRoot`  
**Zmienna środowiskowa**:`ASPNETCORE_CONTENTROOT`

Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [Ustawienia katalogu głównego sieci Web](#web-root). Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a>Szczegóły błędów

Określa, czy mają być przechwytywane szczegółowe błędy.

**Klucz**: detailedErrors  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: FAŁSZ  
**Ustaw przy użyciu**:`UseSetting`  
**Zmienna środowiskowa**:`ASPNETCORE_DETAILEDERRORS`

Po włączeniu (lub gdy <a href="#environment">środowisko</a> jest ustawione na `Development`), aplikacja przechwytuje szczegółowe wyjątki.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Środowisko

Ustawia środowisko aplikacji.

**Klucz**: środowisko  
**Typ**: *ciąg*  
**Wartość domyślna**: Narzędzi  
**Ustaw przy użyciu**:`UseEnvironment`  
**Zmienna środowiskowa**:`ASPNETCORE_ENVIRONMENT`

Dla środowiska można ustawić dowolną wartość. Wartości zdefiniowane przez platformę `Development`obejmują `Staging`,, `Production`i. W wartościach nie jest rozróżniana wielkość liter. Domyślnie *środowisko* jest odczytywane ze `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej. W przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe można ustawić w pliku *profilu launchsettings. JSON* . Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hostowanie zestawów startowych

Ustawia zestawy uruchomieniowe obsługujące aplikację.

**Klucz**: hostingStartupAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: Pusty ciąg  
**Ustaw przy użyciu**:`UseSetting`  
**Zmienna środowiskowa**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.

Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji. W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>Port HTTPS

Ustaw port przekierowania HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Key**: https_port **Type**:
**wartość domyślna**: Nie ustawiono wartości domyślnej.
**Ustaw przy użyciu**: `UseSetting`
**Zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hosting — wykluczanie zestawów

Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.

**Klucz**: hostingStartupExcludeAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: Pusty ciąg  
**Ustaw przy użyciu**:`UseSetting`  
**Zmienna środowiskowa**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferuj adresy URL hostingu

Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy `WebHostBuilder` użyciu zamiast tych skonfigurowanych `IServer` przy użyciu implementacji.

**Klucz**: preferHostingUrls  
**Typ**: *bool* (`true` or `1`)  
Wartość **Domyślna**: prawda  
**Ustaw przy użyciu**:`PreferHostingUrls`  
**Zmienna środowiskowa**:`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Zapobiegaj uruchamianiu hostingu

Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz**: preventHostingStartup  
**Typ**: *bool* (`true` or `1`)  
**Wartość domyślna**: FAŁSZ  
**Ustaw przy użyciu**:`UseSetting`  
**Zmienna środowiskowa**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Adresy URL serwera

Wskazuje adresy IP lub adresy hosta z portami i protokołami, na których serwer powinien nasłuchiwać żądań.

**Klucz**: adresy URL  
**Typ**: *ciąg*  
**Wartość domyślna**: http://localhost:5000  
**Ustaw przy użyciu**:`UseUrls`  
**Zmienna środowiskowa**:`ASPNETCORE_URLS`

Ustaw na rozdzieloną średnikami (;) Lista prefiksów adresów URL, do których serwer powinien reagować. Na przykład `http://localhost:123`. Użyj "\*", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu ( `http://*:5000`na przykład). Protokół (`http://` lub `https://`) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się między serwerami.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Limit czasu zamykania

Określa czas oczekiwania na zamknięcie hosta sieci Web.

**Klucz**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Wartość domyślna**: 5  
**Ustaw przy użyciu**:`UseShutdownTimeout`  
**Zmienna środowiskowa**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Chociaż klucz akceptuje *int* z `UseSetting` `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`(na przykład), Metoda rozszerzenia [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) przyjmuje wartość [TimeSpan](/dotnet/api/system.timespan).

Podczas okresu przekroczenia limitu czasu hostowanie:

* Wyzwala [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Próbuje zatrzymać usługi hostowane, rejestrując błędy dla usług, których nie można zatrzymać.

Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Zestaw startowy

Określa zestaw, który ma być wyszukiwany dla `Startup` klasy.

**Klucz**: startupAssembly  
**Typ**: *ciąg*  
**Wartość domyślna**: Zestaw aplikacji  
**Ustaw przy użyciu**:`UseStartup`  
**Zmienna środowiskowa**:`ASPNETCORE_STARTUPASSEMBLY`

Można odwołać się do`string`zestawu według nazwy (`TStartup`) lub typu (). Jeśli wywoływana `UseStartup` jest wiele metod, pierwszeństwo ma Ostatnia.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Katalog główny sieci Web

Ustawia ścieżkę względną do statycznych zasobów aplikacji.

**Klucz**: Webroot  
**Typ**: *ciąg*  
**Wartość domyślna**: Jeśli nie zostanie określony, wartością domyślną jest "(katalog zawartości)/wwwroot", jeśli ścieżka istnieje. Jeśli ścieżka nie istnieje, zostanie użyty dostawca plików No-op.  
**Ustaw przy użyciu**:`UseWebRoot`  
**Zmienna środowiskowa**:`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a>Zastąp konfigurację

Skonfiguruj hosta sieci Web za pomocą [konfiguracji](xref:fundamentals/configuration/index) . W poniższym przykładzie Konfiguracja hosta jest opcjonalnie określona w pliku *HostSettings. JSON* . Wszystkie konfiguracje załadowane z pliku *HostSettings. JSON* mogą zostać zastąpione przez argumenty wiersza polecenia. Utworzona konfiguracja (w programie `config`) służy do konfigurowania hosta z [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder`Konfiguracja jest dodawana do konfiguracji aplikacji, ale&mdash; `ConfigureAppConfiguration` nie ma ona wpływu `IWebHostBuilder` na konfigurację.

Zastępowanie konfiguracji podanej przy `UseUrls` użyciu pliku *HostSettings. JSON* należy najpierw skonfigurować argument wiersza polecenia Second:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> Metoda rozszerzenia [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) nie jest obecnie w stanie analizowania sekcji konfiguracyjnej zwróconej `GetSection` przez `.UseConfiguration(Configuration.GetSection("section"))`(na przykład. Metoda filtruje klucze konfiguracji do żądanej sekcji `section:urls`, ale pozostawia nazwę sekcji w kluczach (na przykład, `section:environment`). `GetSection` Metoda oczekuje, że klucze są zgodne z `WebHostBuilder` `urls`kluczami (na przykład, `environment`). `UseConfiguration` Obecność nazwy sekcji w kluczach uniemożliwia wartości sekcji od skonfigurowania hosta. Ten problem zostanie rozwiązany w kolejnej wersji. Aby uzyskać więcej informacji i obejść, zobacz [przekazywanie sekcji konfiguracji do WebHostBuilder. UseConfiguration używa pełnych kluczy](https://github.com/aspnet/Hosting/issues/839).
>
> `UseConfiguration`tylko kopiuje klucze z dostarczonej `IConfiguration` do konfiguracji konstruktora hostów. W związku z `reloadOnChange: true` tym nie ma żadnego wpływu na ustawienia plików JSON, ini i XML.

Aby określić hosta na określonym adresie URL, wymagana wartość może zostać przeniesiona z wiersza polecenia podczas wykonywania [przebiegu dotnet](/dotnet/core/tools/dotnet-run). Argument wiersza polecenia zastępuje `urls` wartość z pliku *HostSettings. JSON* , a serwer nasłuchuje na porcie 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Zarządzanie hostem

**Uruchom**

`Run` Metoda uruchamia aplikację sieci Web i blokuje wątek wywołujący do momentu wyłączenia hosta:

```csharp
host.Run();
```

**Start**

Uruchom hosta w sposób nieblokowany przez wywołanie jego `Start` metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Jeśli lista adresów URL jest przenoszona do `Start` metody, nasłuchuje na określonych adresach URL:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

Aplikacja może inicjować i uruchamiać nowy host przy użyciu wstępnie skonfigurowanych ustawień domyślnych `CreateDefaultBuilder` przy użyciu statycznej metody wygodnej. Metody te służą do uruchamiania serwera bez danych wyjściowych konsoli i [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) poczekać na przerwanie (Ctrl-C/SIGINT lub SIGTERM):

**Rozpocznij (aplikacja RequestDelegate)**

Zacznij od `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!" `WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**Rozpocznij (adres URL ciągu, aplikacja RequestDelegate)**

Zacznij od adresu URL i `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **Start (RequestDelegate App)** , z wyjątkiem tego, że aplikacja `http://localhost:8080`reaguje na.

**Start (Action&lt;IRouteBuilder&gt; routeBuilder)**

Użyj wystąpienia programu `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), aby użyć oprogramowania pośredniczącego routingu:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Użyj poniższego żądania przeglądarki z przykładem:

| Request                                    | Odpowiedź                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Witaj, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos Dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Zgłasza wyjątek z ciągiem "ooops!" |
| `http://localhost:5000/throw`              | Zgłasza wyjątek z ciągiem "zapomniano" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Jan!                            |
| `http://localhost:5000`                    | Cześć ludzie!                             |

`WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**Rozpocznij (ciąg URL: Action&lt;IRouteBuilder&gt; routeBuilder)**

Użyj adresu URL i wystąpienia `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **początkowy (Akcja&lt;IRouteBuilder&gt; routeBuilder)** , z wyjątkiem tego, że aplikacja `http://localhost:8080`reaguje na.

**StartWith (Akcja&lt;IApplicationBuilder&gt; aplikacji)**

Podaj delegata, aby skonfigurować `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!" `WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**StartWith (adres URL ciągu,&lt;Akcja&gt; IApplicationBuilder aplikacji)**

Podaj adres URL i delegata, aby skonfigurować `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **StartWith (Akcja&lt;IApplicationBuilder&gt; aplikacji)** , z wyjątkiem tego, że aplikacja `http://localhost:8080`reaguje na.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment, interfejs

[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu aplikacji sieci Web. Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , `IHostingEnvironment` Aby uzyskać w celu użycia jej właściwości i metod rozszerzających:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska. Alternatywnie można wstrzyknąć `IHostingEnvironment` `Startup` do konstruktora do użycia w `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> `IsDevelopment` Oprócz metody rozszerzenia, `IHostingEnvironment` oferty `IsStaging`, `IsProduction`i metody.`IsEnvironment(string environmentName)` Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

Usługę można również wstrzyknąć bezpośrednio `Configure` do metody w celu skonfigurowania potoku przetwarzania: `IHostingEnvironment`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime, interfejs

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umożliwia działanie po uruchomieniu i zamknięciu. Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania    | Wyzwalane, gdy&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Host został w pełni uruchomiony. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Host kończy bezpieczne zamknięcie. Wszystkie żądania powinny być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Host wykonuje bezpieczne zamknięcie. Żądania mogą nadal być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żądania zakończenia aplikacji. Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku wywołania `Shutdown` metody klasy:

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

## <a name="scope-validation"></a>Weryfikacja zakresu

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` Jeśli środowisko aplikacji jest opracowywane.

Gdy `ValidateScopes` jest ustawiona na `true`, domyślny dostawca usług sprawdza, czy:

* Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.
* Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.

Główny dostawca usług jest tworzony, gdy wywoływana jest [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) . Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który go utworzył. Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony. Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.

Aby zawsze weryfikować zakresy, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na konstruktorze hosta:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
