---
title: Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core
author: stevejgordon
description: Dowiedz się więcej o używaniu interfejsu IHttpClientFactory do zarządzania wystąpieniami logicznych HttpClient w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/01/2019
uid: fundamentals/http-requests
ms.openlocfilehash: bcf2a2eaf6910222d274c38bac343c92fab9cb5b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739536"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)

Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji. <xref:System.Net.Http.IHttpClientFactory> Oferuje następujące korzyści:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/). Domyślny klient można zarejestrować do innych celów.
* Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów `HttpClient` obsługi w programie i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.
* Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` podstawowych wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za `ILogger`pośrednictwem programu) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a>Wymagania wstępne

Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) . Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają `Microsoft.Extensions.Http` już pakiet.

::: moniker-end

## <a name="consumption-patterns"></a>Wzorce zużycia

W aplikacji można używać `IHttpClientFactory` kilku sposobów:

* [Podstawowe użycie](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Wpisane komputery klienckie](#typed-clients)
* [Wygenerowane klienci](#generated-clients)

Żadna z nich nie jest ściśle wyższa od siebie. Najlepsze podejście zależy od ograniczeń aplikacji.

### <a name="basic-usage"></a>Podstawowe użycie

Można zarejestrować `IServiceCollection`, `AddHttpClient` wywołując metodę rozszerzenia `Startup.ConfigureServices` na, wewnątrz metody. `IHttpClientFactory`

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection). Można go użyć do `HttpClient` utworzenia wystąpienia: `IHttpClientFactory`

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji. Nie ma to wpływu na sposób `HttpClient` użycia. W miejscach, `HttpClient` w których są obecnie tworzone wystąpienia, Zastąp te wystąpienia <xref:System.Net.Http.IHttpClientFactory.CreateClient*>wywołaniem.

### <a name="named-clients"></a>Nazwani klienci

Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient`, z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**. Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*. Ten klient ma zainstalowaną domyślną&mdash;konfigurację, a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.

Przy każdym wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji. `CreateClient`

Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem `CreateClient`do. Określ nazwę klienta, który ma zostać utworzony:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W powyższym kodzie żądanie nie musi określać nazwy hosta. Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Wpisane komputery klienckie

Wpisane komputery klienckie:

* Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.
* Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.
* Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient`. Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.
* Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.

Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta. `HttpClient` Obiekt jest ujawniany jako właściwość publiczna. Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, `HttpClient` które uwidaczniają funkcjonalność. `GetAspNetDocsIssues` Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.

Aby zarejestrować klienta z określonym typem, można <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> użyć generycznej metody rozszerzenia w `Startup.ConfigureServices`programie, określając klasę klienta z określonym typem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typ klienta jest rejestrowany jako przejściowy z DI. Określony klient może zostać dodany i wykorzystany bezpośrednio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas `Startup.ConfigureServices`rejestracji w, a nie w konstruktorze określonego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta. Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne. Cały dostęp do wykonywania wywołań zewnętrznych odbywa się `GetRepos` za pomocą metody.

### <a name="generated-clients"></a>Wygenerowane klienci

`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit). REFIT to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana dynamicznie przez `RestService`, przy użyciu polecenia `HttpClient` , aby utworzyć zewnętrzne wywołania http.

Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzące

`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP. `IHttpClientFactory` Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta. Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym. Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.

Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z <xref:System.Net.Http.DelegatingHandler>elementu. Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod definiuje procedurę obsługi podstawowej. Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu. Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.

Podczas rejestracji do konfiguracji `HttpClient`można dodać co najmniej jeden program obsługi. To zadanie jest realizowane za pośrednictwem metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>na.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di. `IHttpClientFactory` Tworzy oddzielny zakres di dla każdej procedury obsługi. Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu. Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.

Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di. Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem. Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:

* Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.
* Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.

Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.

::: moniker-end

Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane. Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:

* Przekaż dane do procedury obsługi przy `HttpRequestMessage.Properties`użyciu polecenia.
* Użyj `IHttpContextAccessor` , aby uzyskać dostęp do bieżącego żądania.
* Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na Polly

`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET. Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.

Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami. Rozszerzenia Polly:

* Obsługa dodawania programów obsługi opartych na Pollych do klientów.
* Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.

### <a name="handle-transient-faults"></a>Obsługa błędów przejściowych

Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe. Dołączono wygodną metodę `AddTransientHttpErrorPolicy` rozszerzenia, która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych. Zasady skonfigurowane przy użyciu tego uchwytu `HttpRequestException`metody rozszerzenia, odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.

Rozszerzenie może być używane w `Startup.ConfigureServices`. `AddTransientHttpErrorPolicy` Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady. Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne Wybieranie zasad

Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly. Jedno takie rozszerzenie ma `AddPolicyHandler`wiele przeciążeń. Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu. Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodaj wiele programów obsługi Polly

Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie dodawane są dwa programy obsługi. Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania. Nieudane żądania są ponawiane do trzech razy. Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za `PolicyRegistry`pomocą. Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu `ServiceCollection`do. Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.

Więcej informacji na `IHttpClientFactory` temat integracji z programem i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient i zarządzanie okresem istnienia

Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , `IHttpClientFactory`gdy jest wywoływana w. <xref:System.Net.Http.HttpMessageHandler> Istnieje za nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory``HttpMessageHandler` pule wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł. `HttpMessageHandler`

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP. Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń. Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślną można przesłonić na podstawie nazwy klienta. Aby zastąpić ten element, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> Wywołaj `IHttpClientBuilder` metodę zwracaną podczas tworzenia klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Usuwanie klienta nie jest wymagane. Likwidacja anuluje żądania wychodzące i `HttpClient` gwarantuje, że danego wystąpienia nie <xref:System.IDisposable.Dispose*>można użyć po wywołaniu. `IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia. `HttpClient` Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.

Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory`. Ten wzorzec będzie niepotrzebny po `IHttpClientFactory`migracji do programu.

## <a name="logging"></a>Rejestrowanie

Klienci utworzeni `IHttpClientFactory` za pomocą rejestrowania komunikatów dzienników dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu. Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań. Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku. W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.

Rejestrowanie odbywa się również w potoku obsługi żądania. W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`dzienników. W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci. W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.

Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.

Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie HttpMessageHandler

Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.

`IHttpClientBuilder` Jest zwracany podczas dodawania nazwanych lub wpisanych klientów. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzająca może służyć do definiowania delegata. Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Korzystanie z IHttpClientFactory w aplikacji konsolowej

W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .
* `MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient`. `HttpClient`służy do pobierania strony sieci Web.
* `Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
