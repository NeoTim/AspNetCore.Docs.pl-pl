---
title: Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core
author: scottaddie
description: Dowiedz się, jak używać interfejsu wiersza polecenia LibMan w projekcie ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664634"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Interfejs wiersza polecenia [LibMan](xref:client-side/libman/index) to międzyplatformowe narzędzie, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalacja

Aby zainstalować interfejs wiersza polecenia LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Aby zainstalować interfejs wiersza polecenia LibMan z określonego źródła pakietu NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

W poprzednim przykładzie jest instalowane narzędzie globalne .NET Core z pliku *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* lokalnego komputera z systemem Windows.

## <a name="usage"></a>Sposób użycia

Po pomyślnej instalacji interfejsu wiersza polecenia może być używane następujące polecenie:

```console
libman
```

Aby wyświetlić zainstalowaną wersję interfejsu wiersza polecenia:

```console
libman --version
```

Wyświetlanie dostępnych poleceń interfejsu wiersza polecenia:

```console
libman --help
```

Poprzednie polecenie wyświetla dane wyjściowe podobne do następujących:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.

## <a name="initialize-libman-in-the-project"></a>Inicjuj LibMan w projekcie

`libman init` polecenie tworzy plik *Libman. JSON* , jeśli taki nie istnieje. Plik jest tworzony z domyślną zawartością szablonu elementu.

### <a name="synopsis"></a>Streszczenie

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman init`:

* `-d|--default-destination <PATH>`

  Ścieżka względna do bieżącego folderu. Pliki bibliotek są instalowane w tej lokalizacji, jeśli dla biblioteki w *Libman. JSON*nie zdefiniowano żadnej właściwości `destination`. Wartość `<PATH>` jest zapisywana na właściwości `defaultDestination` pliku *Libman. JSON*.

* `-p|--default-provider <PROVIDER>`

  Dostawca, który ma być używany, jeśli nie zdefiniowano żadnego dostawcy dla danej biblioteki. Wartość `<PROVIDER>` jest zapisywana na właściwości `defaultProvider` pliku *Libman. JSON*. Zastąp `<PROVIDER>` jedną z następujących wartości:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby utworzyć plik *Libman. JSON* w projekcie ASP.NET Core:

* Przejdź do katalogu głównego projektu.
* Uruchom następujące polecenie:

  ```console
  libman init
  ```

* Wpisz nazwę domyślnego dostawcy lub naciśnij `Enter`, aby użyć domyślnego dostawcy CDNJS. Prawidłowe wartości to:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman init — polecenie — domyślny dostawca](_static/libman-init-provider.png)

Plik *Libman. JSON* zostanie dodany do katalogu głównego projektu z następującą zawartością:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Dodaj pliki biblioteki

Polecenie `libman install` pobiera i instaluje pliki bibliotek w projekcie. Plik *Libman. JSON* zostanie dodany, jeśli taki nie istnieje. Plik *Libman. JSON* został zmodyfikowany w celu przechowywania szczegółów konfiguracji dla plików biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do zainstalowania. Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0`).

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman install`:

* `-d|--destination <PATH>`

  Lokalizacja, w której ma zostać zainstalowana Biblioteka. Jeśli nie zostanie określony, zostanie użyta domyślna lokalizacja. Jeśli w pliku *Libman. JSON*nie określono właściwości `defaultDestination`, ta opcja jest wymagana.

* `--files <FILE>`

  Określ nazwę pliku, który ma zostać zainstalowany z biblioteki. Jeśli nie zostanie określony, wszystkie pliki z biblioteki są zainstalowane. Podaj jedną `--files` opcji na plik do zainstalowania. Ścieżki względne są również obsługiwane. Na przykład: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nazwa dostawcy do użycia podczas pozyskiwania biblioteki. Zastąp `<PROVIDER>` jedną z następujących wartości:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Jeśli nie zostanie określony, zostanie użyta Właściwość `defaultProvider` w pliku *Libman. JSON* . Jeśli w pliku *Libman. JSON*nie określono właściwości `defaultProvider`, ta opcja jest wymagana.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Rozważmy następujący plik *Libman. JSON* :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Aby zainstalować plik jQuery w wersji 3.2.1 *jQuery. min. js* do folderu *wwwroot/scripts/jQuery* przy użyciu dostawcy CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Plik *Libman. JSON* jest podobny do następującego:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Aby zainstalować pliki *Calendar. js* i *Calendar. css* z pliku *C:\\temp\\contosoCalendar\\* przy użyciu dostawcy systemu plików:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Następujący monit pojawia się z dwóch powodów:

* Plik *Libman. JSON* nie zawiera właściwości `defaultDestination`.
* Polecenie `libman install` nie zawiera opcji `-d|--destination`.

![Libman — polecenie instalacji — miejsce docelowe](_static/libman-install-destination.png)

Po zaakceptowaniu domyślnego miejsca docelowego plik *Libman. JSON* jest podobny do następującego:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Przywróć pliki biblioteki

`libman restore` polecenie instaluje pliki bibliotek zdefiniowane w *Libman. JSON*. Mają zastosowanie następujące zasady:

* Jeśli w katalogu głównym projektu nie istnieje plik *Libman. JSON* , zwracany jest błąd.
* Jeśli Biblioteka określa dostawcę, właściwość `defaultProvider` w *Libman. JSON* jest ignorowana.
* Jeśli Biblioteka określa miejsce docelowe, właściwość `defaultDestination` w *Libman. JSON* jest ignorowana.

### <a name="synopsis"></a>Streszczenie

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman restore`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby przywrócić pliki biblioteki zdefiniowane w *Libman. JSON*:

