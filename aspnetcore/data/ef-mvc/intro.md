---
title: 'Samouczek: Wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC'
description: Jest to pierwsza z szeregu samouczków, które wyjaśniają, jak skompilować przykładową aplikację firmy Contoso University od podstaw.
author: tdykstra
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 1b68c20ba206a5afe36f307525879f91d03d95d1
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583340"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Samouczek: Wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji sieci Web ASP.NET Core 2,2 MVC przy użyciu Entity Framework (EF) Core 2,2 i Visual Studio 2017 lub 2019.

Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University. Obejmuje funkcje, takie jak czasowej dla uczniów, tworzenia kurs i przypisania instruktora. Jest to pierwsza z szeregu samouczków, które wyjaśniają, jak skompilować przykładową aplikację firmy Contoso University od podstaw.

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Tworzenie aplikacji sieci Web ASP.NET Core MVC
> * Ustawianie stylów lokacji
> * Dowiedz się więcej o EF Core pakietach NuGet
> * Tworzenie modelu danych
> * Tworzenie kontekstu bazy danych
> * Rejestrowanie kontekstu dla iniekcji zależności
> * Zainicjuj bazę danych z danymi testowymi
> * Tworzenie kontrolera i widoków
> * Wyświetlanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

* [.NET Core SDK 2.2](https://www.microsoft.com/net/download)
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:
  * **ASP.NET i programowanie aplikacji sieci Web**
  * **Tworzenie aplikacji dla wielu platform w środowisku .NET Core**

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz problem, nie można rozpoznać ogólnie można znaleźć rozwiązania, porównując swój kod, aby [projektu ukończona](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Aby zapoznać się z listą typowych błędów i sposobu ich rozwiązywania, zobacz [sekcję Rozwiązywanie problemów w ostatnim samouczku w serii](advanced.md#common-errors). Jeśli nie możesz znaleźć tego, czego potrzebujesz, możesz ogłosić pytanie do StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Jest to seria 10 samouczków, z których każdy jest oparty na tym, co zostało zrobione we wcześniejszych samouczkach. Rozważ zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka. Jeśli wystąpią problemy, możesz zacząć od poprzedniego samouczka zamiast wrócić do początku całej serii.

## <a name="contoso-university-web-app"></a>Aplikacja sieci Web firmy Contoso University

Aplikacja, którą tworzysz w tych samouczkach, jest prostą witryną sieci Web.

Użytkownicy mogą przeglądać i aktualizacji dla uczniów, kursu i informacji przez instruktorów. Poniżej przedstawiono kilka ekranów, które zostaną utworzone.

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edytowania uczniów](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

* Otwórz program Visual Studio.

* Z menu **plik** wybierz pozycję **Nowy projekt >** .

* W okienku po lewej stronie wybierz pozycję **zainstalowane > C# Visual > Web**.

* Wybierz szablon projektu **aplikacji sieci Web ASP.NET Core** .

* Wprowadź **ContosoUniversity** jako nazwę, a następnie kliknij przycisk **OK**.

  ![Okno dialogowe nowego projektu](intro/_static/new-project2.png)

* Zaczekaj, aż pojawi się okno dialogowe **Nowa aplikacja sieci Web ASP.NET Core** .

* Wybierz pozycję **.NET Core**, **ASP.NET Core 2,2** i szablon **aplikacji sieci Web (Model-View-Controller)** .

* Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.

* Wybierz **przycisk OK**

  ![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Ustawianie stylów lokacji

Kilka prostych zmian spowoduje skonfigurowanie menu witryny, układu i strony głównej.

Otwórz *Widok widoki/Shared/_Layout. cshtml* i wprowadź następujące zmiany:

* Należy zmienić każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso". Istnieją trzy wystąpienia.

* Dodaj pozycje menu dla **informacji o**programie, **studentów**, **kursy**, **Instruktorzy**i **działy**, a następnie usuń wpis menu **prywatność** .

Zmiany są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

W obszarze *widoki/Home/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu. Zostanie wyświetlona strona główna z kartami dla stron, które zostaną utworzone w tych samouczkach.

![Strona główna firmy Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informacje o pakietach NuGet EF Core

Aby dodać obsługę EF Core do projektu, zainstaluj dostawcę bazy danych, który ma być celem. Ten samouczek używa SQL Server, a pakiet dostawcy to [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie musisz się odwoływać do pakietu.

Pakiet EF SQL Server i jego zależności (`Microsoft.EntityFrameworkCore` oraz `Microsoft.EntityFrameworkCore.Relational`) zapewniają obsługę środowiska uruchomieniowego dla EF. Pakiet narzędzi zostanie dodany później, w samouczku [migracji](migrations.md) .

Aby uzyskać informacje o innych dostawcach baz danych, które są dostępne dla Entity Framework Core, zobacz [dostawcy bazy danych](/ef/core/providers/).

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Następnie utworzysz klasy jednostek dla aplikacji firmy Contoso University. Zaczniesz od następujących trzech jednostek.

![Diagram modelu danych kurs — rejestracja-ucznia](intro/_static/data-model-diagram.png)

`Student` Istnieje relacja jeden do wielu między jednostkami i `Enrollment` i istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` . Innymi słowy, student może być zarejestrowany w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę uczniów zarejestrowanych w nim.

W poniższych sekcjach utworzysz klasę dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostki dla uczniów

![Diagram jednostek dla uczniów](intro/_static/student-entity.png)

W folderze *modele* Utwórz plik klasy o nazwie *student.cs* i Zastąp kod szablonu poniższym kodem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` Właściwość stanie się kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy. Domyślnie Entity Framework interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.

`Enrollments` Właściwość [właściwość nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką. W tym przypadku `Enrollments` Właściwość `Enrollment` a `Student entity` będzie zawierać wszystkie jednostki, które są powiązane z tą `Student` jednostką. Innymi słowy, jeśli dany wiersz ucznia w bazie danych ma dwa powiązane wiersze rejestracji (wiersze zawierające wartość klucza podstawowego tego ucznia w kolumnie klucza obcego StudentID), właściwość `Student` `Enrollments` nawigacji tej jednostki będzie zawierać te dwie `Enrollment` jednostki.

Jeśli właściwość nawigacji może zawierać wiele jednostek (tak jak w przypadku relacji "wiele do wielu" lub "jeden do wielu"), jej typem musi być lista, w której można dodawać, usuwać i aktualizować wpisy, na przykład `ICollection<T>`. Możesz określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`. Jeśli określisz `ICollection<T>`, EF domyślnie `HashSet<T>` tworzy kolekcję.

### <a name="the-enrollment-entity"></a>Jednostki rejestracji

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

W folderze *modele* Utwórz *Enrollment.cs* i Zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Właściwość będzie kluczem podstawowym; ta jednostka `classnameID` używa wzorca zamiast `ID` samego siebie, jak pokazano w `Student` jednostce. `EnrollmentID` Zwykle należy wybrać jeden wzorzec i używać go w całym modelu danych. Tutaj, odmiana ilustruje, że można użyć dowolnego wzorca. W [późniejszym samouczku](inheritance.md)zobaczysz, jak używać identyfikatora bez ClassName, ułatwia implementowanie dziedziczenia w modelu danych.

`Grade` Właściwość `enum`. Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość ma wartość null. Ma wartość null, która różni się od zera klasy korporacyjnej — wartość null oznacza, że wartość nie jest znana lub nie została jeszcze przypisana.

`StudentID` Właściwość to klucz obcy i odpowiednią właściwość nawigacji jest `Student`. `Student` `Student.Enrollments` `Enrollment` Jednostka jest skojarzona z jedną `Student` jednostką, więc właściwość może zawierać tylko jedną jednostkę (w przeciwieństwie do wytoczonej wcześniej właściwości nawigacji, która może zawierać wiele jednostek). `Enrollment`

`CourseID` Właściwość to klucz obcy i odpowiednią właściwość nawigacji jest `Course`. `Enrollment` Jednostka jest skojarzony z jednym `Course` jednostki.

Entity Framework interpretuje właściwość jako właściwość klucza obcego, `<navigation property name><primary key property name>` jeśli jest nazwana (na przykład dla `Student` właściwości `StudentID` nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID`). Właściwości klucza obcego mogą być również nazywane po `<primary key property name>` prostu (na przykład `CourseID` , ponieważ `Course` klucz podstawowy jednostki to `CourseID`).

### <a name="the-course-entity"></a>Jednostki kursu

![Diagram jednostek kursu](intro/_static/course-entity.png)

W folderze *modele* Utwórz *Course.cs* i Zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` Właściwość jest właściwością nawigacji. A `Course` jednostki mogą być one związane z dowolną liczbę `Enrollment` jednostek.

Dowiesz się więcej na temat `DatabaseGenerated` atrybutu w [kolejnym samouczku](complex-data-model.md) w tej serii. Zasadniczo ten atrybut umożliwia wprowadzenie klucza podstawowego dla kursu, a nie jego wygenerowanie.

## <a name="create-the-database-context"></a>Tworzenie kontekstu bazy danych

Klasa główna, która koordynuje funkcje Entity Framework dla danego modelu danych, jest klasą kontekstu bazy danych. Tę klasę można utworzyć, wyprowadzając ją `Microsoft.EntityFrameworkCore.DbContext` z klasy. W kodzie możesz określić, które jednostki zostaną uwzględnione w modelu danych. Można również dostosować pewne zachowanie Entity Framework. W tym projekcie nosi nazwę klasy `SchoolContext`.

W folderze projektu Utwórz folder o nazwie *dane*.

W folderze *dane* Utwórz nowy plik klasy o nazwie *SchoolContext.cs*i Zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ten kod tworzy `DbSet` właściwość dla każdego zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

Można pominąć `DbSet<Enrollment>` instrukcje i `DbSet<Course>` i będzie on działał tak samo. Entity Framework będzie zawierać `Student` je niejawnie, ponieważ jednostka odwołuje się `Enrollment` do jednostki `Enrollment` , a jednostka `Course` odwołuje się do jednostki.

Po utworzeniu bazy danych EF tworzy tabele, które mają nazwy takie same jak `DbSet` nazwy właściwości. Nazwy właściwości dla kolekcji są zwykle plural (studenci zamiast uczniów), ale deweloperzy zgadzają się na to, czy nazwy tabel powinny być wyrzucane. Te samouczki zastąpią domyślne zachowanie, określając pojedyncze nazwy tabel w kontekście DbContext. Aby to zrobić, Dodaj następujący wyróżniony kod po ostatniej właściwości Nieogólnymi.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zarejestruj SchoolContext

ASP.NET Core domyślnie implementuje [iniekcję zależności](../../fundamentals/dependency-injection.md) . Usługi (takie jak kontekst bazy danych EF) są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak kontrolery MVC), są dostarczane przez parametry konstruktora. Zobaczysz kod konstruktora kontrolera, który pobiera wystąpienie kontekstu w dalszej części tego samouczka.

Aby zarejestrować `SchoolContext` się jako usługa, Otwórz *Startup.cs*i Dodaj `ConfigureServices` wyróżnione wiersze do metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w `DbContextOptionsBuilder` obiekcie. Na potrzeby lokalnego programowania dla [systemu konfiguracji platformy ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

Dodaj `using` instrukcje dla `ContosoUniversity.Data` i `Microsoft.EntityFrameworkCore` przestrzeni nazw, a następnie Skompiluj projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otwórz plik *appSettings. JSON* i Dodaj parametry połączenia, jak pokazano w poniższym przykładzie.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają SQL Server bazy danych LocalDB. LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym. LocalDB rozpoczyna się na żądanie i działa w trybie użytkownika, więc nie ma żadnych złożonej konfiguracji. Domyślnie LocalDB tworzy pliki bazy danych *MDF* w `C:/Users/<user>` katalogu.

## <a name="initialize-db-with-test-data"></a>Zainicjuj bazę danych z danymi testowymi

Entity Framework utworzy pustą bazę danych. W tej sekcji napiszesz metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnienia jej danymi testowymi.

W tym miejscu będziesz używać `EnsureCreated` metody do automatycznego tworzenia bazy danych. W [późniejszym samouczku](migrations.md) zobaczysz, jak obsłużyć zmiany modelu przy użyciu migracje Code First, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.

W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Zastąp kod szablonu następującym kodem, co spowoduje utworzenie bazy danych w razie potrzeby i załadowanie danych testowych do nowej bazy danych.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kod sprawdza, czy w bazie danych znajdują się uczniowie i czy nie, zakłada, że baza danych jest nowa i należy ją umieścić w danych testowych. Ładuje dane testowe do tablic zamiast `List<T>` kolekcje w celu zoptymalizowania wydajności.

W *program.cs*Zmień `Main` metodę, aby wykonać następujące czynności podczas uruchamiania aplikacji:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę inicjatora, przekazując ją do kontekstu.
* Usuń kontekst, gdy metoda inicjatora jest gotowa.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Dodaj `using` instrukcje:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

W starszych samouczkach można zobaczyć podobny kod w `Configure` metodzie w *Startup.cs*. Zalecamy użycie `Configure` metody tylko w celu skonfigurowania potoku żądania. Kod uruchamiania aplikacji należy do `Main` metody.

Teraz podczas pierwszego uruchomienia aplikacji baza danych zostanie utworzona i zostanie zainicjowana przy użyciu danych testowych. Za każdym razem, gdy zmieniasz model danych, możesz usunąć bazę danych, zaktualizować metodę inicjatora i zacząć od nowa baza danych w taki sam sposób. W kolejnych samouczkach zobaczysz, jak zmodyfikować bazę danych, gdy zmieni się model danych, bez usuwania i ponownego tworzenia.

## <a name="create-controller-and-views"></a>Tworzenie kontrolera i widoków

Następnie użyjesz aparatu tworzenia szkieletów w programie Visual Studio, aby dodać kontroler MVC i widoki, które będą używać EF do wykonywania zapytań i zapisywania danych.

Automatyczne tworzenie metod i widoków akcji CRUD jest znane jako rusztowania. Tworzenie szkieletu różni się od generowania kodu, ponieważ kod szkieletowy jest punktem wyjścia, który można zmodyfikować zgodnie z własnymi wymaganiami, ale zazwyczaj nie jest modyfikowany kod wygenerowany. Jeśli musisz dostosować wygenerowany kod, użyj klas częściowych lub ponownie Wygeneruj kod, gdy zmienią się.

* Kliknij prawym przyciskiem myszy folder controllers w **Eksplorator rozwiązań** a następnie wybierz pozycję **Dodaj > nowy element szkieletowy**.

* W oknie dialogowym **Dodawanie szkieletu** :

  * Wybierz **kontroler MVC z widokami, używając Entity Framework**.

  * Kliknij przycisk **Dodaj**. Zostanie wyświetlone okno dialogowe **Dodawanie kontrolera MVC z widokami, przy użyciu Entity Framework** .

    ![Student dla szkieletu](intro/_static/scaffold-student2.png)

  * W obszarze **Klasa modelu** wybierz **ucznia**.

  * W obszarze **Klasa kontekstu danych** wybierz pozycję **SchoolContext**.

  * Zaakceptuj domyślną **StudentsController** jako nazwę.

  * Kliknij przycisk **Dodaj**.

  Po kliknięciu przycisku **Dodaj**aparat szkieletu programu Visual Studio tworzy plik *StudentsController.cs* i zestaw widoków (pliki *. cshtml* ), które współpracują z kontrolerem.

(Aparat tworzenia szkieletów może również utworzyć kontekst bazy danych, jeśli nie utworzysz go ręcznie, jak wcześniej w tym samouczku. Aby określić nową klasę kontekstu w polu **Dodaj kontroler** , kliknij znak plus z prawej strony **klasy kontekstu danych**.  Następnie program Visual Studio utworzy `DbContext` klasę, a także kontroler i widoki.

Zauważ, że kontroler przyjmuje `SchoolContext` jako parametr konstruktora.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core iniekcja zależności ma zadbać o przekazanie `SchoolContext` wystąpienia do kontrolera. Wcześniej skonfigurowano plik *Startup.cs* .

Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych. Metoda pobiera listę studentów z zestawu jednostek studentów, odczytując `Students` Właściwość wystąpienia kontekstu bazy danych:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Dowiesz się więcej na temat asynchronicznych elementów programowania w tym kodzie w dalszej części tego samouczka.

Widok *widoki/uczniowie/index. cshtml* wyświetla tę listę w tabeli:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.

Kliknij kartę studenci, aby zobaczyć dane testowe, które `DbInitializer.Initialize` zostały wstawione przez metodę. W zależności od tego, jak Zawężasz okno przeglądarki, zobaczysz `Students` link do karty w górnej części strony lub kliknij ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.

![Strona główna firmy Contoso University Narrow](intro/_static/home-page-narrow.png)

![Strona indeksu uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a>Wyświetlanie bazy danych

Po uruchomieniu aplikacji `DbInitializer.Initialize` metoda wywołuje `EnsureCreated`metodę. EF wykryto, że nie istniała baza danych i dlatego została utworzona, a następnie pozostała część `Initialize` kodu metody wypełnił bazę danych danymi. Aby wyświetlić bazę danych w programie Visual Studio, można użyć **Eksplorator obiektów SQL Server** (SSOX).

Zamknij przeglądarkę.

Jeśli okno SSOX nie jest jeszcze otwarte, wybierz je z menu **Widok** w programie Visual Studio.

W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych**, a następnie kliknij wpis dla nazwy bazy danych znajdującej się w parametrach połączenia w pliku *appSettings. JSON* .

Rozwiń węzeł **tabele** , aby wyświetlić tabele w bazie danych.

![Tabele w SSOX](intro/_static/ssox-tables.png)

Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze, które zostały wstawione do tabeli.

![Tabela uczniów w SSOX](intro/_static/ssox-student-table.png)

Pliki *. mdf* i *. ldf* znajdują się w folderze *C:\Users\\\<yourUserName >* .

Ponieważ wywołujesz `EnsureCreated` metodę inicjatora, która jest uruchamiana podczas uruchamiania aplikacji, możesz teraz wprowadzić zmianę `Student` klasy, usunąć bazę danych, ponownie uruchomić aplikację, a baza danych zostanie automatycznie utworzona ponownie w celu dopasowania do zmiany. Na przykład, jeśli dodasz `EmailAddress` Właściwość `Student` do klasy, zobaczysz nową `EmailAddress` kolumnę w nowo utworzonej tabeli.

## <a name="conventions"></a>Konwencje

Ilość kodu, który miał zostać zapisany w celu Entity Framework być w stanie utworzyć kompletną bazę danych, jest minimalny ze względu na stosowanie Konwencji lub zaEntity Framework łożeń.

* Nazwy `DbSet` właściwości są używane jako nazwy tabel. W przypadku jednostek, do których `DbSet` nie odwołuje się właściwość, nazwy klas jednostek są używane jako nazwy tabel.

* Nazwy właściwości jednostki są używane w nazwach kolumn.

* Właściwości jednostki o nazwach ID lub classnameID są rozpoznawane jako właściwości klucza podstawowego.

* Właściwość jest interpretowana jako właściwość klucza obcego, jeśli nazwa właściwości nawigacji jest nazywana `StudentID`  *\<>\<nazwą właściwości klucza podstawowego >* (na przykład dla `Student` właściwości nawigacji od `Student`klucz podstawowy jednostki to `ID`). Właściwości klucza obcego mogą być również nazwane  *\<nazwą właściwości klucza podstawowego >* (na `Enrollment` przykład, `EnrollmentID` ponieważ klucz podstawowy jednostki to `EnrollmentID`).

Zachowanie konwencjonalne można zastąpić. Na przykład można jawnie określić nazwy tabel, jak zostało to opisane wcześniej w tym samouczku. I można ustawić nazwy kolumn i ustawić dowolną właściwość jako klucz podstawowy lub klucz obcy, jak widać w późniejszym samouczku [](complex-data-model.md) w tej serii.

## <a name="asynchronous-code"></a>Kod asynchroniczny

Programowanie asynchroniczne jest to domyślny tryb dla platformy ASP.NET Core i programem EF Core.

Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach, w dużym obciążeniem wszystkie dostępne wątki mogło zostać użyte. Jeśli tak się stanie, serwer nie może przetworzyć nowe żądania aż wątki są zwalniane. Przy użyciu kodu synchronicznego wiele wątków może powiązane, gdy nie są faktycznie czynności wykonują wszelkie prace ponieważ oczekują one operacji We/Wy zakończyć. Za pomocą kodu asynchronicznego podczas procesu jest oczekiwania na we/wy ukończyć, jego wątku jest zwalniana dla serwera na potrzeby przetwarzaniem innych żądań. W rezultacie kod asynchroniczny umożliwia bardziej efektywne wykorzystanie zasobów serwera i serwera jest włączona, aby obsłużyć większy ruch, bez opóźnień.

Kod asynchroniczny wprowadza niewielką ilość narzutu w czasie wykonywania, ale w przypadku niskiego natężenia ruchu, gdy wydajność jest niewielka, w przypadku dużych sytuacji związanych z ruchem jest istotna poprawa wydajności.

W poniższym kodzie `async` słowo kluczowe, `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Słowo kluczowe instruuje kompilator, aby generował wywołania zwrotne dla części treści metody i automatycznie `Task<IActionResult>` utworzyć zwracany obiekt. `async`

* Typ `Task<IActionResult>` zwracany reprezentuje bieżącą współpracę z wynikiem typu `IActionResult`.

* `await` — Słowo kluczowe powoduje, że kompilator podzielić metodę na dwie części. Pierwsza część kończy się za pomocą operacji, który jest uruchamiany asynchronicznie. Druga część zostanie przełączone do metody wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.

* `ToListAsync` jest to wersja asynchroniczna elementu `ToList` — metoda rozszerzenia.

Niektóre kwestie, o których należy wiedzieć, gdy piszesz kod asynchroniczny, który używa Entity Framework:

* Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje to, na przykład `ToListAsync` `SingleOrDefaultAsync`,, i `SaveChangesAsync`. Nie zawiera na przykład instrukcji, które po prostu zmieniają element `IQueryable`, taki jak. `var students = context.Students.Where(s => s.LastName == "Davolio")`

* Kontekst EF nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji. Gdy wywoływana jest metoda async EF, zawsze używaj `await` słowa kluczowego.

* Jeśli chcesz wykorzystać zalety wydajności w kodzie asynchronicznym, upewnij się, że wszystkie używane pakiety biblioteki (na przykład na potrzeby stronicowania) używają również metody asynchronicznej, jeśli wywołują wszelkie Entity Framework metod, które powodują wysłanie zapytań do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [asynchroniczne Omówienie](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Pobierz kod

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Utworzono ASP.NET Core aplikacji sieci Web MVC
> * Ustawianie stylów lokacji
> * Informacje o EF Core pakietach NuGet
> * Utworzono model danych
> * Utworzono kontekst bazy danych
> * Zarejestrowano SchoolContext
> * Zainicjowano bazę danych z danymi testowymi
> * Utworzono kontroler i widoki
> * Wyświetlanie bazy danych

W poniższym samouczku dowiesz się, jak wykonywać operacje podstawowe CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).

Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonywać podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).

> [!div class="nextstepaction"]
> [Zaimplementuj podstawowe funkcje CRUD](crud.md)
