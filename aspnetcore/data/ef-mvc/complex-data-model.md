---
title: 'Samouczek: Tworzenie złożonego modelu danych — ASP.NET MVC z EF Core'
description: W tym samouczku Dodaj więcej jednostek i relacje i Dostosuj model danych, określając formatowanie, walidację i reguły mapowania.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 85a11ba082fc8f6b364019f6cefcd5b1fe5a9215
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080474"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Samouczek: Tworzenie złożonego modelu danych — ASP.NET MVC z EF Core

W poprzednich samouczkach pracujesz z prostym modelem danych, który składa się z trzech jednostek. W tym samouczku dodasz więcej jednostek i relacji, a następnie utworzysz model danych, określając formatowanie, walidację i reguły mapowania bazy danych.

Po zakończeniu klasy jednostek składają się na ukończony model danych przedstawiony na poniższej ilustracji:

![Diagram jednostek](complex-data-model/_static/diagram.png)

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Dostosowywanie modelu danych
> * Wprowadzanie zmian w jednostce ucznia
> * Utwórz jednostkę instruktora
> * Tworzenie jednostki OfficeAssignment
> * Modyfikuj jednostkę kursu
> * Utwórz jednostkę działu
> * Modyfikuj jednostkę rejestracji
> * Aktualizowanie kontekstu bazy danych
> * Wypełnianie bazy danych z danymi testowymi
> * Dodawanie migracji
> * Zmień parametry połączenia
> * Aktualizowanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

* [Korzystanie z EF Core migracji](migrations.md)

## <a name="customize-the-data-model"></a>Dostosowywanie modelu danych

W tej sekcji dowiesz się, jak dostosować model danych przy użyciu atrybutów, które określają formatowanie, walidację i reguły mapowania bazy danych. Następnie w kilku poniższych sekcjach utworzysz kompletny model danych szkolnych przez dodanie atrybutów do klas, które zostały już utworzone, i tworzenie nowych klas dla pozostałych typów jednostek w modelu.

### <a name="the-datatype-attribute"></a>Atrybut DataType

W przypadku dat rejestracji uczniów na wszystkich stronach sieci Web jest obecnie wyświetlany czas wraz z datą, chociaż wszystko, co jest ważne dla tego pola, to Data. Używając atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, która naprawi format wyświetlania w każdym widoku, który wyświetla dane. Aby zapoznać się z przykładem, jak to zrobić, Dodaj atrybut do `EnrollmentDate` właściwości `Student` w klasie.

W *modelach/student. cs*Dodaj `using` instrukcję do `DisplayFormat` `System.ComponentModel.DataAnnotations` `DataType` przestrzeni nazw i Dodaj atrybuty i do właściwości,jakpokazanownastępującymprzykładzie:`EnrollmentDate`

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Ten `DataType` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych. W tym przypadku chcemy tylko śledzić datę, a nie datę i godzinę. `DataType` Wyliczenie zawiera wiele typów danych, takich jak data, godzina, numer telefonu, waluta, EmailAddress i inne. Ten `DataType` atrybut może również umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu. Na przykład `mailto:` można utworzyć łącze dla `DataType.EmailAddress`i dla programu `DataType.Date` w przeglądarkach, które obsługują HTML5, można podać selektor daty. Ten `DataType` atrybut emituje kod HTML `data-` 5 (wymawiane kreski danych), które przeglądarki HTML 5 mogą zrozumieć. `DataType` Atrybuty nie zapewniają żadnych weryfikacji.

`DataType.Date`nie określa formatu wyświetlanej daty. Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na CultureInfo serwera.

