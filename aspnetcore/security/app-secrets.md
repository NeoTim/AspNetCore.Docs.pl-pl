---
title: Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje jako wpisy tajne aplikacji podczas opracowywania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 0203a5737caf1af809b739d9e266a6971cd1523b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080718"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core. Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym. Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji. Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.

::: moniker range="<= aspnetcore-1.1"

Skonfiguruj Odczytywanie wartości zmiennych środowiskowych przez wywołanie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> `Startup` w konstruktorze:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** . Domyślne parametry połączenia z bazą danych są zawarte w pliku *appSettings. JSON* projektu z kluczem `DefaultConnection`. Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła. Podczas wdrażania `DefaultConnection` aplikacji wartość klucza może być zastąpiona wartością zmiennej środowiskowej. Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.

> [!WARNING]
> Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu. W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron. Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Menedżer wpisów tajnych

Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core. W tym kontekście dane poufne są tajne dla aplikacji. Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu. Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach. Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.

> [!WARNING]
> Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn. Jest przeznaczona tylko do celów programistycznych. Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.

## <a name="how-the-secret-manager-tool-works"></a>Jak działa narzędzie do zarządzania kluczami tajnymi

Narzędzie tajnego Menedżera wyodrębnia szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości. Możesz użyć narzędzia bez znajomości tych szczegółów implementacji. Wartości są przechowywane w pliku konfiguracji JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Ścieżka systemu plików:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Ścieżka systemu plików:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

W poprzednich ścieżkach plików Zamień `<user_secrets_id>` `UserSecretsId` na wartość określoną w pliku *. csproj* .

Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi. Te szczegóły implementacji mogą ulec zmianie. Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>Instalowanie narzędzia do zarządzania kluczami tajnymi

Narzędzie Secret Manager jest powiązane z interfejs wiersza polecenia platformy .NET Core w zestaw .NET Core SDK 2.1.300 lub nowszym. W przypadku wersji zestaw .NET Core SDK przed 2.1.300 należy zainstalować narzędzie.

> [!TIP]
> Uruchom `dotnet --version` polecenie w powłoce poleceń, aby zobaczyć zainstalowany zestaw .NET Core SDK numer wersji.

Ostrzeżenie jest wyświetlane, jeśli używany zestaw .NET Core SDK zawiera narzędzie:

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

Zainstaluj pakiet NuGet [Microsoft. Extensions. SecretManager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) w projekcie ASP.NET Core. Na przykład:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

Aby sprawdzić poprawność instalacji narzędzi, wykonaj następujące polecenie w powłoce poleceń:

```dotnetcli
dotnet user-secrets -h
```

Narzędzie Secret Manager wyświetla przykładowe użycie, opcje i pomoc poleceń:

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> Musisz być w tym samym katalogu, co plik *. csproj* , aby uruchamiać narzędzia zdefiniowane w `DotNetCliToolReference` elementach *. csproj* pliku.

::: moniker-end

## <a name="enable-secret-storage"></a>Włącz magazyn tajny

Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.

::: moniker range=">= aspnetcore-3.0"

Narzędzie Secret Manager zawiera `init` polecenie w zestaw .NET Core SDK 3.0.100 lub nowszym. Aby użyć kluczy tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:

```dotnetcli
dotnet user-secrets init
```

Poprzednie polecenie dodaje `UserSecretsId` element `PropertyGroup` w pliku *. csproj* . Domyślnie tekst `UserSecretsId` wewnętrzny jest identyfikatorem GUID. Wewnętrzny tekst jest dowolny, ale jest unikatowy dla projektu.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Aby użyć kluczy tajnych użytkownika, `UserSecretsId` Zdefiniuj element `PropertyGroup` w pliku *. csproj* . Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu. Deweloperzy zwykle generują identyfikator GUID dla `UserSecretsId`.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego. Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku *. csproj* .

## <a name="set-a-secret"></a>Ustaw klucz tajny

Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości. Wpis tajny jest skojarzony z `UserSecretsId` wartością projektu. Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu `ServiceApiKey` z właściwością.

Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów. Użyj opcji, aby podać ścieżkę systemu plików, w której znajduje się plik *. csproj.* `--project` Na przykład:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Spłaszczanie struktury JSON w programie Visual Studio

Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera plik Secret *. JSON* w edytorze tekstów. Zastąp zawartość Secret *. JSON* wartościami par klucz-wartość, które mają być przechowywane. Na przykład:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON jest spłaszczona po modyfikacji za `dotnet user-secrets remove` pośrednictwem `dotnet user-secrets set`lub. Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu. Zmodyfikowany plik jest podobny do następującego:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Ustawianie wielu wpisów tajnych

Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia. W poniższym przykładzie zawartość pliku *Input. JSON* jest potoku do `set` polecenia.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Dostęp do klucza tajnego

[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do wpisów tajnych usługi Secret Manager.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Jeśli projekt jest przeznaczony .NET Framework, zainstaluj pakiet NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

W ASP.NET Core 2,0 lub nowszej Źródło konfiguracji użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt wywoła <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> nowe wystąpienie hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi. `CreateDefaultBuilder`wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> się, <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> gdy <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>jest:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> , wywołując `Startup` w konstruktorze. Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Zainstaluj pakiet NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

Dodaj źródło konfiguracji kluczy tajnych użytkownika z wywołaniem <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> `Startup` w konstruktorze:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

Wpisy tajne użytkownika można pobrać za `Configuration` pośrednictwem interfejsu API:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>Mapuj wpisy tajne do POCO

Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Aby zmapować poprzednie wpisy tajne do poco, użyj `Configuration` funkcji [powiązania grafu obiektów](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API. Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp `ServiceApiKey` do wartości właściwości:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

Wpisy tajne `Movies:ServiceApiKey`isą mapowane na odpowiednie właściwości w `MovieSettings`programie: `Movies:ConnectionString`

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zastępowanie ciągów hasłami tajnymi

Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne. Na przykład parametry połączenia z bazą danych przechowywane w pliku *appSettings. JSON* mogą zawierać hasło dla określonego użytkownika:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego. Przykład:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Usuń parę klucz-wartość z parametrów połączenia w pliku *appSettings. JSON.* `Password` Na przykład:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> właściwości obiektu, aby zakończyć parametry połączenia:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>Wystaw wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Wyświetlane są następujące dane wyjściowe:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w formacie Secret *. JSON*.

## <a name="remove-a-single-secret"></a>Usuń pojedynczy klucz tajny

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Plik Secret *. JSON* aplikacji został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Uruchomiony `dotnet user-secrets list` program wyświetla następujący komunikat:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Usuń wszystkie wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z pliku Secret *. JSON* :

```json
{}
```

Uruchomiony `dotnet user-secrets list` program wyświetla następujący komunikat:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
