Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Klasa `Movie` zawiera:

* Pole `Id` wymagane przez bazę danych dla klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (`Date`). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.
  * Wyświetlana jest tylko data, a nie informacje o czasie.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.