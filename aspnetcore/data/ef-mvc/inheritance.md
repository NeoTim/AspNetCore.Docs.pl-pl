---
title: 'Samouczek: implementowanie dziedziczenia-ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono sposób implementowania dziedziczenia w modelu danych przy użyciu Entity Framework Core w aplikacji ASP.NET Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: c10df60a43f5d59f3ce13afd38aad42b88c80516
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259401"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="d4f27-103">Samouczek: implementowanie dziedziczenia-ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="d4f27-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="d4f27-104">W poprzednim samouczku zostały obsłużone wyjątki współbieżności.</span><span class="sxs-lookup"><span data-stu-id="d4f27-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="d4f27-105">W tym samouczku pokazano, jak zaimplementować dziedziczenie w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="d4f27-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="d4f27-106">W programowaniu zorientowanym obiektowo można użyć dziedziczenia, aby ułatwić ponowne użycie kodu.</span><span class="sxs-lookup"><span data-stu-id="d4f27-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="d4f27-107">W tym samouczku zmienisz klasy `Instructor` i `Student`, aby pochodzą z klasy bazowej `Person`, która zawiera właściwości, takie jak `LastName`, które są wspólne dla instruktorów i studentów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="d4f27-108">Nie dodasz ani nie zmienisz żadnych stron sieci Web, ale zmienisz część kodu, a zmiany zostaną automatycznie odzwierciedlone w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="d4f27-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="d4f27-109">W tym samouczku zostaną wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d4f27-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d4f27-110">Dziedziczenie mapowania do bazy danych</span><span class="sxs-lookup"><span data-stu-id="d4f27-110">Map inheritance to database</span></span>
> * <span data-ttu-id="d4f27-111">Tworzenie klasy Person</span><span class="sxs-lookup"><span data-stu-id="d4f27-111">Create the Person class</span></span>
> * <span data-ttu-id="d4f27-112">Aktualizuj instruktora i uczniów</span><span class="sxs-lookup"><span data-stu-id="d4f27-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="d4f27-113">Dodawanie osoby do modelu</span><span class="sxs-lookup"><span data-stu-id="d4f27-113">Add Person to the model</span></span>
> * <span data-ttu-id="d4f27-114">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="d4f27-114">Create and update migrations</span></span>
> * <span data-ttu-id="d4f27-115">Testowanie implementacji</span><span class="sxs-lookup"><span data-stu-id="d4f27-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4f27-116">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d4f27-116">Prerequisites</span></span>

