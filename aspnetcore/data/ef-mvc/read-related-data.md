---
title: 'Samouczek: Odczytaj powiązane dane — ASP.NET MVC z EF Core'
description: W tym samouczku zostaną odczytane i wyświetlone powiązane dane, czyli dane, które Entity Framework ładowane do właściwości nawigacji.
author: tdykstra
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 43d05d0a5f938e60cdb54a0025d8bc277059c2e3
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583429"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Samouczek: Odczytaj powiązane dane — ASP.NET MVC z EF Core

W poprzednim samouczku został ukończony model danych szkoły. W tym samouczku zostaną odczytane i wyświetlone powiązane dane, czyli dane, które Entity Framework ładowane do właściwości nawigacji.

Na poniższych ilustracjach przedstawiono strony, z którymi będziesz korzystać.

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Dowiedz się, jak ładować powiązane dane
> * Utwórz stronę kursów
> * Tworzenie strony instruktorów
> * Informacje o jawnym ładowaniu

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie złożonego modelu danych](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Dowiedz się, jak ładować powiązane dane

Istnieje kilka sposobów, które oprogramowanie mapowanie relacyjne (ORM), takie jak Entity Framework, może ładować powiązane dane do właściwości nawigacji jednostki:

* Ładowanie eager. Po odczytaniu jednostki są pobierane powiązane dane. Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne. Należy określić eager ładowania w Entity Framework Core przy użyciu `Include` metod i. `ThenInclude`

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)

  Niektóre dane można pobrać w oddzielnych zapytaniach, a EF "naprawia" właściwości nawigacji.  Oznacza to, że EF automatycznie dodaje oddzielnie pobrane jednostki, w których należą do właściwości nawigacji wcześniej pobranych jednostek. W przypadku zapytania pobierającego powiązane dane można użyć `Load` metody zamiast metody, która zwraca listę lub obiekt, `ToList` na przykład lub `Single`.

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

* Jawne ładowanie. Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Napiszesz kod, który pobiera powiązane dane, jeśli jest to potrzebne. Tak jak w przypadku eager ładowania z oddzielnymi zapytaniami, jawne ładowanie powoduje wysłanie wielu zapytań do bazy danych. Różnica polega na tym, że z jawnym ładowaniem kod określa właściwości nawigacji do załadowania. W Entity Framework Core 1,1 można użyć `Load` metody do wykonania jawnego ładowania. Na przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* Ładowanie z opóźnieniem. Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Jednak przy pierwszej próbie uzyskania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Zapytanie jest wysyłane do bazy danych przy każdej próbie pobrania danych z właściwości nawigacji po raz pierwszy. Entity Framework Core 1,0 nie obsługuje ładowania z opóźnieniem.

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Jeśli wiesz, że potrzebujesz pokrewnych danych dla każdej pobranej jednostki, ładowanie eager często oferuje najlepszą wydajność, ponieważ pojedyncze zapytanie wysyłane do bazy danych jest zwykle bardziej wydajne niż osobne zapytania dla każdej pobranej jednostki. Załóżmy na przykład, że każdy dział ma dziesięć powiązanych kursów. Eager ładowanie wszystkich powiązanych danych spowoduje tylko pojedyncze zapytanie (join) i pojedynczą rundę do bazy danych. Oddzielne zapytanie dotyczące kursów dla każdego działu spowoduje jedenaście rund do bazy danych. Dodatkowe podróże do bazy danych są szczególnie niekorzystne w przypadku opóźnień.

Z drugiej strony w niektórych scenariuszach oddzielne zapytania są bardziej wydajne. Eager ładowanie wszystkich powiązanych danych w jednym zapytaniu może spowodować wygenerowanie bardzo złożonej sprzężenia, co SQL Server nie może przetworzyć efektywnie. Lub jeśli chcesz uzyskać dostęp do właściwości nawigacji jednostki tylko dla podzbioru zestawu obiektów, które są przetwarzane, osobne zapytania mogą działać lepiej, ponieważ eager ładowanie wszystkiego z góry spowodowałoby pobranie większej ilości danych niż jest to potrzebne. Jeśli wydajność ma kluczowe znaczenie, najlepszym rozwiązaniem jest przetestowanie wydajności obu metod w celu zapewnienia najlepszego wyboru.