Ten `DisplayFormat` atrybut służy do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` Ustawienie określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji. (Możesz nie chcieć, aby dla niektórych pól — na przykład w przypadku wartości walutowych, można nie chcieć, aby symbol waluty w polu tekstowym do edycji.)

Możesz użyć `DisplayFormat` atrybutu przez siebie, ale zazwyczaj dobrym pomysłem jest `DataType` użycie atrybutu. Ten `DataType` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, których nie można `DisplayFormat`uzyskać:

* Przeglądarka może włączać funkcje HTML5 (na przykład w celu wyświetlenia kontrolki Calendar, symbolu waluty właściwej dla ustawień regionalnych, linków poczty e-mail, niektórych weryfikacji danych wejściowych po stronie klienta itp.).

* Domyślnie przeglądarka będzie renderować dane przy użyciu poprawnego formatu na podstawie ustawień regionalnych.

Aby uzyskać więcej informacji, zobacz [ \<dokumentację pomocnika tagów > danych wejściowych](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Uruchom aplikację, przejdź do strony indeks uczniów i zwróć uwagę na to, że czasy nie są już wyświetlane dla dat rejestracji. Ta sama wartość będzie dotyczyć dowolnego widoku, który korzysta z modelu ucznia.

![Strona indeksu uczniów pokazująca daty bez czasu](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atrybut StringLength

Można również określić reguły walidacji danych i komunikaty o błędach walidacji przy użyciu atrybutów. `StringLength` Atrybut ustawia maksymalną długość w bazie danych i zapewnia walidację po stronie klienta i serwera dla ASP.NET Core MVC. Możesz również określić minimalną długość ciągu w tym atrybucie, ale wartość minimalna nie ma wpływu na schemat bazy danych.

Załóżmy, że chcesz upewnić się, że użytkownicy nie wprowadzają więcej niż 50 znaków. Aby dodać to ograniczenie, należy `StringLength` dodać atrybuty `LastName` do właściwości `FirstMidName` i, jak pokazano w następującym przykładzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Ten `StringLength` atrybut nie uniemożliwi użytkownikowi wprowadzania białych znaków w nazwie. Możesz użyć atrybutu, `RegularExpression` aby zastosować ograniczenia do danych wejściowych. Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki są alfabetyczne:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Ten `MaxLength` atrybut zawiera funkcje podobne do atrybutu `StringLength` , ale nie zapewnia weryfikacji po stronie klienta.

Model bazy danych został zmieniony w sposób, który wymaga zmiany w schemacie bazy danych. Migracje zostaną użyte do zaktualizowania schematu bez utraty danych, które mogły zostać dodane do bazy danych za pomocą interfejsu użytkownika aplikacji.

Zapisz zmiany i skompiluj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

`migrations add` Polecenie ostrzega o utracie danych, ponieważ zmiana powoduje krótszą długość dla dwóch kolumn.  Migracja tworzy plik o nazwie  *\<timestamp > _MaxLengthOnNames. cs*. Ten plik zawiera kod w `Up` metodzie, która zaktualizuje bazę danych w taki sposób, aby była zgodna z bieżącym modelem danych. `database update` Polecenie uruchomiło ten kod.

Sygnatura czasowa poprzedzona nazwą pliku migracji jest używana przez Entity Framework do porządkowania migracji. Można utworzyć wiele migracji przed uruchomieniem polecenia Update-Database, a następnie wszystkie migracje zostaną zastosowane w kolejności, w której zostały utworzone.

Uruchom aplikację, wybierz kartę **uczniowie** , kliknij pozycję **Utwórz nową**, a następnie spróbuj wprowadzić nazwy dłuższe niż 50 znaków. Aplikacja powinna uniemożliwiać wykonanie tej czynności. 

### <a name="the-column-attribute"></a>Atrybut Column

Można również użyć atrybutów do kontrolowania sposobu, w jaki klasy i właściwości są mapowane do bazy danych. Załóżmy, że użyto nazwy `FirstMidName` dla pola pierwszej nazwy, ponieważ pole może zawierać również nazwę środkową. Ale chcesz, aby kolumna bazy danych była nazywana `FirstName`, ponieważ użytkownicy, którzy będą zapisywać zapytania ad hoc względem bazy danych, są przyzwyczajoni do tej nazwy. Aby to mapowanie było możliwe, można użyć `Column` atrybutu.

Ten `Column` atrybut określa, że podczas tworzenia bazy danych kolumna `Student` `FirstMidName` tabeli, która jest mapowana na właściwość, będzie nosiła nazwę `FirstName`. Innymi słowy, gdy Twój kod odwołuje się `Student.FirstMidName`do, dane będą pochodzić z lub zostaną zaktualizowane `FirstName` w kolumnie `Student` tabeli. Jeśli nie określisz nazw kolumn, otrzymają one taką samą nazwę jak nazwa właściwości.

W pliku *student.cs* Dodaj `using` instrukcję do `System.ComponentModel.DataAnnotations.Schema` i `FirstMidName` Dodaj atrybut nazwy kolumny do właściwości, jak pokazano w następującym wyróżnionym kodzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Dodanie `Column` atrybutu zmienia model przy użyciu `SchoolContext`kopii zapasowej, dlatego nie jest on zgodny z bazą danych.

Zapisz zmiany i skompiluj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia, aby utworzyć kolejną migrację:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

W **Eksplorator obiektów SQL Server**Otwórz projektanta tabeli uczniów, klikając dwukrotnie tabelę **uczniów** .

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

Przed zastosowaniem pierwszych dwóch migracji, kolumny nazw były typu nvarchar (MAX). Są one teraz nvarchar (50), a nazwa kolumny została zmieniona z FirstMidName na FirstName.

> [!Note]
> Jeśli spróbujesz skompilować przed ukończeniem tworzenia wszystkich klas jednostek w poniższych sekcjach, mogą wystąpić błędy kompilatora.

## <a name="changes-to-student-entity"></a>Zmiany w jednostce ucznia

![Jednostka ucznia](complex-data-model/_static/student-entity.png)

W *modelach/student. cs*Zastąp kod, który został dodany wcześniej do poniższego kodu. Zmiany są wyróżnione.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Wymagany atrybut

`Required` Atrybut powoduje, że właściwości nazwy są wymagane. `Required` Atrybut nie jest wymagany dla typów niedopuszczających wartości null, takich jak typy wartości (DateTime, int, Double, float itp.). Typy, które nie mogą mieć wartości null, są automatycznie traktowane jako pola wymagane.

Można usunąć `Required` atrybut i zastąpić go parametrem minimalnej długości `StringLength` dla atrybutu:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atrybut wyświetlania

Ten `Display` atrybut określa, że podpis pól tekstowych powinien mieć wartość "First Name", "nazwisko", "Full Name" i "Data rejestracji" zamiast nazwy właściwości w każdym wystąpieniu (które nie zawiera spacji dzielących wyrazy).

### <a name="the-fullname-calculated-property"></a>Właściwość obliczeniowa FullName

`FullName`jest właściwością obliczaną, która zwraca wartość utworzoną przez połączenie dwóch innych właściwości. W związku z tym ma tylko metodę dostępu get i `FullName` żadna kolumna nie zostanie wygenerowana w bazie danych.

## <a name="create-instructor-entity"></a>Utwórz jednostkę instruktora

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

Utwórz *modele/instruktor. cs*, zastępując kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Zwróć uwagę, że kilka właściwości jest taka sama w jednostkach studenta i instruktora. W samouczku [implementującym dziedziczenie](inheritance.md) w dalszej części tej serii powrócisz ten kod, aby wyeliminować nadmiarowość.

Można umieścić wiele atrybutów w jednym wierszu, aby można było również napisać `HireDate` atrybuty w następujący sposób:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Właściwości nawigacji CourseAssignments i OfficeAssignment

Właściwości `CourseAssignments` i`OfficeAssignment` są właściwościami nawigacji.

Instruktor może nauczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Jeśli właściwość nawigacji może zawierać wiele jednostek, jej typem musi być lista, w której można dodawać, usuwać i aktualizować wpisy.  Możesz określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`. Jeśli określisz `ICollection<T>`, EF domyślnie `HashSet<T>` tworzy kolekcję.

