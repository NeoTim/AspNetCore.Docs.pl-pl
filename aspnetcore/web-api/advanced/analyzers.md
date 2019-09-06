---
title: Korzystanie z analizatorów interfejsu API sieci Web
author: pranavkm
description: Dowiedz się więcej o pakiecie analizatorów interfejsów API sieci Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1568eb0304a58758caa5f82249dc42872f5c36b9
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384865"
---
# <a name="use-web-api-analyzers"></a>Korzystanie z analizatorów interfejsu API sieci Web

ASP.NET Core 2,2 i nowsze udostępniają pakiet analizatorów MVC przeznaczony do użycia z projektami interfejsu API sieci Web. Analizatory współpracują z kontrolerami oznaczonymi przy użyciu programu, podczas kompilowania w ramach <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> [Konwencji interfejsu API sieci Web](xref:web-api/advanced/conventions).

Pakiet analizatorów powiadamia użytkownika o każdej akcji kontrolera, która:

* Zwraca niezadeklarowany kod stanu.
* Zwraca niezadeklarowany wynik sukcesu.
* Dokumentuje kod stanu, który nie jest zwracany.
* Zawiera jawną kontrolę walidacji modelu.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Odwoływanie się do pakietu analizatora

W ASP.NET Core 3,0 lub nowszych analizatory są zawarte w zestaw .NET Core SDK. Aby włączyć analizator w projekcie, należy uwzględnić `IncludeOpenAPIAnalyzers` właściwość w pliku projektu:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Instalacja pakietu

Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. API. analizatory](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) z jedną z następujących metod:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

W oknie **konsola Menedżera pakietów** :
  * Przejdź do pozycji **Wyświetl** > inną **konsolę Menedżera pakietów** **systemu Windows** > .
  * Przejdź do katalogu, w którym znajduje się plik *ApiConventions. csproj* .
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania** > **Dodaj pakiety...** .
* Ustaw listę rozwijaną **Źródło** okna **Dodaj pakiety** na "NuGet.org".
* W polu wyszukiwania wprowadź ciąg "Microsoft. AspNetCore. MVC. API. analizatory".
* Wybierz pakiet "Microsoft. AspNetCore. MVC. API. analizatory" w okienku wyników, a następnie kliknij pozycję **Dodaj pakiet**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **zintegrowanym terminalu**:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uruchom następujące polecenie:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analizatory dla Konwencji interfejsu API sieci Web

Dokumenty OpenAPI zawierają kody stanu i typy odpowiedzi, które może zwrócić akcja. W ASP.NET Core MVC atrybuty takie jak <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> i <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> są używane do dokumentowania akcji. <xref:tutorials/web-api-help-pages-using-swagger>Szczegółowe informacje na temat dokumentowania internetowego interfejsu API.

Jeden z analizatorów w pakiecie sprawdza kontrolery z <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> adnotacją i identyfikuje akcje, które nie są w pełni udokumentowane. Rozważmy następujący przykład:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Poprzednia akcja powoduje udokumentowanie typu zwracanego przez HTTP 200, ale nie dokumentuje kodu stanu błędu HTTP 404. Analizator raportuje brakującą dokumentację dla kodu stanu HTTP 404 jako ostrzeżenie. Podano opcję rozwiązania problemu.

![Analizator raportuje ostrzeżenie](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