* [<span data-ttu-id="d4f27-117">Obsługa współbieżności</span><span class="sxs-lookup"><span data-stu-id="d4f27-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="d4f27-118">Dziedziczenie mapowania do bazy danych</span><span class="sxs-lookup"><span data-stu-id="d4f27-118">Map inheritance to database</span></span>

<span data-ttu-id="d4f27-119">Klasy `Instructor` i `Student` w modelu danych szkolnych mają kilka właściwości, które są identyczne:</span><span class="sxs-lookup"><span data-stu-id="d4f27-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Klasy uczniów i instruktorów](inheritance/_static/no-inheritance.png)

<span data-ttu-id="d4f27-121">Załóżmy, że chcesz wyeliminować nadmiarowy kod dla właściwości, które są współużytkowane przez jednostki `Instructor` i `Student`.</span><span class="sxs-lookup"><span data-stu-id="d4f27-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="d4f27-122">Lub chcesz napisać usługę, która może formatować nazwy bez Caring, niezależnie od tego, czy nazwa pochodzi od instruktora, czy studenta.</span><span class="sxs-lookup"><span data-stu-id="d4f27-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="d4f27-123">Można utworzyć klasę bazową `Person`, która zawiera tylko te właściwości udostępnione, a następnie uczynić klasy `Instructor` i `Student` dziedziczone z tej klasy bazowej, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="d4f27-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Klasy uczniów i instruktorów wyprowadzane z klasy Person](inheritance/_static/inheritance.png)

<span data-ttu-id="d4f27-125">Istnieje kilka sposobów reprezentowania tej struktury dziedziczenia w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="d4f27-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="d4f27-126">Może istnieć tabela osób, która zawiera informacje o studentach i instruktorach w pojedynczej tabeli.</span><span class="sxs-lookup"><span data-stu-id="d4f27-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="d4f27-127">Niektóre kolumny mogą dotyczyć tylko instruktorów (HireDate), niektórych tylko do uczniów (EnrollmentDate), niektórych do obu (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="d4f27-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="d4f27-128">Zazwyczaj masz kolumnę rozróżniacza, aby wskazać, który typ reprezentuje każdy wiersz.</span><span class="sxs-lookup"><span data-stu-id="d4f27-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="d4f27-129">Na przykład kolumna rozróżniacza może mieć "instruktor" dla instruktorów i "Student" dla studentów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Przykład tabeli na hierarchię](inheritance/_static/tph.png)

<span data-ttu-id="d4f27-131">Ten wzorzec generowania struktury dziedziczenia jednostek z pojedynczej tabeli bazy danych jest nazywany dziedziczeniem na hierarchię (TPH).</span><span class="sxs-lookup"><span data-stu-id="d4f27-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="d4f27-132">Alternatywą jest, aby baza danych wyglądała podobnie jak struktura dziedziczenia.</span><span class="sxs-lookup"><span data-stu-id="d4f27-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="d4f27-133">Na przykład można mieć tylko pola nazw w tabeli Person i mieć osobne tabele instruktorów i uczniów z polami daty.</span><span class="sxs-lookup"><span data-stu-id="d4f27-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Dziedziczenie dla typu tabeli](inheritance/_static/tpt.png)

<span data-ttu-id="d4f27-135">Ten wzorzec tworzenia tabeli bazy danych dla każdej klasy jednostek jest nazywany dziedziczeniem tabeli na typ (TPT).</span><span class="sxs-lookup"><span data-stu-id="d4f27-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="d4f27-136">Jeszcze kolejną opcją jest zamapowanie wszystkich typów nieabstrakcyjnych na poszczególne tabele.</span><span class="sxs-lookup"><span data-stu-id="d4f27-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="d4f27-137">Wszystkie właściwości klasy, łącznie z dziedziczonymi właściwościami, mapują do kolumn odpowiedniej tabeli.</span><span class="sxs-lookup"><span data-stu-id="d4f27-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="d4f27-138">Ten wzorzec jest nazywany dziedziczeniem klasy opartej na tabeli (TPC).</span><span class="sxs-lookup"><span data-stu-id="d4f27-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="d4f27-139">W przypadku zaimplementowania dziedziczenia testowego dla osoby, ucznia i klas instruktorów, jak pokazano wcześniej, w tabelach student i instruktor nie będą one wyglądały inaczej po wdrożeniu dziedziczenia niż wcześniej.</span><span class="sxs-lookup"><span data-stu-id="d4f27-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="d4f27-140">Wzorce TPHa i dziedziczenia zapewniają lepszą wydajność niż wzorce dziedziczenia TPT, ponieważ wzorce TPT mogą powodować złożone zapytania sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="d4f27-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="d4f27-141">W tym samouczku pokazano, jak wdrożyć dziedziczenie TPH.</span><span class="sxs-lookup"><span data-stu-id="d4f27-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="d4f27-142">TPH jest jedynym wzorcem dziedziczenia obsługiwanym przez Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d4f27-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="d4f27-143">Co należy zrobić, aby utworzyć klasę `Person`, Zmień klasy `Instructor` i `Student`, aby pochodne od `Person` dodać nową klasę do `DbContext` i utworzyć migrację.</span><span class="sxs-lookup"><span data-stu-id="d4f27-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="d4f27-144">Rozważ zapisanie kopii projektu przed wprowadzeniem następujących zmian.</span><span class="sxs-lookup"><span data-stu-id="d4f27-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="d4f27-145">Jeśli wystąpią problemy i trzeba zacząć od nowa, będzie łatwiej zacząć od zapisanego projektu zamiast odwracania kroków wykonanych dla tego samouczka lub powrotu do początku całej serii.</span><span class="sxs-lookup"><span data-stu-id="d4f27-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="d4f27-146">Tworzenie klasy Person</span><span class="sxs-lookup"><span data-stu-id="d4f27-146">Create the Person class</span></span>

<span data-ttu-id="d4f27-147">W folderze modele Utwórz Person.cs i Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d4f27-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="d4f27-148">Aktualizuj instruktora i uczniów</span><span class="sxs-lookup"><span data-stu-id="d4f27-148">Update Instructor and Student</span></span>

<span data-ttu-id="d4f27-149">W *Instructor.cs*, pochodny klasy instruktora od klasy Person i Usuń pola klucza i nazwy.</span><span class="sxs-lookup"><span data-stu-id="d4f27-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="d4f27-150">Kod będzie wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="d4f27-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="d4f27-151">Wprowadź te same zmiany w programie *student.cs*.</span><span class="sxs-lookup"><span data-stu-id="d4f27-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="d4f27-152">Dodawanie osoby do modelu</span><span class="sxs-lookup"><span data-stu-id="d4f27-152">Add Person to the model</span></span>

<span data-ttu-id="d4f27-153">Dodaj typ jednostki osoby do *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="d4f27-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="d4f27-154">Nowe wiersze są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="d4f27-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="d4f27-155">To wszystko, co Entity Framework potrzebuje w celu skonfigurowania dziedziczenia w hierarchii na poziomie tabeli.</span><span class="sxs-lookup"><span data-stu-id="d4f27-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="d4f27-156">Jak widać, gdy baza danych zostanie zaktualizowana, będzie miała tabelę osób zamiast tabel uczniów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="d4f27-157">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="d4f27-157">Create and update migrations</span></span>

<span data-ttu-id="d4f27-158">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="d4f27-158">Save your changes and build the project.</span></span> <span data-ttu-id="d4f27-159">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d4f27-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="d4f27-160">Nie uruchamiaj jeszcze polecenia `database update`.</span><span class="sxs-lookup"><span data-stu-id="d4f27-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="d4f27-161">To polecenie spowoduje utratę danych, ponieważ spowoduje porzucenie tabeli instruktora i zmianę nazwy tabeli uczniów na osobę.</span><span class="sxs-lookup"><span data-stu-id="d4f27-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="d4f27-162">Musisz podać niestandardowy kod, aby zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="d4f27-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="d4f27-163">Otwórz przystawkę *migracje/\<timestamp > _Inheritance. cs* i Zastąp metodę `Up` następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d4f27-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="d4f27-164">Ten kod obejmuje następujące zadania aktualizacji bazy danych:</span><span class="sxs-lookup"><span data-stu-id="d4f27-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="d4f27-165">Usuwa ograniczenia klucza obcego i indeksy wskazujące na tabelę uczniów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="d4f27-166">Zmienia nazwę tabeli instruktora jako osobę i wprowadza zmiany, które są niezbędne do przechowywania danych ucznia:</span><span class="sxs-lookup"><span data-stu-id="d4f27-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="d4f27-167">Dodaje wartość null EnrollmentDate dla studentów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="d4f27-168">Dodaje kolumnę rozróżniacza, aby wskazać, czy wiersz dotyczy studenta, czy instruktora.</span><span class="sxs-lookup"><span data-stu-id="d4f27-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="d4f27-169">Sprawia, że HireDate dopuszcza wartość null, ponieważ wiersze uczniów nie mają dat zatrudnienia.</span><span class="sxs-lookup"><span data-stu-id="d4f27-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="d4f27-170">Dodaje pole tymczasowe, które będzie używane do aktualizacji kluczy obcych, które wskazują uczniów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="d4f27-171">W przypadku kopiowania uczniów do tabeli osób zostaną pobrane nowe wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="d4f27-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="d4f27-172">Kopiuje dane z tabeli uczniów do tabeli Person.</span><span class="sxs-lookup"><span data-stu-id="d4f27-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="d4f27-173">Powoduje to, że uczniowie mogą uzyskać przypisane nowe wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="d4f27-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="d4f27-174">Naprawia wartości kluczy obcych, które wskazują uczniów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="d4f27-175">Ponownie tworzy ograniczenia klucza obcego i indeksy, teraz wskazując je do tabeli Person.</span><span class="sxs-lookup"><span data-stu-id="d4f27-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="d4f27-176">(Jeśli jako typ klucza podstawowego użyto identyfikatora GUID zamiast Integer, wartości klucza podstawowego studenta nie będą musiały ulec zmianie, a niektóre z tych kroków mogły zostać pominięte).</span><span class="sxs-lookup"><span data-stu-id="d4f27-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="d4f27-177">Uruchom polecenie `database update`:</span><span class="sxs-lookup"><span data-stu-id="d4f27-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="d4f27-178">(W systemie produkcyjnym należy wprowadzić odpowiednie zmiany w metodzie `Down` na wypadek, gdyby kiedykolwiek było to możliwe, aby wrócić do poprzedniej wersji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="d4f27-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="d4f27-179">W tym samouczku nie będziesz używać metody `Down`.)</span><span class="sxs-lookup"><span data-stu-id="d4f27-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="d4f27-180">Podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane, można uzyskać inne błędy.</span><span class="sxs-lookup"><span data-stu-id="d4f27-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="d4f27-181">W przypadku wystąpienia błędów migracji, których nie można rozwiązać, można zmienić nazwę bazy danych w parametrach połączenia lub usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="d4f27-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="d4f27-182">W przypadku nowej bazy danych nie ma żadnych danych do migracji, a polecenie Update-Database może być gotowe do ukończenia bez błędów.</span><span class="sxs-lookup"><span data-stu-id="d4f27-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="d4f27-183">Aby usunąć bazę danych, należy użyć SSOX-0 interfejsu wiersza polecenia @no__t programu.</span><span class="sxs-lookup"><span data-stu-id="d4f27-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="d4f27-184">Testowanie implementacji</span><span class="sxs-lookup"><span data-stu-id="d4f27-184">Test the implementation</span></span>