## <a name="create-a-courses-page"></a>Utwórz stronę kursów

Jednostka kursu zawiera właściwość nawigacji, która zawiera jednostkę działu działu, do której jest przypisany kurs. Aby wyświetlić nazwę przypisanego działu na liście kursów, należy uzyskać Właściwość Name z jednostki działu, która znajduje się we `Course.Department` właściwości nawigacji.

Utwórz kontroler o nazwie CoursesController dla typu jednostki kursu, używając tych samych opcji dla **kontrolera MVC z widokami, używając Entity Framework** szkieletu, który był wcześniej przeznaczony dla kontrolera uczniów, jak pokazano na poniższej ilustracji:

![Dodaj kontroler kursów](read-related-data/_static/add-courses-controller.png)

Otwórz *CoursesController.cs* i Przeanalizuj `Index` metodę. Funkcja automatycznego tworzenia szkieletów określiła eager ładowania dla `Department` właściwości nawigacji przy `Include` użyciu metody.

Zastąp `IQueryable` `courses` `schoolContext`metodę poniższym kodem, który używa bardziej odpowiedniej nazwy dla zwracanych jednostek kursu (zamiast): `Index`

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Otwórz *Widok widoki/kursy/index. cshtml* i Zastąp kod szablonu poniższym kodem. Zmiany są wyróżnione:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Do kodu szkieletowego wprowadzono następujące zmiany:

* Zmieniono nagłówek z indeksu na kursy.

* Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie i chcesz go wyświetlić.

* Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu. Kod wyświetla `Name` Właściwość jednostki działu, która jest ładowana `Department` do właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Tworzenie strony instruktorów

W tej sekcji utworzysz kontroler i widok dla jednostki instruktora, aby wyświetlić stronę instruktorów:

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane z jednostki OfficeAssignment. Jednostki instruktora i OfficeAssignment są w relacji jeden-do-zero-lub-jednego. Będziesz używać eager ładowania dla jednostek OfficeAssignment. Jak wyjaśniono wcześniej, ładowanie eager jest zwykle bardziej wydajne, gdy potrzebne są powiązane dane dla wszystkich pobranych wierszy tabeli podstawowej. W takim przypadku chcesz wyświetlić przypisania pakietu Office dla wszystkich wyświetlanych instruktorów.

* Gdy użytkownik wybierze instruktora, wyświetlane są powiązane jednostki kursu. Jednostki instruktora i kursy znajdują się w relacji wiele-do-wielu. Będziesz używać eager ładowania dla jednostek kursu i powiązanych z nimi jednostek działu. W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są kursy tylko dla wybranego instruktora. Jednak w tym przykładzie pokazano, jak używać eager ładowania dla właściwości nawigacji w obrębie jednostek, które są same we właściwościach nawigacji.

* Gdy użytkownik wybierze kurs, zostanie wyświetlona wartość powiązane dane z zestawu jednostek rejestracji. Jednostki kursu i rejestracji znajdują się w relacji jeden do wielu. W przypadku jednostek rejestracji i powiązanych z nimi jednostek uczniów będziesz używać oddzielnych zapytań.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Utwórz model widoku dla widoku indeksu instruktora

Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel. W związku z tym utworzysz model widoku zawierający trzy właściwości, z których każda będzie zawierać dane dla jednej z tabel.

W folderze *SchoolViewModels* Utwórz *InstructorIndexData.cs* i Zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Tworzenie kontrolera i widoków instruktora

Utwórz kontroler instruktorów z akcjami odczyt/zapis EF, jak pokazano na poniższej ilustracji:

![Dodaj kontroler instruktorów](read-related-data/_static/add-instructors-controller.png)

