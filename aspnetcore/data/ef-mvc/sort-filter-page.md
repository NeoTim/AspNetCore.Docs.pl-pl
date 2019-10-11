---
title: 'Samouczek: Dodawanie sortowania, filtrowania i stronicowania — ASP.NET MVC z EF Core'
description: W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania na stronie indeksu uczniów. Utworzysz również stronę, która będzie prostą grupowaniem.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: c4d50b72c5508d52b17c6754b6d8e77c1a3903b6
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259345"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="87b7f-104">Samouczek: Dodawanie sortowania, filtrowania i stronicowania — ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="87b7f-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="87b7f-105">W poprzednim samouczku zaimplementowano zestaw stron sieci Web dla podstawowych operacji CRUD dla jednostek uczniów.</span><span class="sxs-lookup"><span data-stu-id="87b7f-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="87b7f-106">W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania na stronie indeksu uczniów.</span><span class="sxs-lookup"><span data-stu-id="87b7f-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="87b7f-107">Utworzysz również stronę, która będzie prostą grupowaniem.</span><span class="sxs-lookup"><span data-stu-id="87b7f-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="87b7f-108">Na poniższej ilustracji przedstawiono, jak będzie wyglądać strona po zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="87b7f-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="87b7f-109">Nagłówki kolumn to linki, które użytkownik może kliknąć, aby posortować według tej kolumny.</span><span class="sxs-lookup"><span data-stu-id="87b7f-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="87b7f-110">Wielokrotne kliknięcie nagłówka kolumny powoduje przełączenie między rosnącą i malejącą kolejnością sortowania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

<span data-ttu-id="87b7f-112">W tym samouczku zostaną wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="87b7f-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="87b7f-113">Dodaj linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="87b7f-113">Add column sort links</span></span>
> * <span data-ttu-id="87b7f-114">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="87b7f-114">Add a Search box</span></span>
> * <span data-ttu-id="87b7f-115">Dodaj stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="87b7f-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="87b7f-116">Dodaj stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="87b7f-116">Add paging to Index method</span></span>
> * <span data-ttu-id="87b7f-117">Dodaj linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="87b7f-117">Add paging links</span></span>
> * <span data-ttu-id="87b7f-118">Tworzenie strony informacje</span><span class="sxs-lookup"><span data-stu-id="87b7f-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="87b7f-119">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="87b7f-119">Prerequisites</span></span>

