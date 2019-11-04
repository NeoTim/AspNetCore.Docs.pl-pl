---
title: Wprowadzenie do NSwag i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak używać NSwag do generowania dokumentacji i stron pomocy dla ASP.NET Core internetowego interfejsu API.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 23927e6ce0a7b29ce3f32d4e7f7d3f234257ca9b
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416157"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Wprowadzenie do NSwag i ASP.NET Core

[Christoph Nienaber](https://twitter.com/zuckerthoben), [Suter](https://rsuter.com)i [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag oferuje następujące możliwości:

* Możliwość korzystania z interfejsu użytkownika programu Swagger i generatora Swagger.
* Elastyczne możliwości generowania kodu.

W przypadku usługi NSwag nie jest potrzebny istniejący interfejs API&mdash;można używać interfejsów API innych firm, które zawierają strukturę Swagger i generować implementację klienta. NSwag umożliwia przyspieszenie cyklu programowania i łatwe dostosowanie do zmian interfejsu API.

## <a name="register-the-nswag-middleware"></a>Rejestrowanie oprogramowania pośredniczącego NSwag

Zarejestruj oprogramowanie pośredniczące NSwag w programie:

* Generuj specyfikację Swagger dla zaimplementowanego interfejsu API sieci Web.
* Zaoferują interfejs użytkownika struktury Swagger, aby przeglądać i testować internetowy interfejs API.

Aby użyć oprogramowania [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core pośredniczącego, zainstaluj pakiet NuGet [NSwag. AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) . Ten pakiet zawiera oprogramowanie pośredniczące do generowania i obsługiwania specyfikacji struktury Swagger, interfejsu użytkownika struktury Swagger (v2 i V3) oraz [interfejsu użytkownika ReDoc](https://github.com/Rebilly/ReDoc).

Aby zainstalować pakiet NuGet NSwag, należy użyć jednej z następujących metod:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* W oknie **konsola Menedżera pakietów** :
  * Przejdź do **widoku** > **innej** **konsoli Menedżera pakietów** > Windows
  * Przejdź do katalogu, w którym znajduje się plik *TodoApi. csproj*
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet** :
  * Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** > **Zarządzanie pakietami NuGet**
  * Ustaw **Źródło pakietu** na "NuGet.org"
  * Wprowadź ciąg "NSwag. AspNetCore" w polu wyszukiwania
  * Wybierz pakiet "NSwag. AspNetCore" z karty **Przeglądaj** , a następnie kliknij przycisk **Instaluj** .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania** > **Dodaj pakiety...**
* Ustaw listę rozwijaną **źródła** okna **Dodaj pakiety** na "NuGet.org"
* Wprowadź ciąg "NSwag. AspNetCore" w polu wyszukiwania
* Wybierz pakiet "NSwag. AspNetCore" z okienka wyników, a następnie kliknij pozycję **Dodaj pakiet** .

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger

Dodaj i skonfiguruj strukturę Swagger w aplikacji ASP.NET Core, wykonując następujące czynności:

* W metodzie `Startup.ConfigureServices` Zarejestruj wymagane usługi Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* W metodzie `Startup.Configure` Włącz oprogramowanie pośredniczące do obsługi wygenerowanej specyfikacji struktury Swagger i interfejsu użytkownika programu Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Uruchom aplikację. Przejdź do:
  * `http://localhost:<port>/swagger` wyświetlić interfejsu użytkownika struktury Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` wyświetlić specyfikacji struktury Swagger.

## <a name="code-generation"></a>Generowanie kodu

Aby skorzystać z możliwości generowania kodu w programie NSwag, należy wybrać jedną z następujących opcji:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; aplikacji klasycznej dla systemu Windows w celu wygenerowania C# kodu klienta interfejsu API w języku lub TypeScript.
* Pakiety NuGet [NSwag. CodeGeneration. CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) lub [NSwag. CodeGeneration. TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) na potrzeby generowania kodu w ramach projektu.
* NSwag z [wiersza polecenia](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Pakiet NuGet [NSwag. MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) .
* [Połączona usługa openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) nie &ndash; połączonej usługi programu Visual Studio w celu wygenerowania kodu klienta C# interfejsu API w języku lub TypeScript. Program generuje C# również kontrolery dla usług openapi Services z NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generuj kod przy użyciu NSwagStudio

* Zainstaluj program NSwagStudio, postępując zgodnie z instrukcjami w [repozytorium GitHub NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).
* Uruchom NSwagStudio i wprowadź adres URL pliku *Swagger. JSON* w polu tekstowym **adres URL specyfikacji struktury Swagger** . Na przykład *http://localhost:44354/swagger/v1/swagger.json* .
* Kliknij przycisk **Utwórz kopię lokalną** , aby wygenerować reprezentację JSON specyfikacji struktury Swagger.

  ![Utwórz lokalną kopię specyfikacji struktury Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* W obszarze dane **wyjściowe** kliknij pole wyboru **Klient CSharp** . W zależności od projektu można również wybrać **klienta TypeScript** lub **kontroler internetowego interfejsu API CSharp**. W przypadku wybrania opcji **kontroler internetowego interfejsu API CSharp**Specyfikacja usługi ponownie kompiluje usługę, służącą jako generacja odwrotna.
* Kliknij pozycję **Generuj dane wyjściowe** , aby C# utworzyć kompletną implementację projektu *TodoApi. NSwag* . Aby wyświetlić wygenerowany kod klienta, kliknij kartę **Klient CSharp** :

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> Kod C# klienta jest generowany na podstawie opcji wybranych na karcie **Ustawienia** . zmodyfikuj ustawienia, aby wykonać zadania, takie jak domyślna zmiana nazw obszaru nazw i generowanie metody synchronicznej.

* Skopiuj wygenerowany C# kod do pliku w projekcie klienta, który będzie korzystał z interfejsu API.
* Rozpocznij korzystanie z internetowego interfejsu API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Dostosuj dokumentację interfejsu API

Struktura Swagger zawiera opcje dokumentujące model obiektów, co ułatwia użycie internetowego interfejsu API.

### <a name="api-info-and-description"></a>Informacje o interfejsie API i opis

W metodzie `Startup.ConfigureServices` akcja konfiguracyjna przeniesiona do metody `AddSwaggerDocument` dodaje takie informacje, jak autor, licencja i opis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:

![Interfejs użytkownika struktury Swagger z informacjami o wersji](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>komentarze XML

Aby włączyć Komentarze XML, wykonaj następujące czynności:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj < Project_Name >. csproj**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**
* Sprawdź pole **plik dokumentacji XML** w sekcji **dane wyjściowe** na karcie **kompilacja**

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *okienko rozwiązania*naciśnij klawisz **Control** i kliknij nazwę projektu. Przejdź do **menu narzędzia** > **Edytuj plik**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **kompilacja** > **kompilator**
* Zaznacz pole **Generuj dokumentację XML** w sekcji **Opcje ogólne** .

::: moniker-end

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Adnotacje danych

::: moniker range="<= aspnetcore-2.0"

Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracanym dla akcji interfejsu API sieci Web jest [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nie może wnioskować o to, co działa i co zwraca.

Rozważmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja zwraca `IActionResult`, ale wewnątrz akcji, zwraca albo [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) lub [nieprawidłowego żądania](xref:System.Web.Http.ApiController.BadRequest*). Użyj adnotacji danych, aby poinformować klientów, których kodów stanu HTTP Ta akcja jest zwracana. Dekorować akcję z następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracanym dla akcji interfejsu API sieci Web jest [ActionResult\<t >](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), może on tylko wnioskować o typie zwracanym zdefiniowanym przez `T`. Nie można automatycznie wywnioskować innych możliwych typów zwracanych.

Rozważmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja zwraca `ActionResult<T>`. Wewnątrz akcji zwraca [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Ponieważ kontroler ma atrybut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) , istnieje również odpowiedź [nieprawidłowego żądania](xref:System.Web.Http.ApiController.BadRequest*) . Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses). Użyj adnotacji danych, aby poinformować klientów, których kodów stanu HTTP Ta akcja jest zwracana. Dekorować akcję z następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

W ASP.NET Core 2,2 lub nowszych można używać konwencji zamiast jawnie dekorowania nazwy poszczególnych akcji z `[ProducesResponseType]`. Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

::: moniker-end

Generator Swagger może teraz prawidłowo opisać tę akcję, a wygenerowane klienci wiedzą, co otrzymują podczas wywoływania punktu końcowego. Zgodnie z zaleceniem dekorować wszystkie akcje z tymi atrybutami.

Aby uzyskać wskazówki dotyczące odpowiedzi HTTP, które powinny być zwracane przez działania interfejsu API, zobacz [specyfikację RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
