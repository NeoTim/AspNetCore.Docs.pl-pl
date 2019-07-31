---
title: Układ w ASP.NET Core
author: ardalis
description: Dowiedz się, jak używać typowych układów, udostępniać dyrektywy i uruchamiać wspólny kod przed renderowaniem widoków w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 6bd9dfc65c026ee524277aaaa21333d299c8981e
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68670008"
---
# <a name="layout-in-aspnet-core"></a>Układ w ASP.NET Core

[Steve Kowalski](https://ardalis.com/) i [Dave Brock](https://twitter.com/daveabrock)

Strony i widoki często udostępniają wizualizacje i programistyczne elementy. W tym artykule pokazano, jak:

* Użyj wspólnych układów.
* Dyrektywy udostępniania.
* Uruchom wspólny kod przed renderowaniem stron lub widoków.

W tym dokumencie omówiono układy dla dwóch różnych metod ASP.NET Core MVC: Razor Pages i kontrolery z widokami. W tym temacie różnice są minimalne:

* Razor Pages znajdują się w folderze Pages.
* Kontrolery z widokami używają folderu *widoki* dla widoków.

## <a name="what-is-a-layout"></a>Co to jest układ

Większość aplikacji sieci Web ma wspólny układ, który zapewnia użytkownikom spójne środowisko, które przechodzi ze strony do strony. Układ zazwyczaj obejmuje typowe elementy interfejsu użytkownika, takie jak nagłówek aplikacji, Nawigacja lub elementy menu oraz stopka.

![Przykład układu strony](layout/_static/page-layout.png)

Typowe struktury HTML, takie jak skrypty i arkusze stylów, są również często używane przez wiele stron w aplikacji. Wszystkie te elementy udostępnione mogą być zdefiniowane w pliku *układu* , do którego może odwoływać się dowolny widok używany w aplikacji. Układy redukują zduplikowany kod w widokach.

Zgodnie z Konwencją domyślny układ aplikacji ASP.NET Core ma nazwę *_Layout. cshtml*. Pliki układów dla nowych projektów ASP.NET Core utworzonych przy użyciu szablonów są następujące:

* Razor Pages: *Pages/Shared/_Layout. cshtml*

  ![Folder stron w Eksplorator rozwiązań](layout/_static/rp-web-project-views.png)

* Kontroler z widokami: *Widoki/Shared/_Layout. cshtml*

  ![Folder widoków w Eksplorator rozwiązań](layout/_static/mvc-web-project-views.png)

Układ definiuje szablon najwyższego poziomu dla widoków w aplikacji. Aplikacje nie wymagają układu. Aplikacje mogą definiować więcej niż jeden układ z różnymi widokami określającymi różne układy.

Poniższy kod przedstawia plik układu dla szablonu utworzonego za pomocą kontrolera i widoków:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Określanie układu

Widoki Razor mają `Layout` właściwość. Poszczególne widoki określają układ, ustawiając tę właściwość:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Określony układ może używać pełnej ścieżki (na przykład */Pages/Shared/_Layout.cshtml* lub */views/Shared/_Layout.cshtml*) lub częściowej nazwy (przykład: `_Layout`). Po podaniu częściowej nazwy aparat widoku Razor wyszukuje plik układu przy użyciu standardowego procesu odnajdywania. Folder, w którym istnieje metoda obsługi (lub kontroler), jest wyszukiwany jako pierwszy, a następnie folder *udostępniony* . Ten proces odnajdywania jest identyczny z procesem używanym do odnajdywania [widoków częściowych](xref:mvc/views/partial#partial-view-discovery).

Domyślnie każdy układ musi wywoływać `RenderBody`. W każdym miejscu, `RenderBody` w którym jest umieszczane wywołanie, zawartość widoku będzie renderowana.

<a name="layout-sections-label"></a>

### <a name="sections"></a>Sekcje

Układ może opcjonalnie odwoływać się do co najmniej jednej *sekcji*, `RenderSection`wywołując. Sekcje umożliwiają organizowanie miejsca, w którym należy umieścić pewne elementy strony. Każde wywołanie programu `RenderSection` może określać, czy ta sekcja jest wymagana czy opcjonalna:

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

Jeśli wymagana sekcja nie zostanie znaleziona, zostanie zgłoszony wyjątek. Poszczególne widoki określają zawartość, która ma być renderowana w sekcji przy `@section` użyciu składnia Razor. Jeśli strona lub widok definiuje sekcję, musi być renderowana (lub wystąpił błąd).

Przykładowa `@section` Definicja w widoku Razor Pages:

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

W powyższym kodzie *skrypt/Main. js* zostanie dodany do `scripts` sekcji na stronie lub w widoku. Inne strony lub widoki w tej samej aplikacji mogą nie wymagać tego skryptu i nie będą mogły definiować sekcji skryptów.

W poniższym znaczniku jest używana [pomocnik tagów częściowej](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) do renderowania *_ValidationScriptsPartial. cshtml*:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Poprzednia Adiustacja została wygenerowana przez tworzenie [szkieletu tożsamości](xref:security/authentication/scaffold-identity).

Sekcje zdefiniowane w stronie lub widoku są dostępne tylko na stronie natychmiastowego układu. Nie można się do nich odwoływać ze stron częściowych, składników widoku ani innych części systemu widoku.

### <a name="ignoring-sections"></a>Ignorowanie sekcji

Domyślnie treść i wszystkie sekcje na stronie zawartości muszą być renderowane na stronie układu. Aparat widoku Razor wymusza to przez śledzenie, czy treść i każda z nich zostały renderowane.

Aby nakazać aparatowi widoku ignorowanie treści lub sekcji, wywołaj `IgnoreBody` metody `IgnoreSection` i.

Treść i Każda sekcja na stronie Razor muszą być renderowane lub ignorowane.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importowanie wspólnych dyrektyw

Widoki i strony mogą używać dyrektyw Razor do importowania przestrzeni nazw i używania [iniekcji zależności](dependency-injection.md). Dyrektywy udostępnione przez wiele widoków można określić we wspólnym pliku *_ViewImports. cshtml* . `_ViewImports` Plik obsługuje następujące dyrektywy:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Plik nie obsługuje innych funkcji Razor, takich jak definicje funkcji i sekcji.

Przykładowy `_ViewImports.cshtml` plik:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Plik *_ViewImports. cshtml* dla aplikacji ASP.NET Core MVC jest zwykle umieszczany w folderze *stron* (lub *widoków*). Plik *_ViewImports. cshtml* można umieścić w dowolnym folderze, w takim przypadku tylko do stron lub widoków w tym folderze i jego podfolderach. `_ViewImports`pliki są przetwarzane, rozpoczynając od poziomu głównego, a następnie dla każdego folderu, który zaczyna się do lokalizacji strony lub widoku. `_ViewImports`ustawienia określone na poziomie głównym mogą zostać zastąpione na poziomie folderu.

Załóżmy na przykład, że:

* Plik *_ViewImports. cshtml* poziomu głównego zawiera `@model MyModel1` i `@addTagHelper *, MyTagHelper1`.
* Plik *_ViewImports. cshtml* z podfolderem `@model MyModel2` zawiera `@addTagHelper *, MyTagHelper2`i.

Strony i widoki w podfolderze będą miały dostęp do pomocników tagów i `MyModel2` modelu.

Jeśli w hierarchii plików znaleziono wiele plików *_ViewImports. cshtml* , połączone zachowanie dyrektyw jest następujące:

* `@addTagHelper`, `@removeTagHelper`: wszystkie uruchomienia, w kolejności
* `@tagHelperPrefix`: najbliższy jeden do widoku przesłania wszystkie inne
* `@model`: najbliższy jeden do widoku przesłania wszystkie inne
* `@inherits`: najbliższy jeden do widoku przesłania wszystkie inne
* `@using`: wszystkie są uwzględnione; duplikaty są ignorowane
* `@inject`: dla każdej właściwości najbliższy jeden do widoku przesłania wszystkie inne osoby o tej samej nazwie właściwości

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Uruchamianie kodu przed każdym widokiem

Kod, który musi zostać uruchomiony przed każdym widokiem lub stroną należy umieścić w pliku *_ViewStart. cshtml* . Zgodnie z Konwencją plik *_ViewStart. cshtml* znajduje się w folderze *stron* (lub *widoków*). Instrukcje wymienione w *_ViewStart. cshtml* są uruchamiane przed wszystkimi pełnymi widokami (nie układami i nieczęściowymi widokami). Podobnie jak [ViewImports. cshtml](xref:mvc/views/layout#viewimports), *_ViewStart. cshtml* jest hierarchiczna. Jeśli plik *_ViewStart. cshtml* jest zdefiniowany w folderze widoku lub strony, zostanie on uruchomiony po elemencie zdefiniowanym w folderze głównym folderu *stron* (lub *widoków*) (jeśli istnieje).

Przykładowy plik *_ViewStart. cshtml* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Powyższy plik określa, że wszystkie widoki będą korzystać z układu *_Layout. cshtml* .

*_ViewStart. cshtml* i *_ViewImports. cshtml* **nie** są zwykle umieszczane w folderze */Pages/Shared* (lub */views/Shared*). Wersje tych plików na poziomie aplikacji należy umieścić bezpośrednio w folderze */Pages* (lub */views*).
