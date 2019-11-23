---
title: Pracuj z bazą danych i ASP.NET Core
author: rick-anderson
description: Wyjaśnia, jak pracować z bazą danych i ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334227"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Pracuj z bazą danych i ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i [Audette Jan](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z bazą danych i mapowania obiektów `Movie` do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie `ConfigureServices` w *Startup.cs*:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`. W przypadku lokalnego projektowania pobiera parametry połączenia z pliku *appSettings. JSON* .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Wartość nazwy dla bazy danych (`Database={Database name}`) będzie różna dla wygenerowanego kodu. Wartość nazwy jest dowolną.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może być używana do ustawiania parametrów połączenia na rzeczywistym serwerze bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB rozpoczyna się na żądanie i działa w trybie użytkownika, więc nie ma żadnych złożonej konfiguracji. Domyślnie baza danych LocalDB tworzy pliki `*.mdf` w katalogu `C:\Users\<user>\`.

<a name="ssox"></a>
* Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym przyciskiem myszy tabelę `Movie` i wybierz polecenie **Projektant widoków**:

  ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/design.png)

  ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

Zwróć uwagę na ikonę klucza obok `ID`. Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.

* Kliknij prawym przyciskiem myszy tabelę `Movie` i wybierz polecenie **Wyświetl dane**:

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Inicjowanie bazy danych

Utwórz nową klasę o nazwie `SeedData` w folderze *modele* o następującym kodzie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodawanie inicjatora inicjatora

W *Program.cs*, zmodyfikuj `Main` metodę, aby wykonać następujące czynności:

* Pobierz wystąpienia kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę inicjatora, przekazując ją do kontekstu.
* Usuń kontekst, gdy metoda inicjatora zostanie zakończona.

Poniższy kod przedstawia zaktualizowanego *Program.cs* pliku.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy z bazy danych. Można to zrobić za pomocą linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Wymuś inicjalizację aplikacji (wywołaj metody z klasy `Startup`), aby była uruchamiana Metoda inicjatora. Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie. Można to zrobić przy użyciu następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * W przypadku uruchamiania programu VS w trybie innym niż debugowanie naciśnij klawisz F5, aby uruchomić polecenie w trybie debugowania.
    * W przypadku uruchamiania programu VS w trybie debugowania Zatrzymaj debuger i naciśnij klawisz F5.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy z bazy danych (w związku z czym zostanie uruchomiona Metoda inicjatora). Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

Aplikacja pokazuje dane z rozrzutu.

---

Następny samouczek poprawi prezentację danych.

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzedni: Razor Pages szkieletowe](xref:tutorials/razor-pages/page)
> [Dalej: aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z bazą danych i mapowania obiektów `Movie` do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie `ConfigureServices` w *Startup.cs*:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Aby uzyskać więcej informacji na temat metod używanych w `ConfigureServices`, zobacz:

* [Obsługa ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core](xref:security/gdpr) dla `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`. W przypadku lokalnego projektowania pobiera parametry połączenia z pliku *appSettings. JSON* .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Wartość nazwy dla bazy danych (`Database={Database name}`) będzie różna dla wygenerowanego kodu. Wartość nazwy jest dowolną.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może być używana do ustawiania parametrów połączenia na rzeczywistym serwerze bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB rozpoczyna się na żądanie i działa w trybie użytkownika, więc nie ma żadnych złożonej konfiguracji. Domyślnie baza danych LocalDB tworzy pliki `*.mdf` w katalogu `C:/Users/<user/>`.

<a name="ssox"></a>
* Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym przyciskiem myszy tabelę `Movie` i wybierz polecenie **Projektant widoków**:

  ![Menu kontekstowe jest otwarte w tabeli filmów](sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](sql/_static/dv.png)

Zwróć uwagę na ikonę klucza obok `ID`. Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.

* Kliknij prawym przyciskiem myszy tabelę `Movie` i wybierz polecenie **Wyświetl dane**:

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Inicjowanie bazy danych

Utwórz nową klasę o nazwie `SeedData` w folderze *modele* o następującym kodzie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodawanie inicjatora inicjatora

W *Program.cs*, zmodyfikuj `Main` metodę, aby wykonać następujące czynności:

* Pobierz wystąpienia kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę inicjatora, przekazując ją do kontekstu.
* Usuń kontekst, gdy metoda inicjatora zostanie zakończona.

Poniższy kod przedstawia zaktualizowanego *Program.cs* pliku.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Aplikacja produkcyjna nie wywoła `Database.Migrate`. Jest on dodawany do poprzedniego kodu, aby zapobiec następującym wyjątkom, gdy `Update-Database` nie został uruchomiony:

SqlException: nie można otworzyć bazy danych "RazorPagesMovieContext-21" żądanej przez nazwę logowania. Logowanie nie powiodło się.
Logowanie użytkownika "Nazwa użytkownika" nie powiodło się.

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy z bazy danych. Można to zrobić za pomocą linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Wymuś inicjalizację aplikacji (wywołaj metody z klasy `Startup`), aby była uruchamiana Metoda inicjatora. Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie. Można to zrobić przy użyciu następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * W przypadku uruchamiania programu VS w trybie innym niż debugowanie naciśnij klawisz F5, aby uruchomić polecenie w trybie debugowania.
    * W przypadku uruchamiania programu VS w trybie debugowania Zatrzymaj debuger i naciśnij klawisz F5.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Usuń wszystkie rekordy z bazy danych (w związku z czym zostanie uruchomiona Metoda inicjatora). Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

Aplikacja pokazuje dane z rozrzutu.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Usuń wszystkie rekordy z bazy danych (w związku z czym zostanie uruchomiona Metoda inicjatora). Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

Aplikacja pokazuje dane z rozrzutu.

---

Aplikacja pokazuje dane z rozrzutu:

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55.png)

Następny samouczek czyści prezentację danych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Poprzedni: Razor Pages szkieletowe](xref:tutorials/razor-pages/page)
> [Dalej: aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end
