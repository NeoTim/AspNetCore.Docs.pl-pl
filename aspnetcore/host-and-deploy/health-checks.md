---
title: Kontrole kondycji w ASP.NET Core
author: guardrex
description: Dowiedz się, jak skonfigurować Sprawdzanie kondycji infrastruktury ASP.NET Core, na przykład aplikacje i bazy danych.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/13/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 4a4606a58178018f0d71d467d4c8b6c9982c09dc
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115996"
---
# <a name="health-checks-in-aspnet-core"></a>Kontrole kondycji w ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.
* Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.
* Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany. System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.

Pakiet [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) jest wywoływany niejawnie dla aplikacji ASP.NET Core. Aby przeprowadzić kontrolę kondycji przy użyciu Entity Framework Core, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .

Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy. Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu `DbContext`EF Core. Aby poznać scenariusze baz danych, przykładową aplikację:

* Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .
* W pliku projektu znajdują się następujące odwołania do pakietów:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,jest wystarczająca do odnajdywania stanu aplikacji.

Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję. Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu. Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi zapisuje stan (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że jest to [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżona](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) [kondycja lub HealthStatus.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure`.

W przykładowej aplikacji punkt końcowy sprawdzania kondycji jest tworzony w `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Utwórz kontrole kondycji

Kontrole kondycji są tworzone przez implementację interfejsu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> zwraca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, który wskazuje na kondycję jako `Healthy`, `Degraded`lub `Unhealthy`. Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> może również zwracać opcjonalne pary klucz-wartość.

W poniższej klasie `ExampleHealthCheck` zademonstrowano układ kontroli kondycji. Logika kontroli kondycji jest umieszczana w metodzie `CheckHealthAsync`. Poniższy przykład ustawia zmienną fikcyjną `healthCheckResultHealthy`, aby `true`. Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Rejestrowanie usług sprawdzania kondycji

Typ `ExampleHealthCheck` jest dodawany do usług sprawdzania kondycji z <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie ustawia stan awarii (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) do raportowania, gdy Sprawdzanie kondycji zgłosi błąd. Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) . To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> może również wykonać funkcję lambda. W poniższym przykładzie nazwa sprawdzania kondycji jest określona jako `Example` i sprawdzanie zawsze zwraca prawidłowy stan:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*>, aby przekazać argumenty do implementacji sprawdzania kondycji. W poniższym przykładzie `TestHealthCheckWithArgs` akceptuje liczbę całkowitą i ciąg, który ma być używany, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> jest wywoływana:

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs` jest zarejestrowany przez wywołanie `AddTypeActivatedCheck` z liczbą całkowitą i ciągiem przekazaną do implementacji:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Użyj routingu kontroli kondycji

W `Startup.Configure`Wywołaj `MapHealthChecks` na konstruktorze punktów końcowych z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Wymagaj hosta

Wywołaj `RequireHost`, aby określić co najmniej jeden dozwolony Host dla punktu końcowego sprawdzania kondycji. Hosty powinny być w formacie Unicode, a nie formacie Punycode i mogą zawierać port. Jeśli kolekcja nie zostanie podana, każdy host zostanie zaakceptowany.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

### <a name="require-authorization"></a>Wymagaj autoryzacji

Wywołaj `RequireAuthorization`, aby uruchomić oprogramowanie pośredniczące autoryzacji w punkcie końcowym żądania sprawdzania kondycji. Przeciążenie `RequireAuthorization` akceptuje co najmniej jedną zasadę autoryzacji. Jeśli nie podano zasad, zostanie użyta domyślna zasada autoryzacji.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Włączanie żądań Cross-Origin (CORS)

Mimo że sprawdzanie kondycji jest wykonywane ręcznie z przeglądarki nie jest typowym scenariuszem użycia, oprogramowanie do obsługi mechanizmu CORS można włączyć, wywołując `RequireCors` w punktach końcowych sprawdzania kondycji. Przeciążenie `RequireCors` akceptuje delegata konstruktora zasad CORS (`CorsPolicyBuilder`) lub nazwę zasady. Jeśli nie podano zasad, zostanie użyta domyślna zasada CORS. Aby uzyskać więcej informacji, zobacz <xref:security/cors>.

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> zapewnić możliwość dostosowania zachowania kontroli kondycji:

* [Filtrowanie kontroli kondycji](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomiń nagłówki pamięci podręcznej](#suppress-cache-headers)
* [Dostosuj dane wyjściowe](#customize-output)

### <a name="filter-health-checks"></a>Filtrowanie kontroli kondycji

Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną do <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji. W poniższym przykładzie Sprawdzanie kondycji `Bar` jest odfiltrowane przez tag (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> kontroli kondycji pasuje do `foo_tag` lub `baz_tag`:

W `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

W `Startup.Configure``Predicate` filtruje kontrolę kondycji "bar". Tylko Foo i baz Execute.:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>Dostosowywanie kodu stanu HTTP

Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, aby dostosować mapowanie stanu kondycji do kodów stanu HTTP. Następujące przypisania <xref:Microsoft.AspNetCore.Http.StatusCodes> są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodów stanu, aby spełniały Twoje wymagania.

W `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Pomiń nagłówki pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> kontroluje, czy program do sprawdzania kondycji dodaje do odpowiedzi sondy nagłówki HTTP, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje nagłówki `Cache-Control`, `Expires`i `Pragma`, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `true`, oprogramowanie pośredniczące nie zmodyfikuje nagłówków pamięci podręcznej odpowiedzi.

W `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Dostosuj dane wyjściowe

Opcja <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Pobiera lub ustawia delegata używany do zapisywania odpowiedzi.

W `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujący delegat niestandardowy `WriteResponse`, wyprowadza niestandardową odpowiedź JSON:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. Możesz dowolnie dostosowywać `JObject` w powyższym przykładzie, aby zaspokoić Twoje potrzeby.

## <a name="database-probe"></a>Sonda bazy danych

Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.

Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych. `AspNetCore.Diagnostics.HealthChecks` wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko. Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności. W większości przypadków uruchomienie zapytania testowego nie jest konieczne. Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie. Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1`.

Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji. Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample`:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Przykładowa aplikacja wywołuje metodę `AddSqlServer` przy użyciu parametrów połączenia bazy danych (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="entity-framework-core-dbcontext-probe"></a>Badanie Entity Framework Core DbContext

`DbContext` sprawdza, czy aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext`. Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:

* Użyj [Entity Framework (EF) Core](/ef/core/).
* Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` rejestruje Sprawdzanie kondycji `DbContext`. `DbContext` jest dostarczany jako `TContext` do metody. Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.

Domyślnie:

* `DbContextHealthCheck` wywołuje EF Core metodę `CanConnectAsync`. Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu przeciążeń metody `AddDbContextCheck`.
* Nazwa sprawdzania kondycji jest nazwą typu `TContext`.

W przykładowej aplikacji `AppDbContext` są udostępniane `AddDbContextCheck` i rejestrowane jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić scenariusz sondowania `DbContext` za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do punktu końcowego `/health` w przeglądarce. Baza danych i `AppDbContext` nie istnieją, dlatego aplikacja oferuje następujące odpowiedzi:

```
Unhealthy
```

Wyzwól przykładową aplikację, aby utworzyć bazę danych. Utwórz żądanie `/createdatabase`. Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do punktu końcowego `/health`. Istnieje baza danych i kontekst, aby aplikacja odpowiadała:

```
Healthy
```

Wyzwól przykładową aplikację, aby usunąć bazę danych. Utwórz żądanie `/deletedatabase`. Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do punktu końcowego `/health`. Aplikacja zawiera odpowiedź w złej kondycji:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i na żywo

W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:

* Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań. Jest to stan *gotowości*aplikacji.
* Aplikacja działa i odpowiada na żądania. Ten stan jest *aktywny*.

Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne. Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań. Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążeń aplikacji przy użyciu kosztownego zestawu kontroli gotowości&mdash;dalsze sprawdzenia wymagają tylko sprawdzenia dostępności.

Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` uwidacznia właściwość, `StartupTaskCompleted`, że usługa hostowana może zostać ustawiona na `true` po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*). Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiona na `true`:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest zarejestrowana w <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w `Startup.ConfigureServices` wraz z usługą hostowaną. Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure`. W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:

* `/health/ready` kontroli gotowości. Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą tagu `ready`.
* `/health/live` do sprawdzenia na żywo. Sprawdzenie stanu na żywo filtruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` w [predykacie HealthCheckOptions.](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, zobacz [Sprawdzanie kondycji filtru](#filter-health-checks))

W poniższym przykładowym kodzie:

* Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".
* `Predicate` wyklucza wszystkie testy i zwróci 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund. Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund. Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.

Ten przykład tworzy również Wydawca sprawdzania kondycji (implementacja<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/). W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych. Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się. Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.

Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi

Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.

`MemoryHealthCheck` zgłasza stan obniżonej wydajności, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obejmuje informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Zamiast włączać kontrolę kondycji przez przekazanie jej do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego. Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.

W przykładowej aplikacji (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure`. Do właściwości `ResponseWriter` jest dostarczany delegat `WriteResponse`, który będzie wyprowadzał niestandardową odpowiedź JSON, gdy jest wykonywane sprawdzanie kondycji:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Metoda `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i generuje dane wyjściowe JSON dla odpowiedzi kontroli kondycji:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="filter-by-port"></a>Filtruj według portu

Wywołaj `RequireHost` na `MapHealthChecks` za pomocą wzorca adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .

Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure`.

W przykładowej aplikacji wywołanie `RequireHost` w punkcie końcowym w `Startup.Configure` określa port zarządzania z konfiguracji:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Punkty końcowe są tworzone w przykładowej aplikacji w `Startup.Configure`. W poniższym przykładowym kodzie:

* Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".
* `Predicate` wyklucza wszystkie testy i zwróci 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Można uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji przez ustawienie portu zarządzania jawnie w kodzie. W *program.cs* , w którym utworzono <xref:Microsoft.Extensions.Hosting.HostBuilder>, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> i Podaj punkt końcowy portu zarządzania aplikacji. W `Configure` *ManagementPortStartup.cs*należy określić port zarządzania z `RequireHost`:
>
> *Program.cs*:
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Dystrybucja biblioteki kontroli kondycji

Aby rozpowszechnić kontrolę kondycji jako bibliotekę:

1. Napisz kontrolę kondycji implementującą interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako autonomiczną klasę. Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.

   W logice kontroli kondycji `CheckHealthAsync`:

   * `data1` i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException` jest obsługiwany.

   Gdy wystąpi <xref:System.AccessViolationException>, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostanie zwrócona z <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Napisz metodę rozszerzenia z parametrami, które zużywają aplikację, w metodzie `Startup.Configure`. W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji. Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia. W poniższym przykładzie obiekt wywołujący określa opcjonalne:

   * Nazwa sprawdzania kondycji (`name`). Jeśli `null`, `example_health_check` jest używany.
   * punkt danych ciągu dla kontroli kondycji (`data1`).
   * punkt danych liczb całkowitych dla kontroli kondycji (`data2`). Jeśli `null`, `1` jest używany.
   * stan niepowodzenia (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Wartość domyślna to `null`. Jeśli `null`, [HealthStatus. zła kondycja](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest raportowany pod kątem stanu błędu.
   * Tagi (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Wydawca kontroli kondycji

Gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zostanie dodana do kontenera usługi, system sprawdzania kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` z wynikiem. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> można ustawić:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; początkowe opóźnienie zastosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; czasie wykonywania <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; limit czasu wykonywania kontroli kondycji dla wszystkich wystąpień <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan>, aby wykonać bez limitu czasu. Wartość domyślna to 30 sekund.

W przykładowej aplikacji `ReadinessPublisher` jest implementacją <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:

* Informacje (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), jeśli stan sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Błąd (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W przykładzie `LivenessProbeStartup` przykładowej aplikacji sprawdzanie gotowości `StartupHostedService` ma dwa drugie opóźnienie uruchamiania i sprawdza co 30 sekund. Aby uaktywnić implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, przykład rejestruje `ReadinessPublisher` jako usługę pojedynczą w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="restrict-health-checks-with-mapwhen"></a>Ogranicz kontrolę kondycji za pomocą MapWhen

Użyj <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.

W poniższym przykładzie `MapWhen` rozgałęziać potok żądania, aby aktywować program do sprawdzania kondycji w przypadku odebrania żądania GET dla punktu końcowego `api/HealthCheck`:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.
* Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.
* Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany. System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.

Odwołującego się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu w pakiecie [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .

Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy. Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu `DbContext`EF Core. Aby poznać scenariusze baz danych, przykładową aplikację:

* Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .
* W pliku projektu znajdują się następujące odwołania do pakietów:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,jest wystarczająca do odnajdywania stanu aplikacji.

Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję. Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu. Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi zapisuje stan (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że jest to [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżona](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) [kondycja lub HealthStatus.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Dodaj punkt końcowy dla oprogramowania do sprawdzania kondycji z <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> w potoku przetwarzania żądań `Startup.Configure`.

W przykładowej aplikacji punkt końcowy sprawdzania kondycji jest tworzony w `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Utwórz kontrole kondycji

Kontrole kondycji są tworzone przez implementację interfejsu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> zwraca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, który wskazuje na kondycję jako `Healthy`, `Degraded`lub `Unhealthy`. Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> może również zwracać opcjonalne pary klucz-wartość.

### <a name="example-health-check"></a>Przykładowe Sprawdzenie kondycji

W poniższej klasie `ExampleHealthCheck` zademonstrowano układ kontroli kondycji. Logika kontroli kondycji jest umieszczana w metodzie `CheckHealthAsync`. Poniższy przykład ustawia zmienną fikcyjną `healthCheckResultHealthy`, aby `true`. Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Rejestrowanie usług sprawdzania kondycji

Typ `ExampleHealthCheck` jest dodawany do usług sprawdzania kondycji w usłudze `Startup.ConfigureServices` z <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie ustawia stan awarii (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) do raportowania, gdy Sprawdzanie kondycji zgłosi błąd. Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) . To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> może również wykonać funkcję lambda. W poniższym przykładzie `Startup.ConfigureServices` nazwa sprawdzania kondycji jest określana jako `Example` i sprawdzanie zawsze zwraca prawidłowy stan:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Używanie oprogramowania do sprawdzania kondycji

W `Startup.Configure`Wywołaj <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> w potoku przetwarzania z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseHealthChecks("/health");
```

Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, aby ustawić port (opisany dokładniej w sekcji [Filtruj według portu](#filter-by-port) ):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> zapewnić możliwość dostosowania zachowania kontroli kondycji:

* [Filtrowanie kontroli kondycji](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomiń nagłówki pamięci podręcznej](#suppress-cache-headers)
* [Dostosuj dane wyjściowe](#customize-output)

### <a name="filter-health-checks"></a>Filtrowanie kontroli kondycji

Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną do <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji. W poniższym przykładzie Sprawdzanie kondycji `Bar` jest odfiltrowane przez tag (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> kontroli kondycji pasuje do `foo_tag` lub `baz_tag`:

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Dostosowywanie kodu stanu HTTP

Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, aby dostosować mapowanie stanu kondycji do kodów stanu HTTP. Następujące przypisania <xref:Microsoft.AspNetCore.Http.StatusCodes> są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodów stanu, aby spełniały Twoje wymagania.

W `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Pomiń nagłówki pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> kontroluje, czy program do sprawdzania kondycji dodaje do odpowiedzi sondy nagłówki HTTP, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje nagłówki `Cache-Control`, `Expires`i `Pragma`, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `true`, oprogramowanie pośredniczące nie zmodyfikuje nagłówków pamięci podręcznej odpowiedzi.

W `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Dostosuj dane wyjściowe

Opcja <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Pobiera lub ustawia delegata używany do zapisywania odpowiedzi. Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

W `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujący delegat niestandardowy `WriteResponse`, wyprowadza niestandardową odpowiedź JSON:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. Możesz dowolnie dostosowywać `JObject` w powyższym przykładzie, aby zaspokoić Twoje potrzeby.

## <a name="database-probe"></a>Sonda bazy danych

Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.

Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych. `AspNetCore.Diagnostics.HealthChecks` wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko. Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności. W większości przypadków uruchomienie zapytania testowego nie jest konieczne. Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie. Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1`.

Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji. Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Przykładowa aplikacja wywołuje metodę `AddSqlServer` przy użyciu parametrów połączenia bazy danych (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Wywołaj kontrolę kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w `Startup.Configure`:

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="entity-framework-core-dbcontext-probe"></a>Badanie Entity Framework Core DbContext

`DbContext` sprawdza, czy aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext`. Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:

* Użyj [Entity Framework (EF) Core](/ef/core/).
* Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` rejestruje Sprawdzanie kondycji `DbContext`. `DbContext` jest dostarczany jako `TContext` do metody. Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.

Domyślnie:

* `DbContextHealthCheck` wywołuje EF Core metodę `CanConnectAsync`. Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu przeciążeń metody `AddDbContextCheck`.
* Nazwa sprawdzania kondycji jest nazwą typu `TContext`.

W przykładowej aplikacji `AppDbContext` są udostępniane `AddDbContextCheck` i rejestrowane jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

W przykładowej aplikacji `UseHealthChecks` dodaje oprogramowanie do sprawdzania kondycji w programie `Startup.Configure`.

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić scenariusz sondowania `DbContext` za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do punktu końcowego `/health` w przeglądarce. Baza danych i `AppDbContext` nie istnieją, dlatego aplikacja oferuje następujące odpowiedzi:

```
Unhealthy
```

Wyzwól przykładową aplikację, aby utworzyć bazę danych. Utwórz żądanie `/createdatabase`. Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do punktu końcowego `/health`. Istnieje baza danych i kontekst, aby aplikacja odpowiadała:

```
Healthy
```

Wyzwól przykładową aplikację, aby usunąć bazę danych. Utwórz żądanie `/deletedatabase`. Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do punktu końcowego `/health`. Aplikacja zawiera odpowiedź w złej kondycji:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i na żywo

W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:

* Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań. Jest to stan *gotowości*aplikacji.
* Aplikacja działa i odpowiada na żądania. Ten stan jest *aktywny*.

Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne. Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań. Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążeń aplikacji przy użyciu kosztownego zestawu kontroli gotowości&mdash;dalsze sprawdzenia wymagają tylko sprawdzenia dostępności.

Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` uwidacznia właściwość, `StartupTaskCompleted`, że usługa hostowana może zostać ustawiona na `true` po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*). Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiona na `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest zarejestrowana w <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w `Startup.ConfigureServices` wraz z usługą hostowaną. Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w `Startup.Configure`. W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w `/health/ready` na potrzeby sprawdzania gotowości i `/health/live` na potrzeby kontroli stanu na żywo. Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą tagu `ready`. Sprawdzenie stanu na żywo filtruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` do [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, zobacz [Sprawdzanie kondycji filtru](#filter-health-checks)):

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund. Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund. Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.

Ten przykład tworzy również Wydawca sprawdzania kondycji (implementacja<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/). W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych. Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się. Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.

Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi

Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.

`MemoryHealthCheck` zgłasza stan złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obejmuje informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Zamiast włączać kontrolę kondycji przez przekazanie jej do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego. Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.

W przykładowej aplikacji (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w `Startup.Configure`. Do właściwości `ResponseWriter` jest dostarczany delegat `WriteResponse`, który będzie wyprowadzał niestandardową odpowiedź JSON, gdy jest wykonywane sprawdzanie kondycji:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

Metoda `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i generuje dane wyjściowe JSON dla odpowiedzi kontroli kondycji:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="filter-by-port"></a>Filtruj według portu

Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza liczbę żądań sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .

Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Rejestrowanie usług sprawdzania kondycji za pomocą <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`. Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określa port zarządzania (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Możesz uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie. W *program.cs* , w którym utworzono <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> i podaj normalny punkt końcowy odpowiedzi aplikacji oraz punkt końcowy portu zarządzania. W *ManagementPortStartup.cs* , gdzie jest wywoływana <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, określ port zarządzania jawnie.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Dystrybucja biblioteki kontroli kondycji

Aby rozpowszechnić kontrolę kondycji jako bibliotekę:

1. Napisz kontrolę kondycji implementującą interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako autonomiczną klasę. Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.

   W logice kontroli kondycji `CheckHealthAsync`:

   * `data1` i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException` jest obsługiwany.

   Gdy wystąpi <xref:System.AccessViolationException>, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostanie zwrócona z <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Napisz metodę rozszerzenia z parametrami, które zużywają aplikację, w metodzie `Startup.Configure`. W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji. Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia. W poniższym przykładzie obiekt wywołujący określa opcjonalne:

   * Nazwa sprawdzania kondycji (`name`). Jeśli `null`, `example_health_check` jest używany.
   * punkt danych ciągu dla kontroli kondycji (`data1`).
   * punkt danych liczb całkowitych dla kontroli kondycji (`data2`). Jeśli `null`, `1` jest używany.
   * stan niepowodzenia (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Wartość domyślna to `null`. Jeśli `null`, [HealthStatus. zła kondycja](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest raportowany pod kątem stanu błędu.
   * Tagi (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Wydawca kontroli kondycji

Gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zostanie dodana do kontenera usługi, system sprawdzania kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` z wynikiem. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> można ustawić:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; początkowe opóźnienie zastosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; czasie wykonywania <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; limit czasu wykonywania kontroli kondycji dla wszystkich wystąpień <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan>, aby wykonać bez limitu czasu. Wartość domyślna to 30 sekund.

> [!WARNING]
> W wersji 2,2 ASP.NET Core ustawienie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nie jest honorowane przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>; ustawia wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Ten problem został rozwiązany w ASP.NET Core 3,0.

W przykładowej aplikacji `ReadinessPublisher` jest implementacją <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia jako:

* Informacje (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), jeśli stan sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Błąd (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W przykładzie `LivenessProbeStartup` przykładowej aplikacji sprawdzanie gotowości `StartupHostedService` ma dwa drugie opóźnienie uruchamiania i sprawdza co 30 sekund. Aby uaktywnić implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, przykład rejestruje `ReadinessPublisher` jako usługę pojedynczą w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Następujące obejście umożliwia dodanie wystąpienia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> do kontenera usługi, gdy co najmniej jedna inna usługa hostowana została już dodana do aplikacji. To obejście nie będzie wymagane w ASP.NET Core 3,0.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="restrict-health-checks-with-mapwhen"></a>Ogranicz kontrolę kondycji za pomocą MapWhen

Użyj <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.

W poniższym przykładzie `MapWhen` rozgałęziać potok żądania, aby aktywować program do sprawdzania kondycji w przypadku odebrania żądania GET dla punktu końcowego `api/HealthCheck`:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
