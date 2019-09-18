---
title: Testy jednostkowe Razor Pages w ASP.NET Core
author: guardrex
description: Dowiedz się, jak tworzyć testy jednostkowe dla aplikacji Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: afac97d686ef190ebb92d20a55a15dd774b0d1de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081426"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Testy jednostkowe Razor Pages w ASP.NET Core

Przez [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe Razor Pages aplikacji. Testy warstwy dostępu do danych (DAL) i modele stron pomagają zapewnić:

* Części aplikacji Razor Pages działają niezależnie i razem jako jednostka podczas konstruowania aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy związane z aktualizacjami kodu, są dostępne podczas zautomatyzowanego kompilowania i wdrażania.

W tym temacie założono, że masz podstawową wiedzę na temat Razor Pages aplikacji i testów jednostkowych. Jeśli nie znasz Razor Pages aplikacji lub testów, zapoznaj się z następującymi tematami:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie C# jednostkowe w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *src/RazorPagesTestSample*         | Zezwala użytkownikowi na dodawanie wiadomości, usuwanie jednego komunikatu, usuwanie wszystkich komunikatów i analizowanie komunikatów (Znajdowanie średniej liczby wyrazów na komunikat). |
| Aplikacja testowa    | *tests/RazorPagesTestSample.Tests* | Służy do przetestowania jednostki DAL i indeksu strony aplikacji wiadomości. |

Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub [Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w folderze *Tests/RazorPagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji komunikatów

Aplikacja wiadomości jest systemem komunikatów Razor Pages o następujących cechach:

* Strona indeks aplikacji (*Pages/index. cshtml* i *Pages/index. cshtml. cs*) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (Znajdź średnią liczbę wyrazów na komunikat).
* Komunikat jest opisywany `Message` przez klasę (*Data/Message. cs*) z dwiema właściwościami: `Id` (Key) i `Text` (Message). `Text` Właściwość jest wymagana i jest ograniczona do 200 znaków.
* Komunikaty są przechowywane przy użyciu&#8224; [bazy danych znajdującej się w pamięci Entity Framework](/ef/core/providers/in-memory/).
* Aplikacja zawiera dal w swojej klasie `AppDbContext` kontekstu bazy danych (*Data/AppDbContext. cs*). Metody dal są oznaczone `virtual`, co umożliwia imitację metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów. Te *rozsiane komunikaty* są również używane w testach.

&#8224;W temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory), wyjaśniono, jak korzystać z bazy danych w pamięci dla testów z MSTest. W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) . Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.

Chociaż Przykładowa aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i <xref:mvc/controllers/testing> (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testuj organizację aplikacji

Aplikacja testowa jest aplikacją konsolową wewnątrz folderu *Tests/RazorPagesTestSample. Tests* .

| Testuj folder aplikacji | Opis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla dal.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe dla modelu strony indeksu.</li></ul> |
| *Uruchamiane*     | `TestDbContextOptions` Zawiera metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego dal, aby baza danych była resetowana do jego warunku bazowego dla każdego testu. |

Platforma testowa jest [xUnit](https://xunit.github.io/). Struktura imitacji obiektu jest [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma dal z czterema metodami zawartymi w `AppDbContext` klasie (*src/RazorPagesTestSample/Data/AppDbContext. cs*). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| DAL, Metoda               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje `Text` z bazy danych posortowanej według właściwości. `List<Message>` |
| `AddMessageAsync`        | `Message` Dodaje do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa jeden `Message` z baz danych przez `Id`.                      |

Testy jednostkowe dal wymagają <xref:Microsoft.EntityFrameworkCore.DbContextOptions> podczas tworzenia nowego `AppDbContext` dla każdego testu. Jednym z metod tworzenia `DbContextOptions` dla każdego testu jest <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>użycie:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem polega na tym, że każdy test otrzymuje bazę danych w dowolnym stanie, w którym został pozostawiony poprzedni test. Może to być przyczyną problemów podczas próby zapisania niepodzielnych testów jednostkowych, które nie zakłócają siebie nawzajem. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych dla każdego testu, `DbContextOptions` Podaj wystąpienie, które jest oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak to zrobić za pomocą `Utilities` metody `TestDbContextOptions` klasy (*Tests/RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions` Użycie w ramach testów jednostkowych dal umożliwia niepodzielne uruchamianie każdego testu w przypadku wystąpienia nowego bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa w `DataAccessLayerTest` klasie (*UnitTests/DataAccessLayerTest. cs*) postępuje zgodnie z podobnym wzorcem porządkowania:

1. Ponownego Baza danych jest skonfigurowana do testowania i/lub oczekiwany wynik jest zdefiniowany.
1. Wykonywać Test jest wykonywany.
1. Stanowcz Potwierdzenia są wykonywane w celu ustalenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` Metoda jest odpowiedzialna za usunięcie pojedynczego komunikatu identyfikowanego przez jego `Id` (*src/RazorPagesTestSample/Data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa komunikat, gdy komunikat jest obecny w bazie danych. Druga metoda testuje, że baza danych nie zmienia się, `Id` Jeśli komunikat do usunięcia nie istnieje. Poniżej `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` przedstawiono metodę:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Najpierw Metoda wykonuje krok rozmieszczania, w którym odbywa się przygotowanie do kroku działania. Wypełnianie komunikatów jest uzyskiwane i przechowywane `seedMessages`w. Wypełnianie komunikatów jest zapisywane w bazie danych. Komunikat z programem `Id` `1` jest ustawiany do usunięcia. Gdy metoda jest wykonywana, oczekiwane komunikaty powinny zawierać wszystkie komunikaty z wyjątkiem jednego `Id` z `1`. `DeleteMessageAsync` `expectedMessages` Zmienna reprezentuje oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: Metoda jest wykonywana `recId` w `1`: `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` od kontekstu i porównuje ją `expectedMessages` z potwierdzeniem, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, że te dwa `List<Message>` są takie same:

* Wiadomości są uporządkowane według `Id`.
* Pary komunikatów są porównywane z `Text` właściwością.

Podobna Metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` testowa sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny być równe rzeczywistym komunikatom po `DeleteMessageAsync` wykonaniu metody. Nie powinno się zmieniać zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron indeksu są dostępne w `IndexModel` klasie w *src/RazorPagesTestSample/Pages/index. cshtml. cs*.

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z dal dla interfejsu użytkownika przy użyciu `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, program wywołuje `AddMessageAsync` Dodawanie komunikatu do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołuje `DeleteAllMessagesAsync` usuwanie wszystkich komunikatów w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje `DeleteMessageAsync` , aby usunąć komunikat `Id` z określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli co najmniej jeden komunikat znajduje się w bazie danych, program oblicza średnią liczbę wyrazów na komunikat. |

Metody modelu strony są testowane przy użyciu siedmiu testów w `IndexPageTests` klasie (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy używają znanego wzorca porządkowania i potwierdzeń. Te testy koncentrują się na:

* Określanie, czy metody są zgodne z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowe.
* Potwierdzenie metod daje poprawne <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Sprawdzanie, czy przypisania wartości właściwości są wykonywane poprawnie.

Ta grupa testów często służy do zawzorowania metod DAL, aby generować oczekiwane dane dla kroku działania, w którym jest wykonywana metoda modelu strony. Na przykład `GetMessagesAsync` Metoda `AppDbContext` jest makieta w celu wygenerowania danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, funkcja makieta zwraca wynik. Dane nie pochodzą z bazy danych. Powoduje to utworzenie przewidywalnych, niezawodnych warunków testowania dla korzystania z DAL w testach modelu strony.

Test pokazuje, `GetMessagesAsync` jak metoda jest makietą dla modelu strony: `OnGetAsync_PopulatesThePageModel_WithAListOfMessages`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy metoda jest wykonywana w kroku Act, wywołuje `GetMessagesAsync` metodę modelu strony. `OnGetAsync`

Krok działania testów jednostkowych (*testy/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` Metoda modelu strony (*src/RazorPagesTestSample/Pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` Metoda w dal nie zwraca wyniku dla tego wywołania metody. Makieta wersji metody zwraca wynik.

W tym `Assert` kroku rzeczywiste komunikaty (`actualMessages` `Messages` ) są przypisywane z właściwości modelu strony. Sprawdzanie typu jest również wykonywane po przypisaniu komunikatów. Oczekiwane i rzeczywiste komunikaty są porównywane według `Text` ich właściwości. Test potwierdza, że dwa `List<Message>` wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>tej grupie `PageContext` `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> umożliwiajątworzenie<xref:Microsoft.AspNetCore.Mvc.ActionContext> obiektów modelu strony, które obejmują,,,,,, aby określić, a i a. `PageContext` Są one przydatne podczas przeprowadzania testów. Na `ModelState` przykład aplikacja wiadomości nawiązuje błąd z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> programem, aby sprawdzić, czy po <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync` wykonaniu jest zwracany prawidłowy:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Testowanie C# jednostkowe w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testowanie jednostkowe kodu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do korzystania z xUnit.net: Korzystanie z platformy .NET Core z wierszem polecenia zestawu .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [MOQ — Szybki Start](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe Razor Pages aplikacji. Testy warstwy dostępu do danych (DAL) i modele stron pomagają zapewnić:

* Części aplikacji Razor Pages działają niezależnie i razem jako jednostka podczas konstruowania aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy związane z aktualizacjami kodu, są dostępne podczas zautomatyzowanego kompilowania i wdrażania.

W tym temacie założono, że masz podstawową wiedzę na temat Razor Pages aplikacji i testów jednostkowych. Jeśli nie znasz Razor Pages aplikacji lub testów, zapoznaj się z następującymi tematami:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie C# jednostkowe w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *src/RazorPagesTestSample*         | Zezwala użytkownikowi na dodawanie wiadomości, usuwanie jednego komunikatu, usuwanie wszystkich komunikatów i analizowanie komunikatów (Znajdowanie średniej liczby wyrazów na komunikat). |
| Aplikacja testowa    | *tests/RazorPagesTestSample.Tests* | Służy do przetestowania jednostki DAL i indeksu strony aplikacji wiadomości. |

Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub [Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w folderze *Tests/RazorPagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji komunikatów

Aplikacja wiadomości jest systemem komunikatów Razor Pages o następujących cechach:

* Strona indeks aplikacji (*Pages/index. cshtml* i *Pages/index. cshtml. cs*) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (Znajdź średnią liczbę wyrazów na komunikat).
* Komunikat jest opisywany `Message` przez klasę (*Data/Message. cs*) z dwiema właściwościami: `Id` (Key) i `Text` (Message). `Text` Właściwość jest wymagana i jest ograniczona do 200 znaków.
* Komunikaty są przechowywane przy użyciu&#8224; [bazy danych znajdującej się w pamięci Entity Framework](/ef/core/providers/in-memory/).
* Aplikacja zawiera dal w swojej klasie `AppDbContext` kontekstu bazy danych (*Data/AppDbContext. cs*). Metody dal są oznaczone `virtual`, co umożliwia imitację metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów. Te *rozsiane komunikaty* są również używane w testach.

&#8224;W temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory), wyjaśniono, jak korzystać z bazy danych w pamięci dla testów z MSTest. W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) . Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.

Chociaż Przykładowa aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i <xref:mvc/controllers/testing> (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testuj organizację aplikacji

Aplikacja testowa jest aplikacją konsolową wewnątrz folderu *Tests/RazorPagesTestSample. Tests* .

| Testuj folder aplikacji | Opis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla dal.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe dla modelu strony indeksu.</li></ul> |
| *Uruchamiane*     | `TestDbContextOptions` Zawiera metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego dal, aby baza danych była resetowana do jego warunku bazowego dla każdego testu. |

Platforma testowa jest [xUnit](https://xunit.github.io/). Struktura imitacji obiektu jest [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma dal z czterema metodami zawartymi w `AppDbContext` klasie (*src/RazorPagesTestSample/Data/AppDbContext. cs*). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| DAL, Metoda               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje `Text` z bazy danych posortowanej według właściwości. `List<Message>` |
| `AddMessageAsync`        | `Message` Dodaje do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa jeden `Message` z baz danych przez `Id`.                      |

Testy jednostkowe dal wymagają <xref:Microsoft.EntityFrameworkCore.DbContextOptions> podczas tworzenia nowego `AppDbContext` dla każdego testu. Jednym z metod tworzenia `DbContextOptions` dla każdego testu jest <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>użycie:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem polega na tym, że każdy test otrzymuje bazę danych w dowolnym stanie, w którym został pozostawiony poprzedni test. Może to być przyczyną problemów podczas próby zapisania niepodzielnych testów jednostkowych, które nie zakłócają siebie nawzajem. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych dla każdego testu, `DbContextOptions` Podaj wystąpienie, które jest oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak to zrobić za pomocą `Utilities` metody `TestDbContextOptions` klasy (*Tests/RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions` Użycie w ramach testów jednostkowych dal umożliwia niepodzielne uruchamianie każdego testu w przypadku wystąpienia nowego bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa w `DataAccessLayerTest` klasie (*UnitTests/DataAccessLayerTest. cs*) postępuje zgodnie z podobnym wzorcem porządkowania:

1. Ponownego Baza danych jest skonfigurowana do testowania i/lub oczekiwany wynik jest zdefiniowany.
1. Wykonywać Test jest wykonywany.
1. Stanowcz Potwierdzenia są wykonywane w celu ustalenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` Metoda jest odpowiedzialna za usunięcie pojedynczego komunikatu identyfikowanego przez jego `Id` (*src/RazorPagesTestSample/Data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa komunikat, gdy komunikat jest obecny w bazie danych. Druga metoda testuje, że baza danych nie zmienia się, `Id` Jeśli komunikat do usunięcia nie istnieje. Poniżej `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` przedstawiono metodę:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Najpierw Metoda wykonuje krok rozmieszczania, w którym odbywa się przygotowanie do kroku działania. Wypełnianie komunikatów jest uzyskiwane i przechowywane `seedMessages`w. Wypełnianie komunikatów jest zapisywane w bazie danych. Komunikat z programem `Id` `1` jest ustawiany do usunięcia. Gdy metoda jest wykonywana, oczekiwane komunikaty powinny zawierać wszystkie komunikaty z wyjątkiem jednego `Id` z `1`. `DeleteMessageAsync` `expectedMessages` Zmienna reprezentuje oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: Metoda jest wykonywana `recId` w `1`: `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` od kontekstu i porównuje ją `expectedMessages` z potwierdzeniem, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, że te dwa `List<Message>` są takie same:

* Wiadomości są uporządkowane według `Id`.
* Pary komunikatów są porównywane z `Text` właściwością.

Podobna Metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` testowa sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny być równe rzeczywistym komunikatom po `DeleteMessageAsync` wykonaniu metody. Nie powinno się zmieniać zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron indeksu są dostępne w `IndexModel` klasie w *src/RazorPagesTestSample/Pages/index. cshtml. cs*.

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z dal dla interfejsu użytkownika przy użyciu `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, program wywołuje `AddMessageAsync` Dodawanie komunikatu do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołuje `DeleteAllMessagesAsync` usuwanie wszystkich komunikatów w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje `DeleteMessageAsync` , aby usunąć komunikat `Id` z określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli co najmniej jeden komunikat znajduje się w bazie danych, program oblicza średnią liczbę wyrazów na komunikat. |

Metody modelu strony są testowane przy użyciu siedmiu testów w `IndexPageTests` klasie (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy używają znanego wzorca porządkowania i potwierdzeń. Te testy koncentrują się na:

* Określanie, czy metody są zgodne z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowe.
* Potwierdzenie metod daje poprawne <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Sprawdzanie, czy przypisania wartości właściwości są wykonywane poprawnie.

Ta grupa testów często służy do zawzorowania metod DAL, aby generować oczekiwane dane dla kroku działania, w którym jest wykonywana metoda modelu strony. Na przykład `GetMessagesAsync` Metoda `AppDbContext` jest makieta w celu wygenerowania danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, funkcja makieta zwraca wynik. Dane nie pochodzą z bazy danych. Powoduje to utworzenie przewidywalnych, niezawodnych warunków testowania dla korzystania z DAL w testach modelu strony.

Test pokazuje, `GetMessagesAsync` jak metoda jest makietą dla modelu strony: `OnGetAsync_PopulatesThePageModel_WithAListOfMessages`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy metoda jest wykonywana w kroku Act, wywołuje `GetMessagesAsync` metodę modelu strony. `OnGetAsync`

Krok działania testów jednostkowych (*testy/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` Metoda modelu strony (*src/RazorPagesTestSample/Pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` Metoda w dal nie zwraca wyniku dla tego wywołania metody. Makieta wersji metody zwraca wynik.

W tym `Assert` kroku rzeczywiste komunikaty (`actualMessages` `Messages` ) są przypisywane z właściwości modelu strony. Sprawdzanie typu jest również wykonywane po przypisaniu komunikatów. Oczekiwane i rzeczywiste komunikaty są porównywane według `Text` ich właściwości. Test potwierdza, że dwa `List<Message>` wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>tej grupie `PageContext` `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> umożliwiajątworzenie<xref:Microsoft.AspNetCore.Mvc.ActionContext> obiektów modelu strony, które obejmują,,,,,, aby określić, a i a. `PageContext` Są one przydatne podczas przeprowadzania testów. Na `ModelState` przykład aplikacja wiadomości nawiązuje błąd z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> programem, aby sprawdzić, czy po <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync` wykonaniu jest zwracany prawidłowy:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Testowanie C# jednostkowe w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testowanie jednostkowe kodu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do korzystania z xUnit.net: Korzystanie z platformy .NET Core z wierszem polecenia zestawu .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [MOQ — Szybki Start](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