Otwórz *InstructorsController.cs* i Dodaj instrukcję using dla przestrzeni nazw modele widoków:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Zamień metodę index na następujący kod, aby wykonać eager ładowanie powiązanych danych i umieścić je w modelu widoku.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Metoda akceptuje opcjonalne dane trasy (`id`) i parametr ciągu zapytania (`courseID`), który udostępnia wartości identyfikatora wybranego instruktora i wybranego kursu. Parametry są udostępniane przez **Wybieranie** hiperlinków na stronie.

Kod rozpoczyna się od utworzenia wystąpienia modelu widoku i umieszczenie go w liście instruktorów. Kod określa eager ładowania dla `Instructor.OfficeAssignment` `Instructor.CourseAssignments` i właściwości nawigacji. `Enrollments` `Department` `Enrollment` `Student` We właściwości `CourseAssignments` `Course` właściwość jest ładowana, a w tym, właściwości i są załadowane, a w każdej jednostce jest załadowana właściwość.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Ponieważ widok zawsze wymaga jednostki OfficeAssignment, to bardziej wydajne, aby pobrać te elementy w tym samym zapytaniu. Jednostki kursu są wymagane w przypadku wybrania na stronie sieci Web instruktora, dlatego pojedyncze zapytanie jest lepiej niż wiele zapytań tylko wtedy, gdy strona jest wyświetlana częściej jako kurs wybrany niż bez.

Kod powtarza `CourseAssignments` się i `Course` ponieważ potrzebne są dwie właściwości z `Course`. Pierwszy ciąg `ThenInclude` wywołań pobiera `CourseAssignment.Course`, `Course.Enrollments`, i `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

W tym momencie w kodzie inne `ThenInclude` byłyby dla `Student`właściwości nawigacji, które nie są potrzebne. Ale wywoływanie `Include` zaczyna się `Instructor` od właściwości, więc należy ponownie przejść przez łańcuch, `Course.Enrollments`tym razem określając `Course.Department` zamiast.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Poniższy kod jest wykonywany po wybraniu instruktora. Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. `Courses` Właściwość widok modelu jest następnie ładowana z jednostkami kursu z tej właściwości `CourseAssignments` nawigacji tego instruktora.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where` Metoda zwraca kolekcję, ale w tym przypadku kryteria przekazane do tej metody powodują zwrócenie tylko pojedynczej jednostki instruktora. Metoda konwertuje kolekcję na pojedynczą jednostkę instruktora, która zapewnia dostęp do `CourseAssignments` właściwości tej jednostki. `Single` Właściwość zawiera `CourseAssignment` jednostki, z których mają być tylko powiązane `Course` jednostki. `CourseAssignments`

Możesz użyć `Single` metody w kolekcji, gdy wiesz, że kolekcja będzie zawierać tylko jeden element. Pojedyncza Metoda zgłasza wyjątek, jeśli kolekcja została przeniesiona do niej pusta lub jeśli istnieje więcej niż jeden element. Alternatywą jest `SingleOrDefault`, która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta. Jednak w takim przypadku, w wyniku wyjątku (od próby znalezienia `Courses` właściwości w odwołaniu o wartości null), komunikat o wyjątku będzie mniej jasno wskazywać przyczynę problemu. Po wywołaniu `Single` metody można również przekazać warunek WHERE zamiast `Where` wywołania metody osobno:

```csharp
.Single(i => i.ID == id.Value)
```

Zamiast:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Następnie, jeśli wybrano kurs, wybrany kurs zostanie pobrany z listy kursów w modelu widoku. Następnie `Enrollments` właściwość modelu widoku jest ładowana z jednostkami rejestracji z właściwości `Enrollments` nawigacji tego kursu.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modyfikowanie widoku indeksu instruktora

W obszarze *widoki/instruktorzy/index. cshtml*Zastąp kod szablonu poniższym kodem. Zmiany są wyróżnione.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Wprowadzono następujące zmiany w istniejącym kodzie:

