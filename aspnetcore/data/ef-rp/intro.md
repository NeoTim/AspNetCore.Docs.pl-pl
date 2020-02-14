---
title: Strony razor za pomocą platformy Entity Framework Core w programie ASP.NET Core — samouczek 1 8
author: rick-anderson
description: Pokazuje, jak utworzyć aplikację strony Razor za pomocą platformy Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 1a9d83be9180b1d32ab941932eb3cab8612dff01
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213405"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="7ea68-103">Strony razor za pomocą platformy Entity Framework Core w programie ASP.NET Core — samouczek 1 8</span><span class="sxs-lookup"><span data-stu-id="7ea68-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="7ea68-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ea68-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ea68-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="7ea68-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7ea68-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="7ea68-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7ea68-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7ea68-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="7ea68-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7ea68-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/aspnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="7ea68-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/aspnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7ea68-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-110">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7ea68-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7ea68-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7ea68-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7ea68-112">Prerequisites</span></span>

* <span data-ttu-id="7ea68-113">Jeśli dopiero zaczynasz korzystać z Razor Pages, przejdź do serii samouczek wprowadzenie [do Razor Pages](xref:tutorials/razor-pages/razor-pages-start) , przed rozpoczęciem tej.</span><span class="sxs-lookup"><span data-stu-id="7ea68-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7ea68-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-116">Database engines</span></span>

<span data-ttu-id="7ea68-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="7ea68-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7ea68-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7ea68-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="7ea68-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7ea68-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7ea68-120">Troubleshooting</span></span>

<span data-ttu-id="7ea68-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7ea68-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7ea68-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7ea68-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7ea68-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="7ea68-123">The sample app</span></span>

<span data-ttu-id="7ea68-124">Aplikacja wbudowana w tych samouczkach to podstawowe university witryna sieci web.</span><span class="sxs-lookup"><span data-stu-id="7ea68-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7ea68-125">Użytkownicy mogą przeglądać i aktualizacji dla uczniów, kursu i informacji przez instruktorów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7ea68-126">Oto kilka ekranów, utworzone w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="7ea68-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edytowania uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="7ea68-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7ea68-130">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7ea68-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="7ea68-131">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="7ea68-132">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="7ea68-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="7ea68-133">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ea68-135">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7ea68-136">Usuń trzy pliki i jeden folder, w którym znajduje się nazwa *oprogramowania SQLite* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="7ea68-137">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="7ea68-137">Build the project.</span></span>
* <span data-ttu-id="7ea68-138">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="7ea68-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="7ea68-139">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7ea68-141">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7ea68-142">Usuń *ContosoUniversity. csproj*i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="7ea68-143">Usuń *Startup.cs*i zmień nazwę *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="7ea68-144">Usuń plik *appSettings. JSON*i Zmień nazwę *appSettingsSQLite. JSON* na *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="7ea68-145">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="7ea68-146">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="7ea68-146">Build the project.</span></span>
* <span data-ttu-id="7ea68-147">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7ea68-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="7ea68-148">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="7ea68-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="7ea68-149">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="7ea68-150">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="7ea68-150">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-152">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="7ea68-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7ea68-153">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7ea68-154">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7ea68-155">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="7ea68-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7ea68-156">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea68-158">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="7ea68-159">Uruchom następujące polecenia, aby utworzyć projekt Razor Pages i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7ea68-160">Ustawianie stylów lokacji</span><span class="sxs-lookup"><span data-stu-id="7ea68-160">Set up the site style</span></span>

<span data-ttu-id="7ea68-161">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7ea68-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="7ea68-162">Należy zmienić każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="7ea68-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7ea68-163">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="7ea68-163">There are three occurrences.</span></span>

* <span data-ttu-id="7ea68-164">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów**i **działów**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7ea68-165">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="7ea68-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="7ea68-166">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7ea68-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="7ea68-167">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="7ea68-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7ea68-168">Model danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-168">The data model</span></span>

<span data-ttu-id="7ea68-169">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="7ea68-169">The following sections create a data model:</span></span>

