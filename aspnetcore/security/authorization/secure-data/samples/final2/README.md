# <a name="how-to-buildrun-secure-user-data-sample"></a>Jak kompilacji/uruchomić próbki danych bezpiecznego użytkownika

* Ustaw hasło, za pomocą narzędzia menedżera klucz tajny:

  `dotnet user-secrets set SeedUserPW <pw>`

* Aktualizacja bazy danych:

    `dotnet ef database update`

* Włączanie obsługi protokołu HTTPS w projekcie
