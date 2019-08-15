---
title: ASP.NET Core zarządzanie stanem Blazor
author: guardrex
description: Dowiedz się, jak utrwalać stan w aplikacjach po stronie serwera Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/state-management
ms.openlocfilehash: af040635302fbf2dae8192dcf37d55bfcfedfcec
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030372"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core zarządzanie stanem Blazor

[Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor po stronie serwera jest platformą aplikacji stanowej. W większości przypadków aplikacja utrzymuje ciągłe połączenie z serwerem. Stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. 

Przykłady stanu przechowywanego dla obwodu użytkownika obejmują:

* Renderowany interfejs użytkownika&mdash;hierarchii wystąpień składników i ich najnowszych danych wyjściowych renderowania.
* Wartości wszystkich pól i właściwości w wystąpieniach składników.
* Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.

> [!NOTE]
> Ten artykuł rozwiązuje trwałość stanu w aplikacjach po stronie serwera Blazor. Blazor aplikacje po stronie klienta mogą korzystać z [trwałości stanu po stronie klienta w przeglądarce,](#client-side-in-the-browser) ale wymagają niestandardowych rozwiązań lub pakietów innych firm wykraczających poza zakres tego artykułu.

## <a name="blazor-circuits"></a>Obwody usługi Blazor

Jeśli użytkownik napotyka chwilową utratę połączenia sieciowego, Blazor próbuje ponownie połączyć użytkownika z oryginalnym obwodem, aby mogli nadal korzystać z aplikacji. Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:

* Serwer nie może całkowicie zachować rozłączonego obwodu. Serwer musi zwolnić obwód rozłączony po upływie limitu czasu lub w przypadku, gdy na serwerze znajduje się pamięć.
* W środowiskach wieloserwerowych ze zrównoważonym obciążeniem wszystkie żądania przetwarzania serwera mogą stać się niedostępne w danym momencie. Poszczególne serwery mogą kończyć się niepowodzeniem lub być automatycznie usuwane, gdy nie są już wymagane do obsługi ogólnej liczby żądań. Oryginalny serwer może nie być dostępny, gdy użytkownik próbuje ponownie nawiązać połączenie.
* Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co spowoduje usunięcie wszystkich stanów przechowywanych w pamięci przeglądarki. Na przykład wartości ustawione za poorednictwem wywołań międzyoperacyjnych języka JavaScript są tracone.

Gdy nie można ponownie nawiązać połączenia użytkownika z oryginalnym obwodem, użytkownik otrzymuje nowy obwód z pustym stanem. Jest to równoznaczne z zamknięciem i ponownym otwarciem aplikacji klasycznej.

## <a name="preserve-state-across-circuits"></a>Zachowaj stan między obwodymi

W niektórych scenariuszach jest wymagane zachowanie stanu między obwodami. Aplikacja może zachować ważne dane dla użytkownika, jeśli:

* Serwer sieci Web stał się niedostępny.
* W przeglądarce użytkownika wymuszone jest rozpoczęcie nowego obwodu przy użyciu nowego serwera sieci Web.

Ogólnie rzecz biorąc, utrzymanie stanu między obwodami ma zastosowanie do scenariuszy, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.

Aby zachować stan poza pojedynczym obwodem, *nie należy przechowywać danych w pamięci serwera*. Aplikacja musi przechowywać dane w innej lokalizacji magazynu. Trwałość stanu nie jest&mdash;automatyczna, podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania trwałości danych stanowych.

Trwałość danych jest zwykle wymagana tylko w przypadku stanu wysokiego poziomu, który użytkownicy wystawią nakłady na tworzenie. W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:

* Wieloetapowy formularz &ndash; WebForm, który jest czasochłonny dla użytkownika, aby ponownie wprowadzić dane dla kilku ukończonych kroków procesu wieloetapowego, jeśli ich stan zostanie utracony. Użytkownik utraci stan w tym scenariuszu, jeśli przejdą do formularza wieloetapowego i wrócisz do formularza później.
* Koszyk &ndash; można obsłużyć wszelkie komercyjnie ważne składniki aplikacji, które reprezentują potencjalne przychody. Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.

Zwykle nie jest konieczne zachowywanie stanu łatwego ponownego tworzenia, takiego jak wprowadzona nazwa użytkownika do okna dialogowego logowania, które nie zostało przesłane.

> [!IMPORTANT]
> Aplikacja może utrzymywać tylko *stan aplikacji*. Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania. Składniki i drzewa renderowania nie są generalnie serializowane. Aby zachować coś podobnego do stanu interfejsu użytkownika, na przykład rozwinięte węzły drzewa TreeView, aplikacja musi mieć kod niestandardowy, aby modelować zachowanie jako możliwy do serializacji stan aplikacji.

## <a name="where-to-persist-state"></a>Gdzie będzie trwały stan

Trzy Popularne lokalizacje istnieją dla stanu utrwalania w aplikacji po stronie serwera Blazor. Każde podejście jest najlepiej dostosowane do różnych scenariuszy i ma inne zastrzeżenia:

* [Po stronie serwera w bazie danych](#server-side-in-a-database)
* [Adres URL](#url)
* [Po stronie klienta w przeglądarce](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Po stronie serwera w bazie danych

W przypadku trwałych trwałości danych lub wszelkich danych, które muszą obejmować wiele użytkowników lub urządzeń, niezależna baza danych po stronie serwera prawie najlepiej sprawdza się. Opcje obejmują:

* Relacyjna baza danych SQL
* Magazyn kluczy i wartości
* Magazyn obiektów BLOB
* Magazyn tabel

Po zapisaniu danych w bazie danych nowy obwód może być uruchamiany przez użytkownika w dowolnym momencie. Dane użytkownika są przechowywane i dostępne w dowolnym nowym obwodzie.

Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz [dokumentację usługi Azure Storage](/azure/storage/) i [bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>Adres URL

W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL. Przykłady stanu modelu w adresie URL obejmują:

* Identyfikator wyświetlonej jednostki.
* Numer bieżącej strony w siatce stronicowanej.

Zawartość paska adresu przeglądarki jest zachowywana:

* Jeśli użytkownik ręcznie ponownie załaduje stronę.
* Jeśli serwer sieci Web stał się&mdash;niedostępny, użytkownik jest zmuszony do ponownego załadowania strony, aby można było połączyć się z innym serwerem.

Aby uzyskać informacje na temat definiowania wzorców adresów `@page` URL za pomocą <xref:blazor/routing>dyrektywy, zobacz.

### <a name="client-side-in-the-browser"></a>Po stronie klienta w przeglądarce

W przypadku danych przejściowych, które użytkownik aktywnie tworzy, wspólny magazyn kopii zapasowych jest `localStorage` przeglądarką `sessionStorage` i kolekcjami. Aplikacja nie jest wymagana do zarządzania lub czyszczenia stanu przechowywanego, jeśli obwód został porzucony, co jest korzystne w porównaniu z magazynem po stronie serwera.

> [!NOTE]
> "Po stronie klienta" w tej sekcji odwołuje się do scenariuszy po stronie klienta w przeglądarce, a nie [modelu hostingu po stronie klienta Blazor](xref:blazor/hosting-models#client-side). `localStorage`i `sessionStorage` mogą być używane w aplikacjach po stronie klienta Blazor, ale tylko przez napisanie kodu niestandardowego lub użycie pakietu innej firmy.

`localStorage`i `sessionStorage` różnią się w następujący sposób:

* `localStorage`jest objęty zakresem przeglądarki użytkownika. Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał. Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach. Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.
* `sessionStorage`jest zakresem do karty przeglądarki użytkownika. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał. Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony. Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.

Ogólnie rzecz `sessionStorage` biorąc, jest bezpiecznie używać. `sessionStorage`pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:

* Błędy w magazynie Stanów na kartach.
* Zachowanie mylące, gdy karta zastępuje stan innych kart.

`localStorage`jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.

Ostrzeżenia dotyczące korzystania z magazynu przeglądarki:

* Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.
* W przeciwieństwie do bazy danych po stronie serwera, magazyn nie jest dostępny podczas renderowania wstępnego, ponieważ żądana strona nie istnieje w przeglądarce na etapie renderowania.
* Przechowywanie kilku kilobajtów danych jest rozsądne dla Blazor aplikacji po stronie serwera. Po kilku kilobajtach należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.
* Użytkownicy mogą wyświetlać i modyfikować dane. [Ochrona danych](xref:security/data-protection/introduction) ASP.NET Core może ograniczyć ryzyko.

## <a name="third-party-browser-storage-solutions"></a>Rozwiązania do magazynowania przeglądarki innych firm

Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage`.

Warto rozważać wybór pakietu, który w sposób przezroczysty używa ASP.NET Core [ochrony danych](xref:security/data-protection/introduction). Ochrona danych ASP.NET Core szyfruje przechowywane dane i zmniejsza potencjalne ryzyko naruszenia przechowywanych danych. Jeśli dane serializowane w formacie JSON są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane przy użyciu narzędzi deweloperskich przeglądarki, a także zmodyfikować przechowywane dane. Zabezpieczanie danych nie zawsze jest problemem, ponieważ dane mogą być proste. Na przykład odczytywanie lub modyfikowanie zapisanego koloru elementu interfejsu użytkownika nie jest istotnym zagrożeniem bezpieczeństwa użytkownika lub organizacji. Unikaj zezwalania użytkownikom na inspekcję i manipulowanie *danymi poufnymi*.

## <a name="protected-browser-storage-experimental-package"></a>Pakiet Eksperymentalny magazynu chronionej przeglądarki

Przykład pakietu NuGet, który zapewnia [ochronę danych](xref:security/data-protection/introduction) dla `localStorage` i `sessionStorage` jest [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`jest nieobsługiwanym pakietem eksperymentalnym niewłaściwym do użycia w środowisku produkcyjnym.

### <a name="installation"></a>Instalacja

Aby zainstalować `Microsoft.AspNetCore.ProtectedBrowserStorage` pakiet:

1. W projekcie aplikacji po stronie serwera Blazor Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. W kodzie HTML najwyższego poziomu (na przykład w pliku *Pages/_Host. cshtml* w domyślnym szablonie projektu) Dodaj następujący `<script>` Tag:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. W metodzie Wywołaj `AddProtectedBrowserStorage` polecenie Dodaj `localStorage` i `sessionStorage` usługi do kolekcji usług: `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Zapisz i Załaduj dane w składniku

W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) , aby wstrzyknąć wystąpienie jednego z następujących elementów:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Wybór zależy od tego, który magazyn zapasowy ma być używany. W poniższym przykładzie `sessionStorage` jest używany:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Instrukcja może zostać umieszczona w pliku *_Imports. Razor* zamiast w składniku. `@using` Użycie pliku *_Imports. Razor* sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.

Aby zachować `currentCount` wartość `Counter` w składniku szablonu `IncrementCount` projektu, zmodyfikuj metodę do użycia `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne. Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany. `ProtectedSessionStore`automatycznie serializować i deserializacji danych JSON.

W poprzednim przykładzie `currentCount` kodu dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika. Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu [ochrony danych](xref:security/data-protection/introduction)ASP.NET Core. Zaszyfrowane dane można zobaczyć, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.

Aby odzyskać `currentCount` dane, jeśli użytkownik powróci `Counter` do składnika później (w tym, jeśli znajdują się w całkowicie nowym obwodzie) `ProtectedSessionStore.GetAsync`, użyj:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Jeśli parametry składnika obejmują stan nawigacji, wywołaj `ProtectedSessionStore.GetAsync` i przypisz wynik w `OnParametersSetAsync`, nie `OnInitializedAsync`. `OnInitializedAsync`jest wywoływana tylko raz podczas pierwszego wystąpienia składnika. `OnInitializedAsync`nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.

> [!WARNING]
> Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem. Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd podobny do:
>
> > W tej chwili nie można wystawić wywołań międzyoperacyjnych języka JavaScript. Dzieje się tak, ponieważ składnik jest wstępnie renderowany.
>
> Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem. Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa](#handle-prerendering) przed renderowaniem.

### <a name="handle-the-loading-state"></a>Obsłuż stan ładowania

Ponieważ magazyn przeglądarki jest asynchroniczny (dostęp za pośrednictwem połączenia sieciowego), zawsze jest okres, po upływie którego dane będą ładowane i dostępne do użycia przez składnik. Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.

Jednym z metod jest śledzenie, czy dane są `null` (nadal ładowane) czy nie. W składniku `Counter` domyślnym liczba jest przechowywana `int`w. Wprowadź `currentCount` wartość null, dodając znak zapytania`?`() do typu (`int`):

```csharp
private int? currentCount;
```

Zamiast bezwarunkowo wyświetlać przycisk Count i **Increment** , wybierz, aby wyświetlić te elementy tylko wtedy, gdy dane są ładowane:

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Obsługa przed renderowaniem

Podczas renderowania:

* Połączenie interaktywne z przeglądarką użytkownika nie istnieje.
* Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.

`localStorage`lub `sessionStorage` nie są dostępne podczas renderowania. Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd podobny do:

> W tej chwili nie można wystawić wywołań międzyoperacyjnych języka JavaScript. Dzieje się tak, ponieważ składnik jest wstępnie renderowany.

Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania. Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce. Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej `localStorage` przydatnej zawartości do momentu, `sessionStorage` gdy nie jest dostępna.

Aby wyłączyć renderowanie:

1. Otwórz plik *Pages/_Host. cshtml* i Usuń wywołanie metody `Html.RenderComponentAsync`.
1. Otwórz plik i Zastąp wywołanie `endpoints.MapBlazorHub<App>("app")`do `endpoints.MapBlazorHub()`. `Startup.cs` `App`jest typem składnika głównego. `"app"`jest selektorem CSS określającym lokalizację głównego składnika.

Renderowanie może być przydatne w przypadku innych stron, które `localStorage` nie `sessionStorage`używają ani. Aby włączyć renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu. Poniżej przedstawiono przykład przechowywania wartości licznika:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync()
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Wyrównać zachowywanie stanu do wspólnej lokalizacji

Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu. Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu. Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.

W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.

Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika. Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, `CounterStateProvider` zawiń składnik `Router` wokół składnika w `App` składniku (*App. Razor*):

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników. Poniższy `Counter` składnik implementuje wzorzec:

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Poprzedni składnik nie jest wymagany do współpracy z `ProtectedBrowserStorage`programem ani nie zajmuje się fazą "Ładowanie".

Aby można było zaradzić sobie z instrukcją `CounterStateProvider` prerenderingu zgodnie z wcześniejszym opisem, może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania. Szczegółowe informacje znajdują się w sekcji [Obsługa](#handle-prerendering) przed renderowaniem.

Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :

* Aby korzystać z stanu w wielu innych składnikach.
* Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.

Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać obsługi ładowania i zapisywania stanu globalnie.