```console
libman restore
```

## <a name="delete-library-files"></a>Usuń pliki biblioteki

Polecenie `libman clean` usuwa pliki bibliotek, które zostały wcześniej przywrócone za pośrednictwem LibMan. Foldery, które staną się puste po usunięciu tej operacji. Pliki biblioteki "skojarzone konfiguracje we właściwości `libraries` *Libman. JSON* nie są usuwane.

### <a name="synopsis"></a>Streszczenie

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman clean`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby usunąć pliki biblioteki zainstalowane za pośrednictwem LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstaluj pliki biblioteki

`libman uninstall` polecenie:

* Usuwa wszystkie pliki skojarzone z określoną biblioteką z lokalizacji docelowej w pliku *Libman. JSON*.
* Usuwa skojarzoną konfigurację biblioteki z *Libman. JSON*.

Wystąpił błąd, gdy:

* W katalogu głównym projektu nie istnieje plik *Libman. JSON* .
* Określona biblioteka nie istnieje.

Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.

### <a name="synopsis"></a>Streszczenie

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do odinstalowania. Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0`).

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman uninstall`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Rozważmy następujący plik *Libman. JSON* :

[!code-json[](samples/LibManSample/libman.json)]

* Aby odinstalować jQuery, jedno z następujących poleceń powiedzie się:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Aby odinstalować pliki Lodash zainstalowane za pośrednictwem dostawcy `filesystem`:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Zaktualizuj wersję biblioteki

Polecenie `libman update` aktualizuje bibliotekę zainstalowaną za pośrednictwem LibMan do określonej wersji.

Wystąpił błąd, gdy:

* W katalogu głównym projektu nie istnieje plik *Libman. JSON* .
* Określona biblioteka nie istnieje.

Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.

### <a name="synopsis"></a>Streszczenie

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do zaktualizowania.

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman update`:

* `-pre`

  Uzyskaj najnowszą wersję wstępną biblioteki.

* `--to <VERSION>`

  Uzyskaj określoną wersję biblioteki.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

* Aby zaktualizować jQuery do najnowszej wersji:

  ```console
  libman update jquery
  ```

* Aby zaktualizować jQuery do wersji 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Aby zaktualizować jQuery do najnowszej wersji wstępnej:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Zarządzaj pamięcią podręczną biblioteki

`libman cache` polecenie zarządza pamięcią podręczną biblioteki LibMan. Dostawca `filesystem` nie korzysta z pamięci podręcznej biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumenty

`PROVIDER`

Używane tylko z `clean` polecenie. Określa pamięć podręczną dostawcy do oczyszczenia. Prawidłowe wartości to:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla polecenia `libman cache`:

* `--files`

  Wyświetl listę nazw plików, które są buforowane.

* `--libraries`

  Wyświetl listę nazw bibliotek, które są buforowane.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

* Aby wyświetlić nazwy buforowanych bibliotek na dostawcę, użyj jednego z następujących poleceń:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Wyświetlane są dane wyjściowe podobne do następujących:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Aby wyświetlić nazwy plików bibliotek w pamięci podręcznej na dostawcę:

  ```console
  libman cache list --files
  ```

  Wyświetlane są dane wyjściowe podobne do następujących:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Zwróć uwagę, że powyższe dane wyjściowe pokazują, że jQuery w wersji 3.2.1 i 3.3.1 są buforowane w ramach dostawcy CDNJS.

* Aby opróżnić pamięć podręczną biblioteki dla dostawcy CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po opróżnieniu pamięci podręcznej dostawcy CDNJS polecenie `libman cache list` wyświetla następujące elementy:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Aby opróżnić pamięć podręczną dla wszystkich obsługiwanych dostawców:

  ```console
  libman cache clean
  ```

  Po opróżnieniu wszystkich pamięci podręcznych dostawcy polecenie `libman cache list` wyświetla następujące elementy:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Instalowanie narzędzia globalnego](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