* [<span data-ttu-id="87b7f-120">Implementowanie funkcji CRUD</span><span class="sxs-lookup"><span data-stu-id="87b7f-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="87b7f-121">Dodaj linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="87b7f-121">Add column sort links</span></span>

<span data-ttu-id="87b7f-122">Aby dodać sortowanie na stronie indeksu ucznia, należy zmienić metodę `Index` kontrolera uczniów i dodać kod do widoku indeksu studenta.</span><span class="sxs-lookup"><span data-stu-id="87b7f-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="87b7f-123">Dodawanie funkcji sortowania do metody index</span><span class="sxs-lookup"><span data-stu-id="87b7f-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="87b7f-124">W *StudentsController.cs*Zastąp metodę `Index` następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="87b7f-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="87b7f-125">Ten kod odbiera parametr `sortOrder` z ciągu zapytania w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="87b7f-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="87b7f-126">Wartość ciągu zapytania jest dostarczana przez ASP.NET Core MVC jako parametr do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="87b7f-127">Parametr będzie ciągiem "name" lub "date", opcjonalnie po znaku podkreślenia i ciągu "DESC" w celu określenia kolejności malejącej.</span><span class="sxs-lookup"><span data-stu-id="87b7f-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="87b7f-128">Domyślna kolejność sortowania to Ascending.</span><span class="sxs-lookup"><span data-stu-id="87b7f-128">The default sort order is ascending.</span></span>

<span data-ttu-id="87b7f-129">Podczas pierwszego żądania strony indeksu nie ma ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="87b7f-130">Studenci są wyświetlani w kolejności rosnącej według nazwiska, która jest wartością domyślną ustanowioną przez przypadek przypadania w instrukcji `switch`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="87b7f-131">Gdy użytkownik kliknie hiperlink nagłówka kolumny, odpowiednia wartość `sortOrder` jest podawana w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="87b7f-132">Dwa elementy `ViewData` (NameSortParm i DateSortParm) są używane przez widok do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="87b7f-133">Są to "Trzyelementowy".</span><span class="sxs-lookup"><span data-stu-id="87b7f-133">These are ternary statements.</span></span> <span data-ttu-id="87b7f-134">Pierwszy z nich określa, że jeśli parametr `sortOrder` ma wartość null lub jest pusty, NameSortParm powinna być ustawiona na "name_desc"; w przeciwnym razie należy ustawić pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="87b7f-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="87b7f-135">Te dwie instrukcje umożliwiają widokowi ustawienie hiperłączy nagłówka kolumny w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="87b7f-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="87b7f-136">Bieżący porządek sortowania</span><span class="sxs-lookup"><span data-stu-id="87b7f-136">Current sort order</span></span>  | <span data-ttu-id="87b7f-137">Hiperłącze nazwisko</span><span class="sxs-lookup"><span data-stu-id="87b7f-137">Last Name Hyperlink</span></span> | <span data-ttu-id="87b7f-138">Hiperłącze daty</span><span class="sxs-lookup"><span data-stu-id="87b7f-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="87b7f-139">Nazwisko — rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-139">Last Name ascending</span></span>  | <span data-ttu-id="87b7f-140">Najważniejsze</span><span class="sxs-lookup"><span data-stu-id="87b7f-140">descending</span></span>          | <span data-ttu-id="87b7f-141">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-141">ascending</span></span>      |
| <span data-ttu-id="87b7f-142">Nazwisko malejąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-142">Last Name descending</span></span> | <span data-ttu-id="87b7f-143">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-143">ascending</span></span>           | <span data-ttu-id="87b7f-144">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-144">ascending</span></span>      |
| <span data-ttu-id="87b7f-145">Data rosnąca</span><span class="sxs-lookup"><span data-stu-id="87b7f-145">Date ascending</span></span>       | <span data-ttu-id="87b7f-146">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-146">ascending</span></span>           | <span data-ttu-id="87b7f-147">Najważniejsze</span><span class="sxs-lookup"><span data-stu-id="87b7f-147">descending</span></span>     |
| <span data-ttu-id="87b7f-148">Data malejąca</span><span class="sxs-lookup"><span data-stu-id="87b7f-148">Date descending</span></span>      | <span data-ttu-id="87b7f-149">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-149">ascending</span></span>           | <span data-ttu-id="87b7f-150">rosnąco</span><span class="sxs-lookup"><span data-stu-id="87b7f-150">ascending</span></span>      |

<span data-ttu-id="87b7f-151">Metoda używa LINQ to Entities, aby określić kolumnę, według której ma zostać wykonane sortowanie.</span><span class="sxs-lookup"><span data-stu-id="87b7f-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="87b7f-152">Kod tworzy zmienną `IQueryable` przed instrukcją Switch, modyfikuje ją w instrukcji switch i wywołuje metodę `ToListAsync` po instrukcji `switch`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="87b7f-153">Podczas tworzenia i modyfikowania zmiennych `IQueryable` żadne zapytanie nie jest wysyłane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="87b7f-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="87b7f-154">Zapytanie nie jest wykonywane do momentu przekonwertowania obiektu `IQueryable` do kolekcji przez wywołanie metody, takiej jak `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="87b7f-155">W związku z tym ten kod skutkuje pojedynczym zapytaniem, które nie jest wykonywane do momentu wykonania instrukcji `return View`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="87b7f-156">Ten kod może uzyskać pełne informacje z dużą liczbą kolumn.</span><span class="sxs-lookup"><span data-stu-id="87b7f-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="87b7f-157">W [ostatnim samouczku w tej serii](advanced.md#dynamic-linq) pokazano, jak napisać kod, który umożliwia przekazanie nazwy kolumny `OrderBy` w zmiennej ciągu.</span><span class="sxs-lookup"><span data-stu-id="87b7f-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="87b7f-158">Dodawanie hiperłączy nagłówka kolumny do widoku indeksu ucznia</span><span class="sxs-lookup"><span data-stu-id="87b7f-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="87b7f-159">Zastąp kod w *widokach/uczniów/index. cshtml*, używając poniższego kodu do dodawania hiperłączy nagłówka kolumny.</span><span class="sxs-lookup"><span data-stu-id="87b7f-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="87b7f-160">Zmienione wiersze są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="87b7f-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="87b7f-161">Ten kod używa informacji w `ViewData` właściwości do konfigurowania hiperłączy z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="87b7f-162">Uruchom aplikację, wybierz kartę **studenci** , a następnie kliknij nagłówek kolumny **nazwisko** i **Data rejestracji** , aby sprawdzić, czy sortowanie działa.</span><span class="sxs-lookup"><span data-stu-id="87b7f-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Strona indeksu uczniów w kolejności nazw](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="87b7f-164">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="87b7f-164">Add a Search box</span></span>

<span data-ttu-id="87b7f-165">Aby dodać filtrowanie do strony indeksu uczniów, należy dodać pole tekstowe i przycisk Prześlij do widoku i wprowadzić odpowiednie zmiany w metodzie `Index`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="87b7f-166">Pole tekstowe umożliwia wprowadzenie ciągu do wyszukania w polach Imię i nazwisko.</span><span class="sxs-lookup"><span data-stu-id="87b7f-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="87b7f-167">Dodawanie funkcji filtrowania do metody index</span><span class="sxs-lookup"><span data-stu-id="87b7f-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="87b7f-168">W *StudentsController.cs*Zastąp metodę `Index` następującym kodem (zmiany są wyróżnione).</span><span class="sxs-lookup"><span data-stu-id="87b7f-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="87b7f-169">Dodano parametr `searchString` do metody `Index`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="87b7f-170">Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostanie dodane do widoku indeksu.</span><span class="sxs-lookup"><span data-stu-id="87b7f-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="87b7f-171">Dodano również do instrukcji LINQ klauzula WHERE, która wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="87b7f-172">Instrukcja, która dodaje klauzulę WHERE, jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="87b7f-173">W tym miejscu wywoływana jest metoda `Where` w obiekcie `IQueryable`, a filtr zostanie przetworzony na serwerze.</span><span class="sxs-lookup"><span data-stu-id="87b7f-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="87b7f-174">W niektórych scenariuszach może być wywoływana metoda `Where` jako metoda rozszerzająca w kolekcji w pamięci.</span><span class="sxs-lookup"><span data-stu-id="87b7f-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="87b7f-175">(Na przykład załóżmy, że zmienisz odwołanie do `_context.Students`, tak że zamiast EF `DbSet` odwołuje się do metody repozytorium, która zwraca kolekcję `IEnumerable`). Wyniki byłyby zwykle takie same, ale w niektórych przypadkach mogą być różne.</span><span class="sxs-lookup"><span data-stu-id="87b7f-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="87b7f-176">Na przykład .NET Framework implementacja metody `Contains` domyślnie wykonuje porównanie z uwzględnieniem wielkości liter, ale w SQL Server jest to określane przez ustawienie sortowania wystąpienia SQL Server.</span><span class="sxs-lookup"><span data-stu-id="87b7f-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="87b7f-177">Ustawienie domyślne nie uwzględnia wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="87b7f-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="87b7f-178">Można wywołać metodę `ToUpper`, aby test jawnie nie uwzględniał wielkości liter: *gdzie (s = > s. LastName. ToUpper (). Zawiera (Ciągwyszukiwania. ToUpper ())* .</span><span class="sxs-lookup"><span data-stu-id="87b7f-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="87b7f-179">Dzięki temu wyniki są takie same, jeśli później zmienisz kod w celu użycia repozytorium, które zwraca kolekcję `IEnumerable`, a nie obiekt `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="87b7f-180">(W przypadku wywołania metody `Contains` w kolekcji `IEnumerable` uzyskasz .NET Framework implementację. po wywołaniu dla obiektu `IQueryable` zostanie zaimplementowana implementacja dostawcy bazy danych). Istnieje jednak spadek wydajności dla tego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="87b7f-181">Kod `ToUpper` spowoduje umieszczenie funkcji w klauzuli WHERE instrukcji SELECT TSQL.</span><span class="sxs-lookup"><span data-stu-id="87b7f-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="87b7f-182">Uniemożliwi to Optymalizatorowi użycie indeksu.</span><span class="sxs-lookup"><span data-stu-id="87b7f-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="87b7f-183">Mając na względzie, że program SQL jest głównie instalowany jako bez uwzględniania wielkości liter, najlepszym rozwiązaniem jest uniknięcie `ToUpper` kodu do momentu migracji do magazynu danych z uwzględnieniem wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="87b7f-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="87b7f-184">Dodawanie pola wyszukiwania do widoku indeksu ucznia</span><span class="sxs-lookup"><span data-stu-id="87b7f-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="87b7f-185">W obszarze *widoki/uczniów/index. cshtml*Dodaj wyróżniony kod bezpośrednio przed otwierającym tagiem tabeli, aby utworzyć podpis, pole tekstowe i przycisk **wyszukiwania** .</span><span class="sxs-lookup"><span data-stu-id="87b7f-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="87b7f-186">Ten kod używa [pomocnika tagów](xref:mvc/views/tag-helpers/intro) `<form>` w celu dodania pola tekstowego wyszukiwania i przycisku.</span><span class="sxs-lookup"><span data-stu-id="87b7f-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="87b7f-187">Domyślnie pomocnik tagów `<form>` przesyła dane formularza z WPISem, co oznacza, że parametry są przesyłane w treści wiadomości HTTP, a nie w adresie URL jako ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="87b7f-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="87b7f-188">Po określeniu protokołu HTTP GET dane formularza są przesyłane w adresie URL jako ciągi zapytań, co umożliwia użytkownikom tworzenie zakładek w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="87b7f-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="87b7f-189">Wskazówki dotyczące W3C zaleca się użycie GET, gdy akcja nie spowoduje aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="87b7f-190">Uruchom aplikację, wybierz kartę **studenci** , wprowadź ciąg wyszukiwania, a następnie kliknij przycisk Wyszukaj, aby sprawdzić, czy filtrowanie działa.</span><span class="sxs-lookup"><span data-stu-id="87b7f-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Strona indeksu uczniów z filtrowaniem](sort-filter-page/_static/filtering.png)

<span data-ttu-id="87b7f-192">Zwróć uwagę, że adres URL zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="87b7f-193">Jeśli utworzysz zakładkę na tej stronie, otrzymasz przefiltrowaną listę, gdy używasz zakładki.</span><span class="sxs-lookup"><span data-stu-id="87b7f-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="87b7f-194">Dodanie `method="get"` do tagu `form` wskazuje, co spowodowało wygenerowanie ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="87b7f-195">Jeśli na tym etapie klikniesz link sortowania nagłówka kolumny, utracisz wartość filtru wprowadzoną w polu **wyszukiwania** .</span><span class="sxs-lookup"><span data-stu-id="87b7f-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="87b7f-196">Należy rozwiązać ten problem w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="87b7f-197">Dodaj stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="87b7f-197">Add paging to Students Index</span></span>

<span data-ttu-id="87b7f-198">Aby dodać stronicowanie do strony indeksu uczniów, utworzysz klasę `PaginatedList`, która używa instrukcji `Skip` i `Take` do filtrowania danych na serwerze, a nie zawsze pobiera wszystkie wiersze tabeli.</span><span class="sxs-lookup"><span data-stu-id="87b7f-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="87b7f-199">Następnie wprowadzisz dodatkowe zmiany w metodzie `Index` i dodasz przyciski stronicowania do widoku `Index`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="87b7f-200">Na poniższej ilustracji przedstawiono przyciski stronicowania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-200">The following illustration shows the paging buttons.</span></span>

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="87b7f-202">W folderze projektu Utwórz `PaginatedList.cs`, a następnie zastąp kod szablonu poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="87b7f-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="87b7f-203">Metoda `CreateAsync` w tym kodzie przyjmuje rozmiar strony i numer strony oraz stosuje odpowiednie instrukcje `Skip` i `Take` do `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="87b7f-204">Gdy `ToListAsync` jest wywoływana dla `IQueryable`, zwróci listę zawierającą tylko żądaną stronę.</span><span class="sxs-lookup"><span data-stu-id="87b7f-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="87b7f-205">Właściwości `HasPreviousPage` i `HasNextPage` mogą służyć do włączania lub wyłączania **poprzednich** i **następnych** przycisków stronicowania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="87b7f-206">Metoda `CreateAsync` jest używana zamiast konstruktora, aby utworzyć obiekt `PaginatedList<T>`, ponieważ konstruktory nie mogą uruchamiać kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="87b7f-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="87b7f-207">Dodaj stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="87b7f-207">Add paging to Index method</span></span>

<span data-ttu-id="87b7f-208">W *StudentsController.cs*Zastąp metodę `Index` poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="87b7f-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="87b7f-209">Ten kod dodaje parametr numeru strony, bieżący parametr kolejności sortowania i bieżący parametr filtru do sygnatury metody.</span><span class="sxs-lookup"><span data-stu-id="87b7f-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="87b7f-210">Gdy strona jest wyświetlana po raz pierwszy lub jeśli użytkownik nie kliknął linku stronicowania ani sortowania, wszystkie parametry będą mieć wartość null.</span><span class="sxs-lookup"><span data-stu-id="87b7f-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="87b7f-211">Jeśli zostanie kliknięty link stronicowania, zmienna strony będzie zawierać numer strony do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="87b7f-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="87b7f-212">Element `ViewData` o nazwie CurrentSort zapewnia widok z bieżącą kolejnością sortowania, ponieważ musi on być uwzględniony w łączach stronicowania, aby zachować porządek sortowania w tym samym czasie podczas stronicowania.</span><span class="sxs-lookup"><span data-stu-id="87b7f-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="87b7f-213">Element `ViewData` o nazwie CurrentFilter zapewnia widok z bieżącym ciągiem filtru.</span><span class="sxs-lookup"><span data-stu-id="87b7f-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="87b7f-214">Ta wartość musi być uwzględniona w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania, i musi zostać przywrócone do pola tekstowego, gdy strona jest ponownie wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="87b7f-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="87b7f-215">Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, należy zresetować stronę do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych.</span><span class="sxs-lookup"><span data-stu-id="87b7f-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="87b7f-216">Ciąg wyszukiwania jest zmieniany, gdy wartość zostanie wprowadzona w polu tekstowym, a przycisk Prześlij zostanie naciśnięty.</span><span class="sxs-lookup"><span data-stu-id="87b7f-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="87b7f-217">W takim przypadku parametr `searchString` nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="87b7f-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="87b7f-218">Na końcu metody `Index` Metoda `PaginatedList.CreateAsync` konwertuje zapytanie ucznia na jedną stronę uczniów w typie kolekcji, który obsługuje stronicowanie.</span><span class="sxs-lookup"><span data-stu-id="87b7f-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="87b7f-219">Ta pojedyncza strona studentów jest następnie przenoszona do widoku.</span><span class="sxs-lookup"><span data-stu-id="87b7f-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="87b7f-220">Metoda `PaginatedList.CreateAsync` przyjmuje numer strony.</span><span class="sxs-lookup"><span data-stu-id="87b7f-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="87b7f-221">Dwa znaki zapytania reprezentują operator łączenia wartości null.</span><span class="sxs-lookup"><span data-stu-id="87b7f-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="87b7f-222">Operator łączenia wartości null definiuje wartość domyślną dla typu dopuszczającego wartość null; wyrażenie `(pageNumber ?? 1)` oznacza zwrócenie wartości `pageNumber`, jeśli ma wartość, lub zwraca wartość 1, jeśli `pageNumber` jest równa null.</span><span class="sxs-lookup"><span data-stu-id="87b7f-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="87b7f-223">Dodaj linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="87b7f-223">Add paging links</span></span>

<span data-ttu-id="87b7f-224">W obszarze *widoki/studenci/index. cshtml*Zastąp istniejący kod następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="87b7f-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="87b7f-225">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="87b7f-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="87b7f-226">Instrukcja `@model` w górnej części strony określa, że widok pobiera teraz obiekt `PaginatedList<T>` zamiast obiektu `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="87b7f-227">Linki nagłówka kolumny używają ciągu zapytania, aby przekazać bieżący ciąg wyszukiwania do kontrolera, tak aby użytkownik mógł sortować wyniki filtrów:</span><span class="sxs-lookup"><span data-stu-id="87b7f-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="87b7f-228">Przyciski stronicowania są wyświetlane przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="87b7f-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="87b7f-229">Uruchom aplikację i przejdź do strony uczniów.</span><span class="sxs-lookup"><span data-stu-id="87b7f-229">Run the app and go to the Students page.</span></span>

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="87b7f-231">Kliknij linki stronicowania w różnych kolejności sortowania, aby upewnić się, że stronicowanie działa.</span><span class="sxs-lookup"><span data-stu-id="87b7f-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="87b7f-232">Następnie wprowadź ciąg wyszukiwania i spróbuj ponownie wykonać stronicowanie, aby sprawdzić, czy stronicowanie działa prawidłowo z sortowaniem i filtrowaniem.</span><span class="sxs-lookup"><span data-stu-id="87b7f-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="87b7f-233">Tworzenie strony informacje</span><span class="sxs-lookup"><span data-stu-id="87b7f-233">Create an About page</span></span>

<span data-ttu-id="87b7f-234">Na stronie **Informacje o** witrynie sieci Web dla uniwersytetów firmy Contoso można wyświetlić liczbę studentów zarejestrowanych dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="87b7f-235">Wymaga to grupowania i prostych obliczeń dla grup.</span><span class="sxs-lookup"><span data-stu-id="87b7f-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="87b7f-236">Aby to osiągnąć, należy wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="87b7f-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="87b7f-237">Utwórz klasę modelu widoku dla danych, które należy przekazać do widoku.</span><span class="sxs-lookup"><span data-stu-id="87b7f-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="87b7f-238">Utwórz metodę about na kontrolerze głównym.</span><span class="sxs-lookup"><span data-stu-id="87b7f-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="87b7f-239">Utwórz widok informacje.</span><span class="sxs-lookup"><span data-stu-id="87b7f-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="87b7f-240">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="87b7f-240">Create the view model</span></span>

<span data-ttu-id="87b7f-241">Utwórz folder *SchoolViewModels* w folderze *models* .</span><span class="sxs-lookup"><span data-stu-id="87b7f-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="87b7f-242">W nowym folderze Dodaj plik klasy *EnrollmentDateGroup.cs* i Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="87b7f-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="87b7f-243">Modyfikowanie kontrolera macierzystego</span><span class="sxs-lookup"><span data-stu-id="87b7f-243">Modify the Home Controller</span></span>

<span data-ttu-id="87b7f-244">W *HomeController.cs*Dodaj następujące instrukcje using na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="87b7f-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="87b7f-245">Dodaj zmienną klasy dla kontekstu bazy danych bezpośrednio po otwierającym nawiasie klamrowym dla klasy i Pobierz wystąpienie kontekstu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="87b7f-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="87b7f-246">Dodaj metodę `About` z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="87b7f-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="87b7f-247">Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w każdej grupie i zapisuje wyniki w kolekcji obiektów modelu widoku `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="87b7f-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="87b7f-248">Tworzenie widoku informacje</span><span class="sxs-lookup"><span data-stu-id="87b7f-248">Create the About View</span></span>

<span data-ttu-id="87b7f-249">Dodaj plik *views/Home/about. cshtml* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="87b7f-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="87b7f-250">Uruchom aplikację i przejdź do strony informacje.</span><span class="sxs-lookup"><span data-stu-id="87b7f-250">Run the app and go to the About page.</span></span> <span data-ttu-id="87b7f-251">W tabeli zostanie wyświetlona liczba uczniów dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="87b7f-252">Uzyskaj kod</span><span class="sxs-lookup"><span data-stu-id="87b7f-252">Get the code</span></span>

[<span data-ttu-id="87b7f-253">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="87b7f-253">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="87b7f-254">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="87b7f-254">Next steps</span></span>

<span data-ttu-id="87b7f-255">W tym samouczku zostaną wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="87b7f-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="87b7f-256">Dodano linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="87b7f-256">Added column sort links</span></span>
> * <span data-ttu-id="87b7f-257">Dodano pole wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="87b7f-257">Added a Search box</span></span>
> * <span data-ttu-id="87b7f-258">Dodano stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="87b7f-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="87b7f-259">Dodano stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="87b7f-259">Added paging to Index method</span></span>
> * <span data-ttu-id="87b7f-260">Dodano linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="87b7f-260">Added paging links</span></span>
> * <span data-ttu-id="87b7f-261">Utworzono stronę o</span><span class="sxs-lookup"><span data-stu-id="87b7f-261">Created an About page</span></span>

<span data-ttu-id="87b7f-262">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać zmiany modelu danych przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="87b7f-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="87b7f-263">Dalej: Obsługuj zmiany modelu danych</span><span class="sxs-lookup"><span data-stu-id="87b7f-263">Next: Handle data model changes</span></span>](migrations.md)