<span data-ttu-id="d4f27-185">Uruchom aplikację i wypróbuj różne strony.</span><span class="sxs-lookup"><span data-stu-id="d4f27-185">Run the app and try various pages.</span></span> <span data-ttu-id="d4f27-186">Wszystko działa tak samo jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="d4f27-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="d4f27-187">W **Eksplorator obiektów SQL Server**rozwiń węzeł **połączenia danych/SchoolContext** , a następnie **tabele**i sprawdź, czy tabele student i instruktor zostały zastąpione przez tabelę Person.</span><span class="sxs-lookup"><span data-stu-id="d4f27-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="d4f27-188">Otwórz projektanta tabeli Person i sprawdź, czy zawiera on wszystkie kolumny, które zostały użyte w tabelach studenta i instruktora.</span><span class="sxs-lookup"><span data-stu-id="d4f27-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabela osób w SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="d4f27-190">Kliknij prawym przyciskiem myszy tabelę osoba, a następnie kliknij polecenie **Pokaż dane tabeli** , aby wyświetlić kolumnę rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="d4f27-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabela osób w SSOX — dane tabeli](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="d4f27-192">Uzyskaj kod</span><span class="sxs-lookup"><span data-stu-id="d4f27-192">Get the code</span></span>

[<span data-ttu-id="d4f27-193">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="d4f27-193">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="d4f27-194">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d4f27-194">Additional resources</span></span>

<span data-ttu-id="d4f27-195">Aby uzyskać więcej informacji na temat dziedziczenia w Entity Framework Core, zobacz [dziedziczenie](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="d4f27-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d4f27-196">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="d4f27-196">Next steps</span></span>

<span data-ttu-id="d4f27-197">W tym samouczku zostaną wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d4f27-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d4f27-198">Mapowane dziedziczenie do bazy danych</span><span class="sxs-lookup"><span data-stu-id="d4f27-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="d4f27-199">Utworzono klasę Person</span><span class="sxs-lookup"><span data-stu-id="d4f27-199">Created the Person class</span></span>
> * <span data-ttu-id="d4f27-200">Zaktualizowany instruktor i student</span><span class="sxs-lookup"><span data-stu-id="d4f27-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="d4f27-201">Dodano osobę do modelu</span><span class="sxs-lookup"><span data-stu-id="d4f27-201">Added Person to the model</span></span>
> * <span data-ttu-id="d4f27-202">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="d4f27-202">Created and update migrations</span></span>
> * <span data-ttu-id="d4f27-203">Przetestowano implementację</span><span class="sxs-lookup"><span data-stu-id="d4f27-203">Tested the implementation</span></span>

<span data-ttu-id="d4f27-204">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać wiele relatywnie zaawansowanych scenariuszy Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="d4f27-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d4f27-205">Dalej: Tematy zaawansowane</span><span class="sxs-lookup"><span data-stu-id="d4f27-205">Next: Advanced topics</span></span>](advanced.md)