* Zmieniono klasę modelu na `InstructorIndexData`.

* Zmieniono tytuł strony z **indeksu** na **Instruktorzy**.

* Dodano kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana `item.OfficeAssignment` tylko wtedy, gdy nie ma wartości null. (Ponieważ jest to relacja "jeden do zera" lub jeden-do-jednego, nie może być powiązana jednostka OfficeAssignment).

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodano kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora. Aby uzyskać więcej informacji, zobacz [jawne przejście liniowe w sekcji @:](xref:mvc/views/razor#explicit-line-transition-with-) w artykule Składnia Razor.

* Dodano kod, który dynamicznie `class="success"` dodaje `tr` do elementu wybranego instruktora. Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodano nowe hiperłącze z etykietą **SELECT** zaraz przed innymi łączami w każdym wierszu, co powoduje wysłanie `Index` wybranego identyfikatora instruktora do metody.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie jest wyświetlana Właściwość Location powiązanych jednostek OfficeAssignment i pustej komórki tabeli, gdy nie istnieje powiązana jednostka OfficeAssignment.

![Nie wybrano niczego ze strony indeksu instruktorów](read-related-data/_static/instructors-index-no-selection.png)

W pliku *viewss/instruktors/index. cshtml* po elemencie zamykającej tabeli (na końcu pliku) Dodaj następujący kod. Ten kod wyświetla listę kursów związanych z instruktorem w przypadku wybrania instruktora.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Ten kod odczytuje `Courses` właściwość modelu widoku w celu wyświetlenia listy kursów. Zawiera również hiperłącze **SELECT** , które wysyła identyfikator wybranego kursu do `Index` metody akcji.

Odśwież stronę i wybierz instruktora. Teraz zobaczysz siatkę wyświetlającą kursy przypisane do wybranego instruktora, a dla każdego kursu zobaczysz nazwę przypisanego działu.

![Wybrany instruktor strony indeksu instruktora](read-related-data/_static/instructors-index-instructor-selected.png)

Po dodaniu bloku kodu Dodaj następujący kod. Spowoduje to wyświetlenie listy studentów, którzy są rejestrowani w kursie po wybraniu tego kursu.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Ten kod odczytuje Właściwość Enrollments modelu widoku w celu wyświetlenia listy uczniów zarejestrowanych w kursie.

Odśwież stronę ponownie i wybierz instruktora. Następnie wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Informacje o jawnym załadowaniu

Po pobraniu listy instruktorów w *InstructorsController.cs*określono eager ładowania dla `CourseAssignments` właściwości nawigacji.

Załóżmy, że oczekujesz, że użytkownicy rzadko chcą widzieć rejestracje w wybranym instruktorze i kursie. W takim przypadku możesz chcieć załadować dane rejestracji tylko wtedy, gdy jest to wymagane. Aby zapoznać się z przykładem, jak przeprowadzić jawne ładowanie, Zastąp `Index` metodę następującym kodem, który usuwa eager ładowania na potrzeby rejestracji i ładuje tę właściwość jawnie. Zmiany kodu są wyróżnione.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Nowy kod odrzuca metodę *ThenInclude* wywołań danych rejestracji z kodu, który pobiera jednostki instruktora. Jeśli wybrano instruktora i kurs, wyróżniony kod pobiera jednostki rejestracji dla wybranego kursu i jednostek uczniów dla każdej rejestracji.

Uruchom aplikację, przejdź do strony indeks instruktorów, aby zobaczyć, co jest wyświetlane na stronie, chociaż zmieniono sposób pobierania danych.

## <a name="get-the-code"></a>Pobierz kod

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Dowiesz się, jak ładować powiązane dane
> * Utworzono stronę kursów
> * Utworzono stronę instruktorów
> * Informacje o jawnym ładowaniu

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować powiązane dane.

> [!div class="nextstepaction"]
> [Aktualizowanie powiązanych danych](update-related-data.md)