Przyczyny tego problemu są `CourseAssignment` opisane poniżej w sekcji dotyczącej relacji wiele-do-wielu.

Firma Contoso University State rules, że instruktor może mieć tylko co najwyżej jeden urząd, więc `OfficeAssignment` Właściwość zawiera jedną jednostkę OfficeAssignment (która może mieć wartość null, jeśli nie przypisano pakietu Office).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Tworzenie jednostki OfficeAssignment

![Jednostka OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Utwórz *modele/OfficeAssignment. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atrybut klucza

Istnieje relacja jeden do zera między instruktorem a jednostkami OfficeAssignment. Przypisanie pakietu Office istnieje tylko w odniesieniu do instruktora, do którego jest przypisany, i w związku z tym jego klucz podstawowy jest również jego kluczem obcym dla jednostki instruktora. Jednak Entity Framework nie może automatycznie rozpoznać InstructorID jako klucza podstawowego tej jednostki, ponieważ jego nazwa nie jest zgodna z konwencją nazewnictwa ID lub classnameID. W związku z tym atrybutjestużywanydoidentyfikowaniagojakoklucz:`Key`

```csharp
[Key]
public int InstructorID { get; set; }
```

Można również użyć atrybutu, `Key` Jeśli jednostka ma własny klucz podstawowy, ale chcesz nazwać Właściwość inną niż classnameID lub ID.

Domyślnie EF traktuje klucz jako niegenerowaną przez nie bazę danych, ponieważ kolumna jest dla relacji identyfikującej.

### <a name="the-instructor-navigation-property"></a>Właściwość nawigacji instruktora

Jednostka instruktora ma właściwość nawigacji dopuszczającą wartość null `OfficeAssignment` (ponieważ instruktor może nie mieć przypisania pakietu Office), a jednostka OfficeAssignment ma właściwość nawigacji niedopuszczające wartości null `Instructor` (ponieważ przypisanie pakietu Office nie może istnieje bez instruktora — `InstructorID` nie dopuszcza wartości null. Gdy jednostką instruktora jest powiązana jednostka OfficeAssignment, każda jednostka będzie miała odwołanie do drugiego z nich we właściwości nawigacji.

Można umieścić `[Required]` atrybut we właściwości nawigacji instruktora, aby określić, że musi istnieć powiązany instruktor, ale nie trzeba tego robić, `InstructorID` ponieważ klucz obcy (który również jest kluczem do tej tabeli) nie dopuszcza wartości null.

## <a name="modify-course-entity"></a>Modyfikuj jednostkę kursu

![Jednostka kursu](complex-data-model/_static/course-entity.png)

W *modelach/kurs. cs*Zastąp kod, który został dodany wcześniej do poniższego kodu. Zmiany są wyróżnione.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Jednostka kursu ma właściwość `DepartmentID` klucza obcego, która wskazuje jednostkę powiązanego działu i `Department` ma właściwość nawigacji.

Entity Framework nie wymaga dodawania właściwości klucza obcego do modelu danych, jeśli istnieje właściwość nawigacji dla powiązanej jednostki.  EF automatycznie tworzy klucze obce w bazie danych wszędzie tam, gdzie są potrzebne, i tworzy dla nich [Właściwości cienia](/ef/core/modeling/shadow-properties) . Jednak klucz obcy w modelu danych może sprawiać, że aktualizacje są prostsze i bardziej wydajne. Na przykład podczas pobierania jednostki kursu do edycji, jednostka działu ma wartość null, jeśli nie zostanie załadowana, więc po zaktualizowaniu jednostki kursu należy najpierw pobrać jednostkę działu. Gdy właściwość `DepartmentID` klucza obcego jest uwzględniona w modelu danych, nie musisz pobierać jednostki działu przed aktualizacją.

### <a name="the-databasegenerated-attribute"></a>Atrybut DatabaseGenerated

Atrybut z parametrem `CourseID` właściwości określa, że wartości klucza podstawowego są dostarczane przez użytkownika, a nie wygenerowane przez bazę danych. `None` `DatabaseGenerated`

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Domyślnie, Entity Framework zakłada, że wartości klucza podstawowego są generowane przez bazę danych. To wszystko, czego potrzebujesz w większości scenariuszy. Jednak w przypadku jednostek kursu używany jest numer kursu określony przez użytkownika, taki jak seria 1000 dla jednego działu, seria 2000 dla innego działu itd.

Ten `DatabaseGenerated` atrybut może być również używany do generowania wartości domyślnych, tak jak w przypadku kolumn bazy danych używanych do rejestrowania daty utworzenia lub aktualizacji wiersza.  Aby uzyskać więcej informacji, zobacz [wygenerowane właściwości](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego i właściwości nawigacji w jednostce kurs odzwierciedlają następujące relacje:

Kurs jest przypisywany do jednego działu, dlatego istnieje `DepartmentID` klucz obcy `Department` i właściwość nawigacji dla powodów wymienionych powyżej.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurs może zawierać dowolną liczbę studentów, więc `Enrollments` właściwość nawigacji jest kolekcją:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs może być nauczany przez wiele instruktorów, więc `CourseAssignments` właściwość nawigacji jest kolekcją (typ `CourseAssignment` jest wyjaśniony [później](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Utwórz jednostkę działu

![Jednostka działu](complex-data-model/_static/department-entity.png)

Utwórz *modele/dział. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atrybut Column

Wcześniej użyto `Column` atrybutu do zmiany mapowania nazw kolumn. W kodzie podmiotu `Column` działu atrybut jest używany do zmiany mapowania typu danych SQL, tak aby kolumna została zdefiniowana przy użyciu SQL Server typu pieniędzy w bazie danych:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapowanie kolumn zazwyczaj nie jest wymagane, ponieważ Entity Framework wybiera odpowiedni SQL Server typ danych oparty na typie CLR zdefiniowanym dla właściwości. Typ CLR `decimal` jest mapowany na typ SQL Server `decimal` . Ale w tym przypadku wiesz, że kolumna będzie zawierać kwoty walutowe, a typ danych walutowych jest bardziej odpowiedni dla tego.

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego i nawigacji odzwierciedlają następujące relacje:

Dział może lub nie ma uprawnienia administratora, a administrator jest zawsze instruktorem. W związku `InstructorID` z tym właściwość jest dołączana jako klucz obcy do jednostki instruktora, a znak zapytania jest dodawany `int` po wyznaczeniu typu, aby oznaczyć właściwość jako wartość null. Właściwość nawigacji ma nazwę `Administrator` , ale zawiera jednostkę instruktora:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Dział może mieć wiele kursów, więc istnieje właściwość nawigacji kursów:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Zgodnie z Konwencją Entity Framework włącza kaskadowe usuwanie kluczy obcych niedopuszczających wartości null oraz relacji wiele-do-wielu. Może to spowodować cykliczne reguły usuwania kaskadowego, co spowoduje wyjątek podczas próby dodania migracji. Na przykład jeśli nie zdefiniowano właściwości Department. InstructorID jako nullable, EF skonfiguruje regułę usuwania kaskadowego w celu usunięcia działu po usunięciu instruktora, który nie jest tym, co się dzieje. Jeśli reguły biznesowe wymagają `InstructorID` , aby właściwość nie dopuszczać wartości null, należy użyć następującej instrukcji interfejsu API Fluent, aby wyłączyć kaskadowe usuwanie relacji:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Modyfikuj jednostkę rejestracji

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

W *modelach/rejestracji. cs*Zastąp kod, który został dodany wcześniej do poniższego kodu:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego i właściwości nawigacji odzwierciedlają następujące relacje:

Rekord rejestracji dotyczy pojedynczego kursu, dlatego istnieje `CourseID` właściwość klucza obcego `Course` i właściwość nawigacji:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Rekord rejestracji dotyczy pojedynczego ucznia, dlatego istnieje `StudentID` właściwość klucza obcego `Student` i właściwość nawigacji:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relacje wiele do wielu

Istnieje relacja wiele-do-wielu między jednostkami uczniów i kursów, a jednostka rejestracji działa jako tabela sprzężeń wiele-do-wielu *z ładunkiem* w bazie danych. "Z ładunkiem" oznacza, że tabela rejestracji zawiera dodatkowe dane, a nie klucze obce dla sprzężonych tabel (w tym przypadku klucz podstawowy i właściwość klasy).

Na poniższej ilustracji pokazano, jak wyglądają te relacje w diagramie jednostek. (Ten diagram został wygenerowany przy użyciu Entity Framework narzędzia do zarządzania prawami do programu EF 6. x; Tworzenie diagramu nie jest częścią samouczka, a właśnie jest używane w tym miejscu jako ilustracja).

![Student-kurs wiele do wielu relacji](complex-data-model/_static/student-course.png)

Każda linia relacji ma 1 na jednym końcu i gwiazdkę (*) na drugim, wskazując relację jeden do wielu.

Jeśli tabela rejestracji nie zawiera informacji o klasie, musi zawierać tylko dwa klucze obce CourseID i StudentID. W takim przypadku byłoby to tabela sprzężenia wiele-do-wielu bez ładunku (lub czystej tabeli sprzężeń) w bazie danych. Jednostki instruktora i kursy mają ten rodzaj relacji wiele-do-wielu, a następnym krokiem jest utworzenie klasy jednostki do działania jako tabela sprzężenia bez ładunku.

(Dr 6. x obsługuje niejawne tabele sprzężeń dla relacji wiele-do-wielu, ale EF Core nie. Aby uzyskać więcej informacji, zobacz [dyskusję w repozytorium EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368)).

## <a name="the-courseassignment-entity"></a>Jednostka CourseAssignment

![Jednostka CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Utwórz *modele/CourseAssignment. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Przyłączanie nazw jednostek

Tabela sprzężenia jest wymagana w bazie danych programu dla instruktora "wiele do wielu" i musi być reprezentowana przez zestaw jednostek. Nazwa jednostki `EntityName1EntityName2`sprzężenia jest wspólna w przypadku, gdy jest to możliwe `CourseInstructor`. Zalecamy jednak wybranie nazwy opisującej relację. Modele danych zaczynają się łatwo i zwiększają, dzięki czemu nie są często odbierane ładunki. Jeśli zaczynasz od nazwy obiektu opisowego, nie będzie konieczna zmiana nazwy później. Najlepiej, aby jednostka sprzężenia miała własną nazwę naturalną (prawdopodobnie jednowyrazową) w domenie biznesowej. Na przykład książki i klienci mogą być połączone poprzez klasyfikacje. Dla tej relacji `CourseAssignment` jest lepszym wyborem niż `CourseInstructor`.

### <a name="composite-key"></a>Klucz złożony

Ponieważ klucze obce nie dopuszczają wartości null i jednoznacznie identyfikują każdy wiersz tabeli, nie ma potrzeby oddzielnego klucza podstawowego. Właściwości *InstructorID* i *CourseID* powinny działać jako złożony klucz podstawowy. Jedynym sposobem identyfikacji złożonych kluczy podstawowych do EF jest użycie *interfejsu API Fluent* (nie można go wykonać przy użyciu atrybutów). W następnej sekcji zobaczysz, jak skonfigurować złożony klucz podstawowy.

Klucz złożony gwarantuje, że chociaż można mieć wiele wierszy dla jednego kursu i wiele wierszy dla jednego instruktora, nie można mieć wielu wierszy dla tego samego instruktora i kursu. Jednostka `Enrollment` Join definiuje własny klucz podstawowy, więc możliwe jest duplikowanie tego sortowania. Aby uniknąć takich duplikatów, można dodać unikatowy indeks do pól klucza obcego lub skonfigurować `Enrollment` przy użyciu podstawowego klucza złożonego podobnego do. `CourseAssignment` Aby uzyskać więcej informacji, zobacz [indeksy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizowanie kontekstu bazy danych

Dodaj następujący wyróżniony kod do pliku *Data/SchoolContext. cs* :

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Ten kod dodaje nowe jednostki i konfiguruje złożony klucz podstawowy jednostki CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>Informacje o alternatywnym interfejsie API Fluent

Kod w `OnModelCreating` metodzie `DbContext` klasy używa *interfejsu API Fluent* do konfigurowania zachowania EF. Interfejs API nosi nazwę "Fluent", ponieważ jest często używany przez ciąg szeregu wywołań metod wraz z pojedynczą instrukcją, jak w poniższym przykładzie z [dokumentacji EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

W tym samouczku korzystasz z interfejsu API Fluent tylko dla mapowania bazy danych, której nie można wykonać przy użyciu atrybutów. Można jednak również użyć interfejsu API Fluent, aby określić większość reguł formatowania, walidacji i mapowania, które można wykonać przy użyciu atrybutów. Niektórych atrybutów, takich `MinimumLength` jak nie można zastosować w interfejsie API Fluent. Jak wspomniano wcześniej `MinimumLength` , nie zmienia schematu, stosuje tylko regułę walidacji po stronie klienta i serwera.

Niektórzy deweloperzy wolą korzystać z interfejsu API Fluent wyłącznie w taki sposób, aby mogli utrzymać czyste klasy jednostek. Jeśli chcesz, możesz mieszać atrybuty i interfejs API Fluent, a istnieje kilka dostosowań, które można wykonać tylko za pomocą interfejsu API Fluent, ale ogólnie zalecane jest, aby wybrać jeden z tych dwóch podejść i użyć go spójnie tak dużo, jak to możliwe. Jeśli używasz obu tych metod, pamiętaj, że w przypadku wystąpienia konfliktu, interfejs API Fluent przesłania atrybuty.

Aby uzyskać więcej informacji na temat atrybutów a interfejs API Fluent, zobacz [metody konfiguracji](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram jednostek przedstawiający relacje

Na poniższej ilustracji przedstawiono diagram, który Entity Framework narzędzia do tworzenia dla gotowego modelu szkoły.

![Diagram jednostek](complex-data-model/_static/diagram.png)

Oprócz linii relacji jeden-do-wielu (od 1 do \*) można zobaczyć w tym miejscu linię relacji jeden-do-lub-jednego (od 1 do 0.. 1) między jednostkami instruktora i OfficeAssignment oraz linią relacji zero-lub-jeden-do-wielu (0.. 1 do *) między Instruktorzy i działy.

## <a name="seed-database-with-test-data"></a>Wypełnianie bazy danych z danymi testowymi

Zastąp kod w pliku *Data/dbinitialize. cs* następującym kodem, aby zapewnić dane dotyczące inicjatorów dla nowo utworzonych jednostek.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Jak przedstawiono w pierwszym samouczku, większość tego kodu po prostu tworzy nowe obiekty jednostki i ładuje przykładowe dane do właściwości, które są wymagane do testowania. Zauważ, że relacje wiele-do-wielu są obsługiwane: kod tworzy relacje przez tworzenie jednostek w `Enrollments` zestawach jednostek i. `CourseAssignment`

## <a name="add-a-migration"></a>Dodawanie migracji

Zapisz zmiany i skompiluj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź `migrations add` polecenie (nie wykonuj jeszcze polecenia Update-Database):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Zostanie wyświetlone ostrzeżenie o możliwej utracie danych.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Jeśli podjęto próbę uruchomienia `database update` polecenia w tym punkcie (nie rób tego jeszcze), zostanie wyświetlony następujący błąd:

> Instrukcja ALTER TABLE spowodowała konflikt z ograniczeniem klucza obcego "FK_dbo. Course_dbo. Department_DepartmentID". Konflikt wystąpił w bazie danych "ContosoUniversity", tabela "dbo. Dział ", kolumna" DepartmentID ".

Czasami w przypadku wykonywania migracji z istniejącymi danymi należy wstawić dane szczątkowe do bazy danych w celu spełnienia ograniczeń klucza obcego. Wygenerowany kod w `Up` metodzie dodaje klucz obcy DepartmentID niedopuszczający wartości null do tabeli kursów. Jeśli w tabeli kursów występuje już wiersz, `AddColumn` operacja nie powiedzie się, ponieważ SQL Server nie wie, jaka wartość należy umieścić w kolumnie, która nie może mieć wartości null. Na potrzeby tego samouczka przeprowadzisz migrację do nowej bazy danych, ale w aplikacji produkcyjnej musisz przeprowadzić migrację istniejących danych, więc w poniższych wskazówkach przedstawiono przykład sposobu, w jaki należy to zrobić.

Aby ta migracja działała z istniejącymi danymi, należy zmienić kod w celu nadania nowej kolumnie wartości domyślnej, a także utworzyć Wydział zastępczy o nazwie "Temp", który będzie pełnił rolę domyślnego działu. W związku z tym `Up` istniejące wiersze kursu będą ze sobą powiązane z działem "Temp" po uruchomieniu metody.

* Otwórz plik *{timestamp} _ComplexDataModel. cs* .

* Dodaj komentarz do wiersza kodu, który dodaje kolumnę DepartmentID do tabeli kursów.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Dodaj następujący wyróżniony kod po kodzie, który tworzy tabelę działu:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

W aplikacji produkcyjnej Napisz kod lub skrypty w celu dodania wierszy działu i powiązania wierszy kursu z nowym wierszem działu. W kolumnie kurs. DepartmentID nie będzie już potrzebny żaden dział "Temp" ani wartość domyślna.

Zapisz zmiany i skompiluj projekt.

## <a name="change-the-connection-string"></a>Zmień parametry połączenia

Teraz masz nowy kod w `DbInitializer` klasie, który dodaje dane inicjatora dla nowych jednostek do pustej bazy danych. Aby utworzyć EF Utwórz nową pustą bazę danych, należy zmienić nazwę bazy danych w parametrach pliku *appSettings. JSON* na ContosoUniversity3 lub inną nazwę, która nie została użyta na komputerze, którego używasz.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Zapisz zmiany w pliku *appSettings. JSON*.

> [!NOTE]
> Alternatywą dla zmiany nazwy bazy danych jest usunięcie bazy danych. Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` interfejsu wiersza polecenia:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualizowanie bazy danych

Po zmianie nazwy bazy danych lub usunięciu bazy danych Uruchom `database update` polecenie w oknie wiersza polecenia, aby wykonać migracje.

```dotnetcli
dotnet ef database update
```

Uruchom aplikację, aby spowodować `DbInitializer.Initialize` uruchomienie metody i wypełnianie nowej bazy danych.

Otwórz bazę danych w programie SSOX, tak jak wcześniej, i rozwiń węzeł **tabele** , aby zobaczyć, że wszystkie tabele zostały utworzone. (Jeśli nadal masz otwarty SSOX od momentu wcześniejszego, kliknij przycisk **odświeżania** ).

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

Uruchom aplikację, aby wyzwolić kod inicjatora, który odziarnauje bazę danych.

Kliknij prawym przyciskiem myszy tabelę **CourseAssignment** , a następnie wybierz pozycję **Wyświetl dane** , aby sprawdzić, czy zawiera ona dane.

![CourseAssignment dane w SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Pobierz kod

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Dostosowany model danych
> * Wprowadzono zmiany w jednostce ucznia
> * Utworzono jednostkę instruktora
> * Utworzono jednostkę OfficeAssignment
> * Zmodyfikowana jednostka kursu
> * Utworzona jednostka działu
> * Zmodyfikowano jednostkę rejestracji
> * Zaktualizowano kontekst bazy danych
> * Wypełnianie bazy danych z danymi testowymi
> * Dodano migrację
> * Zmieniono parametry połączenia
> * Zaktualizowano bazę danych

Przejdź do następnego samouczka, aby dowiedzieć się więcej o tym, jak uzyskać dostęp do powiązanych danych.

> [!div class="nextstepaction"]
> [Ponown Dostęp do powiązanych danych](read-related-data.md)
