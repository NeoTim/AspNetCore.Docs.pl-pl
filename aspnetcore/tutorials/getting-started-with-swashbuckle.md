---
title: Wprowadzenie do Swashbuckle i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak dodać Swashbuckle do projektu interfejsu API sieci Web ASP.NET Core, aby zintegrować interfejs użytkownika struktury Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/21/2019
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: d3cef72de22e54f7e65ddf9f1446eb32256d0c71
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924982"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Wprowadzenie do Swashbuckle i ASP.NET Core

Autorzy [Shayne Boyer](https://twitter.com/spboyer) i [Scott Addie](https://twitter.com/Scott_Addie)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([sposobu pobierania](xref:index#how-to-download-a-sample))

Istnieją trzy główne składniki do Swashbuckle:

* [Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): model obiektów Swagger i oprogramowanie pośredniczące umożliwiające Uwidacznianie `SwaggerDocument` obiektów jako punktów końcowych JSON.

* [Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): Generator Swagger, który kompiluje `SwaggerDocument` obiekty bezpośrednio z tras, kontrolerów i modeli. Zwykle jest on łączony z programem pośredniczącym punktu końcowego programu Swagger, aby automatycznie uwidaczniać dane JSON struktury Swagger.

* [Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): wbudowana wersja narzędzia interfejsu użytkownika struktury Swagger. Interpretuje kod JSON struktury Swagger w celu utworzenia zaawansowanego, dostosowywalnego środowiska do opisywania funkcji interfejsu Web API. Zawiera wbudowaną obsługę testów dla metod publicznych.

## <a name="package-installation"></a>Instalacja pakietu

Swashbuckle można dodać przy użyciu następujących metod:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* W oknie **konsola Menedżera pakietów** :
  * Przejdź do **wyświetlania** > innych**konsoli Menedżera pakietów** **systemu Windows** > 
  * Przejdź do katalogu, w którym znajduje się plik *TodoApi. csproj*
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0-rc4
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet** :
  * Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** > **Zarządzanie pakietami NuGet**
  * Ustaw **Źródło pakietu** na "NuGet.org"
  * Upewnij się, że opcja "Uwzględnij wersję wstępną" jest włączona
  * Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania
  * Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z karty **Przeglądaj** , a następnie kliknij przycisk **Instaluj** .

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania** > **Dodaj pakiety...**
* Ustaw listę rozwijaną **źródła** okna **Dodaj pakiety** na "NuGet.org"
* Upewnij się, że opcja "Pokaż pakiety wersji wstępnej" jest włączona
* Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania
* Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z okienka wyników, a następnie kliknij pozycję **Dodaj pakiet** .

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w zintegrowanym **terminalu**:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc4
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc4
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger

W klasie zaimportuj następującą przestrzeń nazw, aby `OpenApiInfo` użyć klasy: `Startup`

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Dodaj Generator Swagger do kolekcji usług w `Startup.ConfigureServices` metodzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

`Startup.Configure` W metodzie Włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika programu Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

Poprzednie `UseSwaggerUI` wywołanie metody włącza [oprogramowanie pośredniczące pliku statycznego](xref:fundamentals/static-files). Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.

Uruchom aplikację i przejdź do `http://localhost:<port>/swagger/v1/swagger.json`. Wygenerowany dokument opisujący punkty końcowe pojawia się, jak pokazano w [specyfikacji Swagger (Swagger. JSON)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Interfejs użytkownika struktury Swagger można znaleźć pod `http://localhost:<port>/swagger`adresem. Eksploruj interfejs API za pośrednictwem interfejsu użytkownika struktury Swagger i Uwzględnij go w innych programach.

> [!TIP]
> Aby obpracować interfejs użytkownika struktury Swagger w katalogu głównym aplikacji`http://localhost:<port>/`(), `RoutePrefix` ustaw właściwość na pusty ciąg:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

W przypadku używania katalogów z usługami IIS lub zwrotnego serwera proxy Ustaw punkt końcowy struktury Swagger na ścieżkę względną przy użyciu `./` prefiksu. Na przykład `./swagger/v1/swagger.json`. Użycie `/swagger/v1/swagger.json` instruuje aplikację, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany). Na przykład użyj `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`zamiast.

## <a name="customize-and-extend"></a>Dostosuj i rozwiń

Struktura Swagger zawiera opcje dokumentowania modelu obiektów i dostosowywania interfejsu użytkownika w celu dopasowania go do motywu.

`Startup` W klasie Dodaj następujące przestrzenie nazw:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informacje o interfejsie API i opis

Akcja konfiguracji przeniesiona do `AddSwaggerGen` metody powoduje dodanie informacji, takich jak autor, licencja i opis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:

![Interfejs użytkownika struktury Swagger z informacjami o wersji: Description, Author i linku więcej](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>komentarze XML

Komentarze XML można włączyć przy użyciu następujących metod:

#### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj < Project_Name >. csproj**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz **właściwości**.
* Sprawdź pole **plik dokumentacji XML** w sekcji **dane wyjściowe** na karcie **kompilacja** .

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *okienko rozwiązania*naciśnij klawisz **Control** i kliknij nazwę projektu. Przejdź do **menu Narzędzia** > **Edytuj plik**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **kompilator** **kompilacji** >
* Zaznacz pole **Generuj dokumentację XML** w sekcji **Opcje ogólne** .

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków. Nieudokumentowane typy i elementy członkowskie są wskazywane przez komunikat ostrzegawczy. Na przykład następujący komunikat oznacza naruszenie kodu ostrzegawczego 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Aby pominąć ostrzeżenia dla całego projektu, należy zdefiniować rozdzielaną średnikami listę kodów ostrzeżeń do ignorowania w pliku projektu. Dołączanie kodów ostrzeżeń w `$(NoWarn);` celu zastosowania [ C# wartości domyślnych](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Aby pominąć ostrzeżenia tylko dla określonych elementów członkowskich, należy ująć kod w [#pragma ostrzeżenia](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocesora. Takie podejście jest przydatne w przypadku kodu, który nie powinien być ujawniony za pośrednictwem dokumentacji interfejsu API. W poniższym przykładzie kod ostrzegawczy CS1591 jest ignorowany dla całej `Program` klasy. Wymuszanie kodu ostrzegawczego jest przywracane po zamknięciu definicji klasy. Określ wiele kodów ostrzeżeń z listą rozdzielaną przecinkami.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Skonfiguruj strukturę Swagger, aby używała pliku XML, który jest generowany z poprzednimi instrukcjami. W przypadku systemów operacyjnych Linux lub innych niż Windows nazwy plików i ścieżki mogą być rozróżniane wielkości liter. Na przykład plik *TodoApi. XML* jest prawidłowy w systemie Windows, ale nie CentOS.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

W poprzednim kodzie [odbicie](/dotnet/csharp/programming-guide/concepts/reflection) jest używane do kompilowania nazwy pliku XML pasującego do projektu interfejsu API sieci Web. Właściwość [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) służy do konstruowania ścieżki do pliku XML. Niektóre funkcje struktury Swagger (na przykład schematu parametrów wejściowych lub metod HTTP i kodów odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML. W przypadku większości funkcji, a mianowicie podsumowania metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.

Dodawanie komentarzy z potrójnym ukośnikiem do akcji rozszerza interfejs użytkownika struktury Swagger, dodając opis do nagłówka sekcji. Dodaj element `Delete` podsumowania > powyżej akcji: [ \<](/dotnet/csharp/programming-guide/xmldoc/summary)

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Interfejs użytkownika struktury Swagger wyświetla tekst wewnętrzny `<summary>` elementu poprzedniego kodu:

![Interfejs użytkownika struktury Swagger pokazujący komentarz XML "Usuwa określony TodoItem". dla metody DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Interfejs użytkownika jest oparty na wygenerowanym schemacie JSON:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

`Create` [ Dodaj\<> element uwagi](/dotnet/csharp/programming-guide/xmldoc/remarks) do dokumentacji metody akcji. Uzupełnia on informacje określone w `<summary>` elemencie i zapewnia bardziej niezawodny interfejs użytkownika struktury Swagger. Zawartość `<remarks>` elementu może składać się z tekstu, JSON lub XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Zwróć uwagę na ulepszenia interfejsu użytkownika z następującymi dodatkowymi komentarzami:

![Interfejs użytkownika struktury Swagger z pokazanymi dodatkowymi komentarzami](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Adnotacje danych

Dekorować model z atrybutami, które znajdują się w przestrzeni nazw [System. ComponentModel.](/dotnet/api/system.componentmodel.dataannotations) DataAnnotations, aby pomóc w użyciu składników interfejsu użytkownika struktury Swagger.

`[Required]` Dodaj atrybut`Name` do właściwości`TodoItem` klasy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Obecność tego atrybutu zmienia zachowanie interfejsu użytkownika i zmienia źródłowy schemat JSON:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

`[Produces("application/json")]` Dodaj atrybut do kontrolera interfejsu API. Celem jest zadeklarowanie, że działania kontrolera obsługują typ zawartości odpowiedzi dla *aplikacji/JSON*:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

Lista rozwijana **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji Get kontrolera:

![Interfejs użytkownika struktury Swagger z domyślnym typem zawartości odpowiedzi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

W miarę wzrostu użycia adnotacji danych w interfejsie API sieci Web strony interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.

### <a name="describe-response-types"></a>Opisz typy odpowiedzi

Deweloperzy korzystający z internetowego interfejsu API są najbardziej zainteresowani, które są&mdash;zwracane w szczególności typy odpowiedzi i kody błędów (jeśli nie są standardem). Typy odpowiedzi i kody błędów są oznaczane w komentarzach XML i adnotacjach danych.

`Create` Akcja zwraca kod stanu HTTP 201 na sukcesie. Kod stanu HTTP 400 jest zwracany, gdy treść ogłoszonego żądania ma wartość null. Bez odpowiedniej dokumentacji w interfejsie użytkownika programu Swagger klient nie ma wiedzy na temat oczekiwanych wyników. Rozwiąż ten problem, dodając wyróżnione wiersze w następującym przykładzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Interfejs użytkownika struktury Swagger teraz jasno dokumentuje oczekiwane kody odpowiedzi HTTP:

![Interfejs użytkownika struktury Swagger pokazujący opis klasy odpowiedzi "zwraca nowo utworzony element zadania" i "400-Jeśli element ma wartość null" dla kodu stanu i przyczyny w komunikatach odpowiedzi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

W ASP.NET Core 2,2 lub nowszych Konwencji mogą służyć jako alternatywa dla jawnego dekorowania nazwy poszczególnych akcji z `[ProducesResponseType]`. Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Podstawowy interfejs użytkownika jest zarówno funkcjonalny, jak i najbardziej do wysłania. Jednak strony dokumentacji interfejsu API powinny reprezentować swoją markę lub motyw. Znakowanie składników Swashbuckle wymaga dodania zasobów do obsługi plików statycznych i skompilowania struktury folderów do hostowania tych plików.

Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) do projektu:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Poprzedni pakiet NuGet jest już zainstalowany, jeśli celem jest .NET Core 2. x i używanie [pakietu](xref:fundamentals/metapackage).

Włącz oprogramowanie pośredniczące plików statycznych:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Pobierz zawartość folderu *ROZKŁ* z [repozytorium GitHub interfejsu użytkownika programu Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist). Ten folder zawiera zasoby wymagane dla strony interfejsu użytkownika programu Swagger.

Utwórz folder *wwwroot/Swagger/UI* i skopiuj go do zawartości folderu *ROZKŁ* .

Utwórz *niestandardowy plik. css* w pliku *wwwroot/Swagger/UI*z następującym arkuszem CSS, aby dostosować nagłówek strony:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Odwołuje się do *niestandardowego. css* w pliku *index. html* w folderze UI, po dowolnych innych plikach CSS:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Przejdź do strony *index. html* pod adresem `http://localhost:<port>/swagger/ui/index.html`. Wprowadź `https://localhost:<port>/swagger/v1/swagger.json` tekst w polu tekstowym nagłówka i kliknij przycisk **Eksploruj** . Wynikowa strona wygląda następująco:

![Interfejs użytkownika struktury Swagger z niestandardowym tytułem nagłówka](web-api-help-pages-using-swagger/_static/custom-header.png)

Istnieje dużo więcej możliwości na stronie. Zapoznaj się z pełnymi możliwościami zasobów interfejsu użytkownika w [repozytorium GitHub interfejsu użytkownika programu Swagger](https://github.com/swagger-api/swagger-ui).
