---
title: Wprowadzenie do uwierzytelniania aplikacji jednostronicowych na ASP.NET Core
author: javiercn
description: Używanie tożsamości z aplikacją jednostronicową hostowaną w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: f58d92634ce1ef6110533d56c40b7520dda90514
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897048"
---
# <a name="authentication-and-authorization-for-spas"></a>Uwierzytelnianie i autoryzacja dla aplikacji jednostronicowych

ASP.NET Core 3,0 lub nowszy oferuje uwierzytelnianie w aplikacjach jednostronicowych (aplikacji jednostronicowych) przy użyciu obsługi autoryzacji interfejsu API. ASP.NET Core tożsamość uwierzytelniania i przechowywania użytkowników jest połączona z [IdentityServer](https://identityserver.io/) w celu zaimplementowania połączenia Open ID Connect.

Dodano parametr uwierzytelniania do szablonów projektów **kątowych** i **reagowania** , które są podobne do parametrów uwierzytelniania w **aplikacji sieci Web (Model-View-Controller)** (MVC) i **aplikacji sieci Web** (Razor Pages) Szablony projektów. Dozwolone wartości parametrów to **none** i **indywidualny**. Szablon projektu **re. js i Redux** nie obsługuje teraz parametru Authentication.

## <a name="create-an-app-with-api-authorization-support"></a>Tworzenie aplikacji z obsługą autoryzacji interfejsu API

Uwierzytelnianie i autoryzacja użytkowników mogą być używane z aplikacji jednostronicowychą kątową i reagują. Otwórz powłokę poleceń i uruchom następujące polecenie:

**Kątowy**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagowanie**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Poprzednie polecenie tworzy aplikację ASP.NET Core z katalogiem *ClientApp* zawierającym spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Ogólny opis składników ASP.NET Core aplikacji

W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania:

### <a name="startup-class"></a>Klasa początkowa

Klasa `Startup` ma następujące dodatki:

* Wewnątrz metody `Startup.ConfigureServices`:
  * Tożsamość z domyślnym interfejsem użytkownika:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer z dodatkową metodą pomocniczą `AddApiAuthorization`, która konfiguruje niektóre domyślne konwencje ASP.NET Core w oparciu o IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za pomocą dodatkowej metody pomocnika `AddIdentityServerJwt`, która konfiguruje aplikację do weryfikowania tokenów JWT utworzonych przez IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Wewnątrz metody `Startup.Configure`:
  * Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:

    ```csharp
    app.UseAuthentication();
    ```

  * Oprogramowanie pośredniczące IdentityServer, które uwidacznia punkty końcowe połączenia Open ID:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Ta metoda pomocnika umożliwia skonfigurowanie IdentityServer do korzystania z naszej obsługiwanej konfiguracji. IdentityServer to zaawansowane i rozszerzalne środowisko do obsługi zagadnień związanych z zabezpieczeniami aplikacji. W tym samym czasie, które ujawnia niezbędną złożoność dla najbardziej typowych scenariuszy. W związku z tym zestaw Konwencji i opcji konfiguracji jest dostarczany do użytkownika, który jest uważany za dobry punkt wyjścia. Gdy uwierzytelnianie będzie wymagało zmiany, pełne możliwości IdentityServer są nadal dostępne, aby dostosować uwierzytelnianie zgodnie z potrzebami.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Ta metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby umożliwić obsługę tożsamości wszystkie żądania kierowane do dowolnej ścieżki podrzędnej w adresie URL tożsamości "/Identity". `JwtBearerHandler` obsługuje wszystkie inne żądania. Ponadto ta metoda rejestruje zasób interfejsu API `<<ApplicationName>>API` przy użyciu usługi IdentityServer z domyślnym zakresem `<<ApplicationName>>API` i konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez IdentityServer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W pliku *Controllers\WeatherForecastController.cs* Zwróć uwagę na atrybut `[Authorize]` stosowany do klasy, która wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji mają być skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest konfigurowany przez `AddIdentityServerJwt` do schematu zasad, który został wymieniony powyżej, a `JwtBearerHandler` skonfigurowany przez taką metodę pomocnika domyślną procedurę obsługi żądań do aplikacja.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W pliku *Data\ApplicationDbContext.cs* należy zauważyć, że ten sam `DbContext` jest używany w tożsamości z wyjątkiem, że rozszerza `ApiAuthorizationDbContext` (bardziej pochodna klasa z `IdentityDbContext`), aby uwzględnić schemat dla IdentityServer.

Aby uzyskać pełną kontrolę nad schematem bazy danych, należy podziedziczyć z jednej z dostępnych `DbContext` klas i skonfigurować kontekst w celu uwzględnienia schematu tożsamości, wywołując `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na metodę `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

W pliku *Controllers\OidcConfigurationController.cs* Zwróć uwagę na punkt końcowy, który jest wstępnie zainicjowany do obsługi parametrów OIDC wymaganych przez klienta.

### <a name="appsettingsjson"></a>appSettings. JSON

W pliku *appSettings. JSON* w katalogu głównym projektu znajduje się nowa sekcja `IdentityServer` opisująca listę skonfigurowanych klientów. W poniższym przykładzie istnieje pojedynczy klient. Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do parametru `ClientId` protokołu OAuth. Profil wskazuje konfigurowany typ aplikacji. Jest on używany wewnętrznie w przypadku Konwencji, które upraszczają proces konfiguracji serwera. Istnieje kilka dostępnych profilów, zgodnie z opisem w sekcji [Profile aplikacji](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>sekcji. Plik Development. JSON

W pliku *appSettings. Plik Development. JSON* w katalogu głównym projektu zawiera sekcję `IdentityServer` opisującą klucz używany do podpisywania tokenów. Podczas wdrażania w środowisku produkcyjnym należy zainicjować i wdrożyć klucz wraz z aplikacją, jak wyjaśniono w sekcji [wdrażanie w środowisku produkcyjnym](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Ogólny opis aplikacji kątowej

Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie kątowym znajduje się w jego własnym module skośnym w katalogu *ClientApp\src\api-Authorization* . Moduł składa się z następujących elementów:

* 3 składniki:
  * *login. Component. TS*: obsługuje przepływ logowania aplikacji.
  * *Wyloguj. składnik. TS*: obsługuje przepływ wylogowania aplikacji.
  * *login-menu. składnik. TS*: element widget wyświetlający jeden z następujących zestawów linków:
    * Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.
    * Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.
* `AuthorizeGuard` ochrony trasy, którą można dodać do tras i wymaga uwierzytelnienia użytkownika przed odwiedzeniem trasy.
* `AuthorizeInterceptor` Interceptor protokołu HTTP, który dołącza token dostępu do wychodzących żądań HTTP przeznaczonych dla interfejsu API w przypadku uwierzytelnienia użytkownika.
* `AuthorizeService` usługi, która obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.
* Moduł kątowy, który definiuje trasy skojarzone z częściami uwierzytelniania aplikacji. Przedstawia on składnik menu logowania, Interceptor, ochronę i usługę do użycia w pozostałej części aplikacji.

## <a name="general-description-of-the-react-app"></a>Ogólny opis aplikacji do reagowania

Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie reagowania znajduje się w katalogu *ClientApp\src\components\api-Authorization* . Składa się z następujących elementów:

* 4 składniki:
  * *Login. js*: obsługuje przepływ logowania aplikacji.
  * *Wyloguj. js*: obsługuje przepływ wylogowania aplikacji.
  * *LoginMenu. js*: element widget wyświetlający jeden z następujących zestawów linków:
    * Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.
    * Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.
  * *AuthorizeRoute. js*: składnik trasy, który wymaga uwierzytelnienia użytkownika przed renderowaniem składnika wskazanego w parametrze `Component`.
* Wyeksportowane wystąpienie `authService` klasy `AuthorizeService`, które obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.

Teraz, gdy widzisz główne składniki rozwiązania, możesz zapoznać się ze szczegółowymi scenariuszami dotyczącymi aplikacji.

## <a name="require-authorization-on-a-new-api"></a>Wymagaj autoryzacji na nowym interfejsie API

Domyślnie system jest skonfigurowany tak, aby z łatwością wymagał autoryzacji dla nowych interfejsów API. W tym celu Utwórz nowy kontroler i Dodaj atrybut `[Authorize]` do klasy Controller lub do dowolnej akcji w ramach kontrolera.

## <a name="customize-the-api-authentication-handler"></a>Dostosowywanie procedury obsługi uwierzytelniania interfejsu API

Aby dostosować konfigurację procedury obsługi JWT interfejsu API, skonfiguruj jej <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> wystąpienie:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Procedura obsługi JWT interfejsu API wywołuje zdarzenia, które umożliwiają kontrolę nad procesem uwierzytelniania przy użyciu `JwtBearerEvents`. Aby zapewnić pomoc techniczną dla autoryzacji interfejsu API, `AddIdentityServerJwt` rejestruje własne procedury obsługi zdarzeń.

Aby dostosować obsługę zdarzenia, zawiń istniejący program obsługi zdarzeń z dodatkową logiką zgodnie z wymaganiami. Na przykład:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

W poprzednim kodzie program obsługi zdarzeń `OnTokenValidated` został zastąpiony implementacją niestandardową. Ta implementacja:

1. Wywołuje oryginalną implementację dostarczoną przez obsługę autoryzacji interfejsu API.
1. Uruchom własną logikę niestandardową.

## <a name="protect-a-client-side-route-angular"></a>Ochrona trasy po stronie klienta (wartość kątowa)

Ochrona trasy po stronie klienta odbywa się przez dodanie funkcji Autoryzuj ochronę do listy osłon do uruchomienia podczas konfigurowania trasy. Przykładowo można zobaczyć, jak trasa `fetch-data` jest konfigurowana w module kątowym aplikacji głównej:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Należy pamiętać, że ochrona trasy nie chroni rzeczywistego punktu końcowego (co nadal wymaga zastosowania atrybutu `[Authorize]`), ale uniemożliwia użytkownikowi przechodzenie do podanej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.

## <a name="authenticate-api-requests-angular"></a>Uwierzytelnianie żądań interfejsu API (kątowy)

Żądania uwierzytelniające do interfejsów API hostowanych razem z aplikacją są wykonywane automatycznie przy użyciu interceptora klienta HTTP zdefiniowanego przez aplikację.

## <a name="protect-a-client-side-route-react"></a>Ochrona trasy po stronie klienta (reagowanie)

Ochrona trasy po stronie klienta przy użyciu składnika `AuthorizeRoute` zamiast składnika zwykłego `Route`. Na przykład Zwróć uwagę na sposób skonfigurowania trasy `fetch-data` w składniku `App`:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrona trasy:

* Nie chroni rzeczywistego punktu końcowego (co nadal wymaga zastosowania atrybutu `[Authorize]`).
* Uniemożliwia użytkownikowi przechodzenie do danej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.

## <a name="authenticate-api-requests-react"></a>Uwierzytelnianie żądań interfejsu API (reagowanie)

Żądania uwierzytelniania przy użyciu reakcji są wykonywane najpierw przez zaimportowanie wystąpienia `authService` z `AuthorizeService`. Token dostępu jest pobierany z `authService` i jest dołączany do żądania, jak pokazano poniżej. W przypadku składników reagujących ta czynność jest wykonywana zwykle w metodzie `componentDidMount` cyklu życia lub w wyniku działania niektórych użytkowników.

### <a name="import-the-authservice-into-your-component"></a>Zaimportuj authService do składnika

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Pobieranie i dołączanie tokenu dostępu do odpowiedzi

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Wdróż w środowisku produkcyjnym

Aby wdrożyć aplikację w środowisku produkcyjnym, należy zainicjować następujące zasoby:

* Baza danych do przechowywania kont użytkowników tożsamości i dotacji IdentityServer.
* Certyfikat produkcyjny do użycia na potrzeby podpisywania tokenów.
  * Nie ma określonych wymagań dotyczących tego certyfikatu; może to być certyfikat z podpisem własnym lub certyfikat obsługiwany przez urząd certyfikacji.
  * Można go wygenerować za poorednictwem standardowych narzędzi, takich jak PowerShell lub OpenSSL.
  * Można go zainstalować w magazynie certyfikatów na maszynach docelowych lub wdrożyć jako plik *PFX* przy użyciu silnego hasła.

### <a name="example-deploy-to-azure-websites"></a>Przykład: wdrażanie w usłudze Azure Websites

W tej sekcji opisano wdrażanie aplikacji w usłudze Azure Websites przy użyciu certyfikatu przechowywanego w magazynie certyfikatów. Aby zmodyfikować aplikację w celu załadowania certyfikatu z magazynu certyfikatów, plan App Service musi znajdować się co najmniej w warstwie Standardowa podczas konfigurowania w późniejszym kroku. W pliku *appSettings. JSON* aplikacji zmodyfikuj sekcję `IdentityServer`, aby uwzględnić szczegóły klucza:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Właściwość Name certyfikatu odpowiada podmiotowi wyróżnionemu dla certyfikatu.
* Lokalizacja magazynu reprezentuje miejsce, z którego ma zostać załadowany certyfikat (`CurrentUser` lub `LocalMachine`).
* Nazwa magazynu reprezentuje nazwę magazynu certyfikatów, w którym przechowywany jest certyfikat. W tym przypadku wskazuje osobisty magazyn użytkownika.

Aby wdrożyć usługę Azure Websites, wdróż aplikację zgodnie z instrukcjami w temacie [wdrażanie aplikacji na platformie Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) w celu utworzenia niezbędnych zasobów platformy Azure i wdrożenia aplikacji w środowisku produkcyjnym.

Po wykonaniu powyższych instrukcji aplikacja zostanie wdrożona na platformie Azure, ale nie jest jeszcze funkcjonalna. Nadal trzeba skonfigurować certyfikat używany przez aplikację. Znajdź odcisk palca certyfikatu, który ma być używany, i postępuj zgodnie z instrukcjami opisanymi w artykule [ładowanie certyfikatów](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Chociaż te kroki zawierają informacje o protokole SSL, w portalu znajduje się sekcja **Certyfikaty prywatne** , w której można przekazać certyfikat z obsługą administracyjną do użycia z aplikacją.

Po wykonaniu tego kroku należy ponownie uruchomić aplikację, która powinna działać.

## <a name="other-configuration-options"></a>Inne opcje konfiguracji

Obsługa usługi API Authorization kompiluje się w oparciu o IdentityServer z zestawem Konwencji, wartościami domyślnymi i ulepszeniami, aby uprościć środowisko aplikacji jednostronicowych. Niezbędna jest pełna moc IdentityServer w tle, jeśli integracja ASP.NET Core nie obejmuje Twojego scenariusza. Pomoc techniczna ASP.NET Core jest skoncentrowana na aplikacjach "pierwszej strony", w których wszystkie aplikacje są tworzone i wdrażane przez naszą organizację. W związku z tym pomoc techniczna nie jest oferowana dla elementów, takich jak zgody lub Federacji. W tych scenariuszach Użyj IdentityServer i postępuj zgodnie z dokumentacją.

### <a name="application-profiles"></a>Profile aplikacji

Profile aplikacji są wstępnie zdefiniowanymi konfiguracjami dla aplikacji, które definiują ich parametry. W tej chwili obsługiwane są następujące profile:

* `IdentityServerSPA`: reprezentuje protokół SPA hostowany obok IdentityServer jako pojedynczą jednostkę.
  * `redirect_uri` domyślnie `/authentication/login-callback`.
  * `post_logout_redirect_uri` domyślnie `/authentication/logout-callback`.
  * Zestaw zakresów zawiera `openid`, `profile`i każdy zakres zdefiniowany dla interfejsów API w aplikacji.
  * Zestaw dozwolonych typów odpowiedzi OIDC to `id_token token` lub każda z nich pojedynczo (`id_token`, `token`).
  * Dozwolony tryb odpowiedzi to `fragment`.
* `SPA`: reprezentuje SPA, który nie jest hostowany z IdentityServer.
  * Zestaw zakresów zawiera `openid`, `profile`i każdy zakres zdefiniowany dla interfejsów API w aplikacji.
  * Zestaw dozwolonych typów odpowiedzi OIDC to `id_token token` lub każda z nich pojedynczo (`id_token`, `token`).
  * Dozwolony tryb odpowiedzi to `fragment`.
* `IdentityServerJwt`: reprezentuje interfejs API, który jest hostowany wraz z IdentityServer.
  * Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.
* `API`: reprezentuje interfejs API, który nie jest obsługiwany przez IdentityServer.
  * Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.

### <a name="configuration-through-appsettings"></a>Konfiguracja za poorednictwem AppSettings

Skonfiguruj aplikacje za pomocą systemu konfiguracji, dodając je do listy `Clients` lub `Resources`.

Skonfiguruj `redirect_uri` i Właściwość `post_logout_redirect_uri` każdego klienta, jak pokazano w następującym przykładzie:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Podczas konfigurowania zasobów można skonfigurować zakresy dla zasobu, jak pokazano poniżej:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Konfiguracja przy użyciu kodu

Klientów i zasoby można również skonfigurować za pomocą kodu przy użyciu przeciążenia `AddApiAuthorization`, które wykonuje akcję konfigurowania opcji.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