![Diagram modelu danych kurs — rejestracja-ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="7ea68-171">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="7ea68-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7ea68-172">Jednostki dla uczniów</span><span class="sxs-lookup"><span data-stu-id="7ea68-172">The Student entity</span></span>

![Diagram jednostek dla uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="7ea68-174">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="7ea68-175">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7ea68-176">Właściwość `ID` jest kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="7ea68-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7ea68-177">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="7ea68-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7ea68-178">W związku z tym alternatywną automatycznie rozpoznawaną nazwą klucza podstawowego klasy `Student` jest `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="7ea68-179">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7ea68-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7ea68-180">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="7ea68-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7ea68-181">W takim przypadku Właściwość `Enrollments` jednostki `Student` zawiera wszystkie jednostki `Enrollment`, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="7ea68-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7ea68-182">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, właściwość nawigacji `Enrollments` zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7ea68-183">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="7ea68-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7ea68-184">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="7ea68-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7ea68-185">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="7ea68-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7ea68-186">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7ea68-187">Właściwość `Enrollments` jest definiowana jako `ICollection<Enrollment>`, ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7ea68-188">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7ea68-189">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy kolekcję `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7ea68-190">Jednostki rejestracji</span><span class="sxs-lookup"><span data-stu-id="7ea68-190">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="7ea68-192">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7ea68-193">Właściwość `EnrollmentID` jest kluczem podstawowym; Ta jednostka używa wzorca `classnameID`, a nie `ID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7ea68-194">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7ea68-195">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="7ea68-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7ea68-196">Używanie `ID` bez `classname` ułatwia implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7ea68-197">Właściwość `Grade` jest `enum`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7ea68-198">Znak zapytania po deklaracji typu `Grade` wskazuje, że właściwość `Grade` ma [wartość null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="7ea68-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7ea68-199">Klasa o wartości null różni się od klasy zerowej,&mdash;wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="7ea68-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7ea68-200">Właściwość `StudentID` jest kluczem obcym, a odpowiednia właściwość nawigacji jest `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7ea68-201">Jednostka `Enrollment` jest skojarzona z jedną jednostką `Student`, więc Właściwość zawiera jedną jednostkę `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7ea68-202">Właściwość `CourseID` jest kluczem obcym, a odpowiednia właściwość nawigacji jest `Course`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7ea68-203">Jednostka `Enrollment` jest skojarzona z jedną jednostką `Course`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7ea68-204">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7ea68-205">Na przykład`StudentID` jest kluczem obcym dla właściwości nawigacji `Student`, ponieważ klucz podstawowy jednostki `Student` jest `ID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7ea68-206">Właściwości klucza obcego mogą być również nazwane `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7ea68-207">Na przykład `CourseID`, ponieważ klucz podstawowy jednostki `Course` jest `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7ea68-208">Jednostki kursu</span><span class="sxs-lookup"><span data-stu-id="7ea68-208">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="7ea68-210">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7ea68-211">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7ea68-212">Jednostka `Course` może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="7ea68-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7ea68-213">Atrybut `DatabaseGenerated` umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7ea68-214">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7ea68-215">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="7ea68-215">Scaffold Student pages</span></span>

<span data-ttu-id="7ea68-216">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="7ea68-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7ea68-217">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="7ea68-217">An EF Core *context* class.</span></span> <span data-ttu-id="7ea68-218">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7ea68-219">Pochodzi ona z klasy `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="7ea68-220">Strony Razor obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla jednostki `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-222">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="7ea68-223">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7ea68-224">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages przy użyciu Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7ea68-225">W oknie dialogowym **dodawanie Razor Pages przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7ea68-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7ea68-226">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7ea68-227">W wierszu **klasy kontekstu danych** wybierz znak **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="7ea68-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="7ea68-228">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="7ea68-229">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-229">Select **Add**.</span></span>

<span data-ttu-id="7ea68-230">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="7ea68-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea68-232">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="7ea68-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="7ea68-233">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7ea68-234">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7ea68-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7ea68-235">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7ea68-236">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7ea68-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="7ea68-237">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="7ea68-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7ea68-238">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="7ea68-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="7ea68-239">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="7ea68-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="7ea68-240">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7ea68-241">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-241">The scaffolding process:</span></span>

* <span data-ttu-id="7ea68-242">Tworzy strony Razor w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="7ea68-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7ea68-243">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7ea68-244">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7ea68-245">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7ea68-246">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7ea68-247">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7ea68-248">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7ea68-249">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7ea68-250">Dodaje parametry połączenia z bazą danych do pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7ea68-251">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-251">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ea68-253">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7ea68-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="7ea68-254">LocalDB to Uproszczona wersja aparatu programu SQL Server Express bazy danych i jest przeznaczony do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="7ea68-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7ea68-255">Domyślnie LocalDB tworzy pliki *MDF* w katalogu `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7ea68-257">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="7ea68-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7ea68-258">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-258">Update the database context class</span></span>

<span data-ttu-id="7ea68-259">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7ea68-260">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7ea68-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7ea68-261">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7ea68-262">W tym projekcie Klasa ma nazwę `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7ea68-263">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7ea68-264">Wyróżniony kod tworzy właściwość [nieogólnymi\<>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="7ea68-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7ea68-265">W terminologii programu EF Core:</span><span class="sxs-lookup"><span data-stu-id="7ea68-265">In EF Core terminology:</span></span>

* <span data-ttu-id="7ea68-266">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="7ea68-267">Jednostki odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="7ea68-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7ea68-268">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="7ea68-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="7ea68-269">Ponieważ narzędzie tworzenia szkieletów utworzyło`Student` Nieogólnymi, ten krok zmienia go na `Students`w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="7ea68-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="7ea68-270">Aby kod Razor Pages był zgodny z nową nazwą Nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="7ea68-271">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="7ea68-271">There are 8 occurrences.</span></span>

<span data-ttu-id="7ea68-272">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7ea68-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7ea68-273">Startup.cs</span></span>

<span data-ttu-id="7ea68-274">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7ea68-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7ea68-275">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7ea68-276">Składniki, które wymagają tych usług (np. strony Razor) znajdują się tych usług za pomocą parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7ea68-277">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="7ea68-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7ea68-278">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7ea68-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-280">W `ConfigureServices`wyróżnione wiersze zostały dodane przez szkieleter:</span><span class="sxs-lookup"><span data-stu-id="7ea68-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea68-282">W `ConfigureServices`upewnij się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="7ea68-283">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="7ea68-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7ea68-284">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7ea68-285">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-285">Create the database</span></span>

<span data-ttu-id="7ea68-286">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="7ea68-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7ea68-287">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7ea68-288">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="7ea68-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7ea68-289">`EnsureCreated` umożliwia obsługę zmian modelu danych w poniższym przepływie pracy:</span><span class="sxs-lookup"><span data-stu-id="7ea68-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7ea68-290">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-290">Delete the database.</span></span> <span data-ttu-id="7ea68-291">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="7ea68-291">Any existing data is lost.</span></span>
* <span data-ttu-id="7ea68-292">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-292">Change the data model.</span></span> <span data-ttu-id="7ea68-293">Na przykład Dodaj pole `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7ea68-294">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-294">Run the app.</span></span>
* <span data-ttu-id="7ea68-295">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="7ea68-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7ea68-296">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7ea68-297">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="7ea68-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7ea68-298">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="7ea68-299">W dalszej części tego samouczka usuniesz bazę danych, która została utworzona przez `EnsureCreated` i zamiast tego użyj migracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7ea68-300">Bazy danych utworzonej przez `EnsureCreated` nie można zaktualizować za pomocą migracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7ea68-301">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="7ea68-301">Test the app</span></span>

* <span data-ttu-id="7ea68-302">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-302">Run the app.</span></span>
* <span data-ttu-id="7ea68-303">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7ea68-304">Przetestuj Edytuj, szczegóły i usuwać łącza.</span><span class="sxs-lookup"><span data-stu-id="7ea68-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7ea68-305">Inicjowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-305">Seed the database</span></span>

<span data-ttu-id="7ea68-306">Metoda `EnsureCreated` tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7ea68-307">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="7ea68-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7ea68-308">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7ea68-309">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7ea68-310">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7ea68-311">Tworzy dane testowe w tablicach, a nie `List<T>` kolekcje w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="7ea68-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="7ea68-312">W *program.cs*Zastąp wywołanie `EnsureCreated` przy użyciu `DbInitializer.Initialize` wywołania:</span><span class="sxs-lookup"><span data-stu-id="7ea68-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ea68-314">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7ea68-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea68-316">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7ea68-317">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-317">Restart the app.</span></span>

* <span data-ttu-id="7ea68-318">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7ea68-319">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-319">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-321">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7ea68-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7ea68-322">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7ea68-323">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="7ea68-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7ea68-324">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7ea68-325">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="7ea68-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7ea68-326">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak model `Student` jest mapowany na schemat `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="7ea68-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7ea68-328">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="7ea68-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7ea68-329">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7ea68-330">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="7ea68-330">Asynchronous code</span></span>

<span data-ttu-id="7ea68-331">Programowanie asynchroniczne jest to domyślny tryb dla platformy ASP.NET Core i programem EF Core.</span><span class="sxs-lookup"><span data-stu-id="7ea68-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7ea68-332">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach, w dużym obciążeniem wszystkie dostępne wątki mogło zostać użyte.</span><span class="sxs-lookup"><span data-stu-id="7ea68-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7ea68-333">Jeśli tak się stanie, serwer nie może przetworzyć nowe żądania aż wątki są zwalniane.</span><span class="sxs-lookup"><span data-stu-id="7ea68-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7ea68-334">Przy użyciu kodu synchronicznego wiele wątków może powiązane, gdy nie są faktycznie czynności wykonują wszelkie prace ponieważ oczekują one operacji We/Wy zakończyć.</span><span class="sxs-lookup"><span data-stu-id="7ea68-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7ea68-335">Za pomocą kodu asynchronicznego podczas procesu jest oczekiwania na we/wy ukończyć, jego wątku jest zwalniana dla serwera na potrzeby przetwarzaniem innych żądań.</span><span class="sxs-lookup"><span data-stu-id="7ea68-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7ea68-336">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="7ea68-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7ea68-337">Kod asynchroniczny wprowadzić niewielkiej ilości obciążenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="7ea68-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7ea68-338">W sytuacjach o niewielkim ruchu wpływający na wydajność jest nieistotny, podczas w sytuacjach dużego ruchu, potencjalna poprawa wydajności jest znacząca.</span><span class="sxs-lookup"><span data-stu-id="7ea68-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7ea68-339">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` zwracanej wartości, `await` słowo kluczowe i Metoda `ToListAsync` sprawia, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7ea68-340">Słowo kluczowe `async` instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="7ea68-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7ea68-341">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="7ea68-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7ea68-342">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="7ea68-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7ea68-343">Typ zwracany `Task<T>` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="7ea68-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7ea68-344">Słowo kluczowe `await` powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="7ea68-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7ea68-345">Pierwsza część kończy się za pomocą operacji, który jest uruchamiany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7ea68-346">Druga część zostanie przełączone do metody wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7ea68-347">`ToListAsync` jest asynchroniczną wersją metody rozszerzenia `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7ea68-348">Kilka rzeczy, w których trzeba pamiętać podczas pisania kodu asynchronicznego, który używa programu EF Core:</span><span class="sxs-lookup"><span data-stu-id="7ea68-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7ea68-349">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7ea68-350">Obejmuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`i `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7ea68-351">Nie zawiera on instrukcji, które po prostu zmieniają `IQueryable`, takie jak `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7ea68-352">Z kontekstu programu EF Core nie jest bezpieczny dla wątków: nie należy próbować wykonać wiele operacji wykonywane równolegle.</span><span class="sxs-lookup"><span data-stu-id="7ea68-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7ea68-353">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7ea68-354">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7ea68-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7ea68-355">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="7ea68-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7ea68-356">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="7ea68-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ea68-357">Przykładową aplikację sieci web firmy Contoso University pokazuje, jak utworzyć aplikację stron Razor programu ASP.NET Core przy użyciu Core Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="7ea68-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="7ea68-358">Przykładowa aplikacja jest witryną sieci web dla uniwersytetu fikcyjnej firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="7ea68-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7ea68-359">Obejmuje funkcje, takie jak czasowej dla uczniów, tworzenia kurs i przypisania instruktora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7ea68-360">Ta strona jest to pierwszy z serii samouczków, które opisują sposób tworzenia przykładowej aplikacji Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7ea68-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="7ea68-361">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-361">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7ea68-362">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7ea68-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7ea68-363">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7ea68-363">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="7ea68-366">Znajomość [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="7ea68-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="7ea68-367">Nowi programiści powinni zakończyć pracę [z Razor Pages](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="7ea68-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7ea68-368">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7ea68-368">Troubleshooting</span></span>

<span data-ttu-id="7ea68-369">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7ea68-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7ea68-370">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7ea68-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="7ea68-371">Aplikacja sieci web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="7ea68-371">The Contoso University web app</span></span>

<span data-ttu-id="7ea68-372">Aplikacja wbudowana w tych samouczkach to podstawowe university witryna sieci web.</span><span class="sxs-lookup"><span data-stu-id="7ea68-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="7ea68-373">Użytkownicy mogą przeglądać i aktualizacji dla uczniów, kursu i informacji przez instruktorów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7ea68-374">Oto kilka ekranów, utworzone w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="7ea68-374">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edytowania uczniów](intro/_static/student-edit.png)

<span data-ttu-id="7ea68-377">Styl interfejsu użytkownika w tej lokacji znajduje się w pobliżu co to jest generowany przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="7ea68-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="7ea68-378">Samouczek koncentruje się na programu EF Core ze stronami Razor, nie interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7ea68-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="7ea68-379">Tworzenie aplikacji sieci web ContosoUniversity stron Razor</span><span class="sxs-lookup"><span data-stu-id="7ea68-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-381">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="7ea68-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7ea68-382">Tworzenie nowej aplikacji sieci Web platformy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ea68-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="7ea68-383">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="7ea68-384">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="7ea68-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="7ea68-385">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="7ea68-386">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie aplikacji sieci Web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="7ea68-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="7ea68-387">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-387">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7ea68-389">Ustawianie stylów lokacji</span><span class="sxs-lookup"><span data-stu-id="7ea68-389">Set up the site style</span></span>

<span data-ttu-id="7ea68-390">Kilka zmian, skonfiguruj w menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="7ea68-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="7ea68-391">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="7ea68-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="7ea68-392">Należy zmienić każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="7ea68-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7ea68-393">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="7ea68-393">There are three occurrences.</span></span>

* <span data-ttu-id="7ea68-394">Dodaj pozycje menu dla **studentów**, **kursów**, **instruktorów**i **działów**, a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="7ea68-395">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="7ea68-395">The changes are highlighted.</span></span> <span data-ttu-id="7ea68-396">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="7ea68-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="7ea68-397">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7ea68-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="7ea68-398">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-398">Create the data model</span></span>

<span data-ttu-id="7ea68-399">Tworzenie klas jednostek dla aplikacji Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7ea68-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="7ea68-400">Uruchom przy użyciu następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="7ea68-400">Start with the following three entities:</span></span>

![Diagram modelu danych kurs — rejestracja-ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="7ea68-402">Istnieje relacja jeden do wielu między jednostkami `Student` i `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="7ea68-403">Istnieje relacja jeden do wielu między jednostkami `Course` i `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="7ea68-404">Uczniem/uczennicą mogą rejestrować się w dowolnej liczby kursów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="7ea68-405">Kurs może mieć dowolną liczbę uczniów zarejestrowane w nim.</span><span class="sxs-lookup"><span data-stu-id="7ea68-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="7ea68-406">W poniższych sekcjach tworzenia klasy dla każdego z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="7ea68-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="7ea68-407">Jednostki dla uczniów</span><span class="sxs-lookup"><span data-stu-id="7ea68-407">The Student entity</span></span>

![Diagram jednostek dla uczniów](intro/_static/student-entity.png)

<span data-ttu-id="7ea68-409">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-409">Create a *Models* folder.</span></span> <span data-ttu-id="7ea68-410">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="7ea68-411">Właściwość `ID` jest kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="7ea68-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="7ea68-412">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="7ea68-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7ea68-413">W `classnameID`, `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="7ea68-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="7ea68-414">Alternatywny automatycznie rozpoznany klucz podstawowy jest `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="7ea68-415">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7ea68-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7ea68-416">Właściwości nawigacji link do innych jednostek, które są powiązane z tej jednostki.</span><span class="sxs-lookup"><span data-stu-id="7ea68-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="7ea68-417">W takim przypadku Właściwość `Enrollments` `Student entity` zawiera wszystkie jednostki `Enrollment`, które są powiązane z tym `Student`em.</span><span class="sxs-lookup"><span data-stu-id="7ea68-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="7ea68-418">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, właściwość nawigacji `Enrollments` zawiera te dwa jednostki `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="7ea68-419">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w kolumnie `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="7ea68-420">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w tabeli `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="7ea68-421">Tabela `Enrollment` ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="7ea68-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="7ea68-422">`StudentID` jest kluczem obcym w tabeli `Enrollment`, który określa student w tabeli `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="7ea68-423">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, takim jak `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="7ea68-424">można określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="7ea68-425">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy kolekcję `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="7ea68-426">Właściwości nawigacji, które zawierają wiele jednostek pochodzą z relacji wiele do wielu i jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="7ea68-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="7ea68-427">Jednostki rejestracji</span><span class="sxs-lookup"><span data-stu-id="7ea68-427">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="7ea68-429">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="7ea68-430">Właściwość `EnrollmentID` jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="7ea68-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="7ea68-431">Ta jednostka używa wzorca `classnameID`, a nie `ID`, takiego jak `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="7ea68-432">Zwykle programiści wybierz jednym ze wzorców i używać go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="7ea68-433">Przy użyciu Identyfikatora bez classname jest wyświetlany później w samouczku, aby ułatwić implementują dziedziczenie w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="7ea68-434">Właściwość `Grade` jest `enum`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7ea68-435">Znak zapytania po deklaracji typu `Grade` wskazuje, że właściwość `Grade` ma wartość null.</span><span class="sxs-lookup"><span data-stu-id="7ea68-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="7ea68-436">Ma wartość null, która różni się od zera klasy korporacyjnej — wartość null oznacza, że wartość nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="7ea68-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7ea68-437">Właściwość `StudentID` jest kluczem obcym, a odpowiednia właściwość nawigacji jest `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7ea68-438">Jednostka `Enrollment` jest skojarzona z jedną jednostką `Student`, więc Właściwość zawiera jedną jednostkę `Student`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="7ea68-439">Jednostka `Student` różni się od właściwości nawigacji `Student.Enrollments`, która zawiera wiele jednostek `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="7ea68-440">Właściwość `CourseID` jest kluczem obcym, a odpowiednia właściwość nawigacji jest `Course`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7ea68-441">Jednostka `Enrollment` jest skojarzona z jedną jednostką `Course`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7ea68-442">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7ea68-443">Na przykład`StudentID` właściwość nawigacji `Student`, ponieważ klucz podstawowy jednostki `Student` jest `ID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7ea68-444">Właściwości klucza obcego mogą być również nazwane `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7ea68-445">Na przykład `CourseID`, ponieważ klucz podstawowy jednostki `Course` jest `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="7ea68-446">Jednostki kursu</span><span class="sxs-lookup"><span data-stu-id="7ea68-446">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="7ea68-448">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="7ea68-449">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7ea68-450">Jednostka `Course` może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="7ea68-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7ea68-451">Atrybut `DatabaseGenerated` umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="7ea68-452">Tworzenie szkieletu modelu dla uczniów</span><span class="sxs-lookup"><span data-stu-id="7ea68-452">Scaffold the student model</span></span>

<span data-ttu-id="7ea68-453">W tej sekcji jest szkieletu modelu dla uczniów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="7ea68-454">Oznacza to, że narzędzie do tworzenia szkieletów tworzy strony dla operacji tworzenia, odczytu, aktualizowania lub usuwania (CRUD) dla modelu dla uczniów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="7ea68-455">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="7ea68-455">Build the project.</span></span>
* <span data-ttu-id="7ea68-456">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea68-458">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7ea68-459">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages przy użyciu Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="7ea68-460">Wypełnij okno dialogowe **dodawanie Razor Pages przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7ea68-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7ea68-461">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="7ea68-462">W wierszu **klasy kontekstu danych** wybierz znak **+** (plus) i Zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="7ea68-463">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="7ea68-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="7ea68-464">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-464">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="7ea68-466">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="7ea68-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7ea68-468">Uruchom następujące polecenia, aby utworzyć szkielet modelu dla uczniów.</span><span class="sxs-lookup"><span data-stu-id="7ea68-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="7ea68-469">Proces szkieletu tworzonych i zmienianych następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="7ea68-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7ea68-470">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="7ea68-470">Files created</span></span>

* <span data-ttu-id="7ea68-471">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="7ea68-472">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="7ea68-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="7ea68-473">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="7ea68-473">File updates</span></span>

* <span data-ttu-id="7ea68-474">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="7ea68-475">*appSettings. JSON* : dodano parametry połączenia używane do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7ea68-476">Badanie kontekstu zarejestrowane przy użyciu iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="7ea68-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7ea68-477">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7ea68-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7ea68-478">Usługi (takie jak kontekst bazy danych programu EF Core) zostały zarejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7ea68-479">Składniki, które wymagają tych usług (np. strony Razor) znajdują się tych usług za pomocą parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7ea68-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7ea68-480">Kod konstruktora, który pobiera wystąpienia kontekstu bazy danych jest przedstawiony w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="7ea68-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7ea68-481">Narzędzie do tworzenia szkieletów automatycznie tworzone kontekst bazy danych i jest on zarejestrowany za pomocą kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7ea68-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7ea68-482">Przejrzyj metodę `ConfigureServices` w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea68-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="7ea68-483">Wyróżniony wiersz został dodany w procesie tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="7ea68-484">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="7ea68-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7ea68-485">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="7ea68-486">Zaktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="7ea68-486">Update main</span></span>

<span data-ttu-id="7ea68-487">W *program.cs*zmień metodę `Main`, aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="7ea68-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7ea68-488">Pobierz wystąpienia kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7ea68-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7ea68-489">Wywołaj [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="7ea68-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="7ea68-490">Usuń kontekst, gdy metoda `EnsureCreated` zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="7ea68-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="7ea68-491">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="7ea68-492">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="7ea68-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="7ea68-493">Jeśli istnieje, nie podjęto żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-493">If it exists, no action is taken.</span></span> <span data-ttu-id="7ea68-494">Jeśli nie istnieje, bazy danych i jego schematu są tworzone.</span><span class="sxs-lookup"><span data-stu-id="7ea68-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="7ea68-495">`EnsureCreated` nie korzysta z migracji, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="7ea68-496">Bazy danych utworzonej przy użyciu `EnsureCreated` nie można później zaktualizować za pomocą migracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="7ea68-497">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="7ea68-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="7ea68-498">Usuwanie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-498">Delete the DB.</span></span>
* <span data-ttu-id="7ea68-499">Zmień schemat bazy danych (na przykład Dodaj pole `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="7ea68-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="7ea68-500">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="7ea68-500">Run the app.</span></span>
* <span data-ttu-id="7ea68-501">`EnsureCreated` tworzy bazę danych z kolumną`EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="7ea68-502">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat szybko się rozwija.</span><span class="sxs-lookup"><span data-stu-id="7ea68-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="7ea68-503">Później w samouczku baza danych zostanie usunięty i migracje są używane.</span><span class="sxs-lookup"><span data-stu-id="7ea68-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7ea68-504">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="7ea68-504">Test the app</span></span>

<span data-ttu-id="7ea68-505">Uruchom aplikację i zaakceptować zasady plików cookie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="7ea68-506">Ta aplikacja nie zachowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="7ea68-507">Informacje o zasadach dotyczących plików cookie można znaleźć na stronie [pomocy technicznej ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="7ea68-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="7ea68-508">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="7ea68-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7ea68-509">Przetestuj Edytuj, szczegóły i usuwać łącza.</span><span class="sxs-lookup"><span data-stu-id="7ea68-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="7ea68-510">Badanie kontekstu SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="7ea68-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="7ea68-511">Główna klasa, która służy do koordynowania funkcji EF Core dla modelu danych jest z klasy kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="7ea68-512">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7ea68-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7ea68-513">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7ea68-514">W tym projekcie Klasa ma nazwę `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7ea68-515">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="7ea68-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="7ea68-516">Wyróżniony kod tworzy właściwość [nieogólnymi\<>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="7ea68-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7ea68-517">W terminologii programu EF Core:</span><span class="sxs-lookup"><span data-stu-id="7ea68-517">In EF Core terminology:</span></span>

* <span data-ttu-id="7ea68-518">Zwykle zestaw jednostek odnosi się do tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="7ea68-519">Jednostki odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="7ea68-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7ea68-520">nie można pominąć `DbSet<Enrollment>` i `DbSet<Course>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="7ea68-521">EF Core obejmuje te niejawnie, ponieważ jednostka `Student` odwołuje się do jednostki `Enrollment`, a jednostka `Enrollment` odwołuje się do jednostki `Course`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="7ea68-522">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="7ea68-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7ea68-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7ea68-524">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7ea68-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="7ea68-525">LocalDB to Uproszczona wersja aparatu programu SQL Server Express bazy danych i jest przeznaczony do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="7ea68-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7ea68-526">LocalDB rozpoczyna się na żądanie i działa w trybie użytkownika, więc nie ma żadnych złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7ea68-527">Domyślnie LocalDB tworzy pliki *MDF* DB w katalogu `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="7ea68-528">Dodaj kod, aby zainicjować bazy danych przy użyciu danych testowych</span><span class="sxs-lookup"><span data-stu-id="7ea68-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="7ea68-529">EF Core tworzy pustą bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="7ea68-530">W tej części Metoda `Initialize` jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="7ea68-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="7ea68-531">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="7ea68-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="7ea68-532">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw (`namespace ContosoUniversity.Models`), a nie `Data`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="7ea68-533">`Models` jest spójna z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="7ea68-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="7ea68-534">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="7ea68-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="7ea68-535">Kod sprawdza, czy wszystkie studentów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="7ea68-536">W przypadku nie studentów w bazie danych, baza danych jest inicjowany z danych testowych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="7ea68-537">Ładuje dane testowe do tablic, a nie `List<T>` kolekcje w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="7ea68-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7ea68-538">Metoda `EnsureCreated` automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="7ea68-539">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="7ea68-540">W *program.cs*zmień metodę `Main`, aby wywołać `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7ea68-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea68-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea68-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ea68-542">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7ea68-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea68-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea68-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea68-544">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="7ea68-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="7ea68-545">Widok bazy danych</span><span class="sxs-lookup"><span data-stu-id="7ea68-545">View the DB</span></span>

<span data-ttu-id="7ea68-546">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="7ea68-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="7ea68-547">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="7ea68-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="7ea68-548">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7ea68-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="7ea68-549">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7ea68-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="7ea68-550">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="7ea68-551">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="7ea68-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="7ea68-552">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="7ea68-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="7ea68-553">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="7ea68-553">Asynchronous code</span></span>

<span data-ttu-id="7ea68-554">Programowanie asynchroniczne jest to domyślny tryb dla platformy ASP.NET Core i programem EF Core.</span><span class="sxs-lookup"><span data-stu-id="7ea68-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7ea68-555">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach, w dużym obciążeniem wszystkie dostępne wątki mogło zostać użyte.</span><span class="sxs-lookup"><span data-stu-id="7ea68-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7ea68-556">Jeśli tak się stanie, serwer nie może przetworzyć nowe żądania aż wątki są zwalniane.</span><span class="sxs-lookup"><span data-stu-id="7ea68-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7ea68-557">Przy użyciu kodu synchronicznego wiele wątków może powiązane, gdy nie są faktycznie czynności wykonują wszelkie prace ponieważ oczekują one operacji We/Wy zakończyć.</span><span class="sxs-lookup"><span data-stu-id="7ea68-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7ea68-558">Za pomocą kodu asynchronicznego podczas procesu jest oczekiwania na we/wy ukończyć, jego wątku jest zwalniana dla serwera na potrzeby przetwarzaniem innych żądań.</span><span class="sxs-lookup"><span data-stu-id="7ea68-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7ea68-559">W rezultacie kod asynchroniczny umożliwia bardziej efektywne wykorzystanie zasobów serwera i serwera jest włączona, aby obsłużyć większy ruch, bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="7ea68-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="7ea68-560">Kod asynchroniczny wprowadzić niewielkiej ilości obciążenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="7ea68-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7ea68-561">W sytuacjach o niewielkim ruchu wpływający na wydajność jest nieistotny, podczas w sytuacjach dużego ruchu, potencjalna poprawa wydajności jest znacząca.</span><span class="sxs-lookup"><span data-stu-id="7ea68-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7ea68-562">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` zwracanej wartości, `await` słowo kluczowe i Metoda `ToListAsync` sprawia, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="7ea68-563">Słowo kluczowe `async` instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="7ea68-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7ea68-564">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="7ea68-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7ea68-565">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="7ea68-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="7ea68-566">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7ea68-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="7ea68-567">Niejawny typ zwracany `Task` reprezentuje trwającą prace.</span><span class="sxs-lookup"><span data-stu-id="7ea68-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="7ea68-568">Słowo kluczowe `await` powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="7ea68-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7ea68-569">Pierwsza część kończy się za pomocą operacji, który jest uruchamiany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7ea68-570">Druga część zostanie przełączone do metody wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="7ea68-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7ea68-571">`ToListAsync` jest asynchroniczną wersją metody rozszerzenia `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7ea68-572">Kilka rzeczy, w których trzeba pamiętać podczas pisania kodu asynchronicznego, który używa programu EF Core:</span><span class="sxs-lookup"><span data-stu-id="7ea68-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7ea68-573">Tylko te instrukcje, które powodują zapytań i poleceń do wysłania do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7ea68-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="7ea68-574">Obejmuje to, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`i `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7ea68-575">Nie zawiera on instrukcji, które po prostu zmieniają `IQueryable`, takie jak `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7ea68-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7ea68-576">Z kontekstu programu EF Core nie jest bezpieczny dla wątków: nie należy próbować wykonać wiele operacji wykonywane równolegle.</span><span class="sxs-lookup"><span data-stu-id="7ea68-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7ea68-577">Aby móc korzystać z zalet wydajności kod asynchroniczny, sprawdź, czy biblioteka pakietów (np. stronicowania) używają asynchronicznej, jeśli wywołują metod programu EF Core, które wysyłają zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7ea68-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="7ea68-578">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7ea68-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="7ea68-579">W następnym samouczku basic CRUD (Tworzenie, odczytywanie, aktualizowanie, usuwanie) są badane.</span><span class="sxs-lookup"><span data-stu-id="7ea68-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="7ea68-580">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7ea68-580">Additional resources</span></span>

* [<span data-ttu-id="7ea68-581">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="7ea68-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="7ea68-582">Dalej</span><span class="sxs-lookup"><span data-stu-id="7ea68-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
