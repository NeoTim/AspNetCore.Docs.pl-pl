---
title: Używanie zestawów startowych hostingu w ASP.NET Core
author: guardrex
description: Dowiedz się, jak ulepszyć aplikację ASP.NET Core z zestawu zewnętrznego przy użyciu implementacji IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c1ba742dda64296348898ec6a15ba725501dcb4f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391016"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Używanie zestawów startowych hostingu w ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (uruchamianie hostingu) dodaje usprawnienia do aplikacji podczas uruchamiania z zestawu zewnętrznego. Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowym dostawcom konfiguracji lub usługom aplikację.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup — atrybut

Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) wskazuje obecność zestawu startowego hostingu do aktywowania w czasie wykonywania.

Zestaw wpisów lub zestaw zawierający klasę `Startup` jest automatycznie skanowany dla atrybutu `HostingStartup`. Lista zestawów do wyszukiwania atrybutów `HostingStartup` jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Lista zestawów do wykluczenia z odnajdywania jest ładowana z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

W poniższym przykładzie przestrzeń nazw zestawu startowego hostingu jest `StartupEnhancement`. Klasa zawierająca kod uruchomienia hostingu jest `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy implementacji hostingu `IHostingStartup`.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Odkryj załadowane zestawy uruchamiania hostingu

Aby odnaleźć załadowane zestawy uruchamiania hostingu, Włącz rejestrowanie i Sprawdź dzienniki aplikacji. Błędy występujące podczas rejestrowania zestawów. Załadowane zestawy startowe hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Wyłącz automatyczne ładowanie zestawów startowych hostingu

Aby wyłączyć automatyczne ładowanie zestawów startowych hostingu, należy użyć jednej z następujących metod:

* Aby zapobiec ładowaniu wszystkich początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości `true` lub `1`:

  * Zapobiegaj ustawieniu konfiguracji hosta uruchamiania hostingu:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * Zmienna środowiskowa `ASPNETCORE_PREVENTHOSTINGSTARTUP`.

* Aby zapobiec ładowaniu określonych początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości rozdzielanej średnikami ciąg początkowych zestawów startowych do wykluczenia podczas uruchamiania:

  * Uruchamianie hostingu Wyklucz ustawienia konfiguracja hosta:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * Zmienna środowiskowa `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Jeśli ustawienia konfiguracji hosta i zmienna środowiskowa są ustawione, ustawienie hosta steruje zachowaniem.

Wyłączenie hostingu zestawów startowych przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.

## <a name="project"></a>Projekt

Utwórz uruchamianie hostingu przy użyciu jednego z następujących typów projektów:

* [Biblioteka klas](#class-library)
* [Aplikacja konsolowa bez punktu wejścia](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteka klas

Rozszerzenie uruchamiania hostingu można podać w bibliotece klas. Biblioteka zawiera atrybut `HostingStartup`.

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera Razor Pages aplikacji, *HostingStartupApp*i biblioteki klas *HostingStartupLibrary*. Biblioteka klas:

* Zawiera klasę uruchamiania hostingu, `ServiceKeyInjection`, która implementuje `IHostingStartup`. `ServiceKeyInjection` dodaje parę ciągów usługi do konfiguracji aplikacji za pomocą dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zawiera atrybut `HostingStartup`, który identyfikuje przestrzeń nazw i klasę uruchomienia hostingu.

Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> klasy `ServiceKeyInjection` używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący bibliotekę klas:

*HostingStartupApp/Pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*. Pakiet ma takie same charakterystyki biblioteki klas opisanej wcześniej. Pakiet:

* Zawiera klasę uruchamiania hostingu, `ServiceKeyInjection`, która implementuje `IHostingStartup`. `ServiceKeyInjection` dodaje parę ciągów usługi do konfiguracji aplikacji.
* Zawiera atrybut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący pakiet:

*HostingStartupApp/Pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplikacja konsolowa bez punktu wejścia

*Ta metoda jest dostępna tylko dla aplikacji platformy .NET Core, a nie .NET Framework.*

Rozszerzenie dynamicznego uruchamiania hostingu, które nie wymaga odwołania do czasu kompilowania dla aktywacji, można dostarczyć w aplikacji konsolowej bez punktu wejścia, który zawiera atrybut `HostingStartup`. Opublikowanie aplikacji konsolowej tworzy zestaw startowy hostujący, który może być używany z magazynu środowiska uruchomieniowego.

W tym procesie jest używana aplikacja konsolowa bez punktu wejścia, ponieważ:

* Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu. Plik zależności to zasób aplikacji możliwy do uruchomienia, który jest tworzony przez opublikowanie aplikacji, a nie biblioteki.
* Biblioteki nie można dodać bezpośrednio do [magazynu pakietów środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store), który wymaga projektu możliwy do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.

Podczas tworzenia dynamicznego uruchamiania hostingu:

* Zestaw startowy obsługujący hosting jest tworzony z poziomu aplikacji konsolowej bez punktu wejścia, który:
  * Zawiera klasę, która zawiera implementację `IHostingStartup`.
  * Zawiera atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do identyfikowania klasy implementacji `IHostingStartup`.
* Aplikacja konsolowa zostanie opublikowana w celu uzyskania zależności uruchamiania hostingu. Wynikiem publikowania aplikacji konsolowej jest to, że nieużywane zależności są przycinane z pliku zależności.
* Plik zależności został zmodyfikowany w celu ustawienia lokalizacji środowiska uruchomieniowego zestawu startowego hostingu.
* Zestaw startowy hostingu i jego plik zależności są umieszczane w magazynie pakietów środowiska uruchomieniowego. Aby odnaleźć zestaw startowy hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.

Aplikacja konsolowa odwołuje się do pakietu [Microsoft. AspNetCore. hosting. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identyfikuje klasę jako implementację `IHostingStartup` do załadowania i wykonania podczas kompilowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>. W poniższym przykładzie przestrzeń nazw jest `StartupEnhancement`, a Klasa jest `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Klasa implementuje `IHostingStartup`. Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> w celu dodania ulepszeń do aplikacji. `IHostingStartup.Configure` w zestawie startowym hostingu jest wywoływany przez środowisko uruchomieniowe przed `Startup.Configure` w kodzie użytkownika, dzięki czemu kod użytkownika może zastępować każdą konfigurację podaną przez zestaw startowy hostingu.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Podczas kompilowania projektu `IHostingStartup` plik zależności ( *. deps. JSON*) ustawia lokalizację `runtime` zestawu do folderu *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Wyświetlana jest tylko część pliku. Nazwa zestawu w przykładzie jest `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfiguracja dostarczona przez uruchomienie hostingu

Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma mieć pierwszeństwo:

1. Podaj konfigurację aplikacji przy użyciu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>, aby załadować konfigurację po wykonaniu delegatów <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji. Hostowanie konfiguracji uruchamiania ma wyższy priorytet niż konfiguracja aplikacji przy użyciu tego podejścia.
1. Podaj konfigurację aplikacji przy użyciu <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>, aby załadować konfigurację przed wykonaniem delegatów <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji. Wartości konfiguracyjne aplikacji mają pierwszeństwo przed tymi, które są udostępniane przez uruchamianie hostingu przy użyciu tego podejścia.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Określ zestaw startowy hostingu

W przypadku biblioteki klas lub uruchamiania hostingu obsługiwanej przez aplikację konsoli Określ nazwę zestawu startowego hostingu w zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Zmienna środowiskowa to rozdzielana średnikami lista zestawów.

Tylko hosting zestawów startowych jest skanowany pod kątem atrybutu `HostingStartup`. W przypadku przykładowej aplikacji, *HostingStartupApp*, aby odnaleźć opisane wcześniej uruchomienia hostingu, zmienna środowiskowa jest ustawiona na następującą wartość:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Zestaw startowy obsługujący hosting można również ustawić przy użyciu ustawienia konfiguracji hosta zestawów uruchamiania hostingu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Gdy są obecne wiele asemblerów uruchamiania hostingu, ich metody <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> są wykonywane w kolejności, w jakiej są wyświetlane zestawy.

## <a name="activation"></a>Aktywacja

Opcje hostingu aktywacji uruchamiania są następujące:

* Aktywacja [magazynu środowiska uruchomieniowego](#runtime-store) &ndash; nie wymaga odwołania do czasu kompilacji na potrzeby aktywacji. Przykładowa aplikacja umieszcza zestaw startowy obsługujący i pliki zależności w folderze, *wdrożeniu*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielomaszynowym. Folder *wdrożenia* zawiera także skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby umożliwić uruchamianie hostingu.
* Odwołanie do czasu kompilacji wymagane do aktywacji
  * [Pakiet NuGet](#nuget-package)
  * [Folder bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Implementacja uruchamiania hostingu jest umieszczana w [magazynie w czasie wykonywania](/dotnet/core/deploying/runtime-store). Informacje o odwołaniu do zestawu nie są wymagane przez rozszerzoną aplikację.

Po skompilowaniu uruchomienia hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

W przykładowej aplikacji (projekt*RuntimeStore* ) używane jest następujące polecenie:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby środowisko uruchomieniowe wykrywało magazyn środowiska uruchomieniowego, lokalizacja magazynu środowiska uruchomieniowego jest dodawana do zmiennej środowiskowej `DOTNET_SHARED_STORE`.

**Modyfikuj i umieść plik zależności uruchamiania hostingu**

Aby aktywować rozszerzanie bez odwołania do pakietu do rozszerzenia, określ dodatkowe zależności dla środowiska uruchomieniowego z `additionalDeps`. `additionalDeps` umożliwia:

* Rozwiń Graf biblioteki aplikacji, dostarczając zestaw dodatkowych plików *. deps. JSON* do scalenia z własnym plikiem *. deps. JSON* podczas uruchamiania.
* Ustaw możliwość odnajdywania i ładowania zestawu uruchamiania hostingu.

Zalecanym podejściem do generowania dodatkowego pliku zależności jest:

 1. Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska uruchomieniowego, do którego odwołuje się w poprzedniej sekcji.
 1. Usuń odwołanie do manifestu z bibliotek i sekcji `runtime` pliku z wynikiem *. deps. JSON* .

W przykładowym projekcie Właściwość `store.manifest/1.0.0` jest usuwana z sekcji `targets` i `libraries`:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umieść plik *. deps. JSON* w następującej lokalizacji:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; lokalizacja dodana do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}` &ndash; współdzielona struktura wymagana dla tego pliku zależności dodatkowych.
* `{SHARED FRAMEWORK VERSION}` &ndash; minimalna udostępniona wersja platformy.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; Nazwa zestawu rozszerzenia.

W przykładowej aplikacji (projekt*RuntimeStore* ) plik dodatkowych zależności jest umieszczany w następującej lokalizacji:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Aby środowisko uruchomieniowe wykrywało lokalizację magazynu środowiska uruchomieniowego, do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS` dodawana jest lokalizacja pliku z dodatkowymi zależnościami.

W przykładowej aplikacji (projekt*RuntimeStore* ) Kompilowanie magazynu środowiska uruchomieniowego i generowanie pliku dodatkowych zależności odbywa się przy użyciu skryptu [programu PowerShell](/powershell/scripting/powershell-scripting) .

Przykłady sposobu ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych znajdują się w temacie [Używanie wielu środowisk](xref:fundamentals/environments).

**Wdrażanie**

Aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielokomputerowym, aplikacja Przykładowa tworzy folder *wdrożenia* w opublikowanych danych wyjściowych zawierający:

* Magazyn środowiska uruchomieniowego uruchamiania hostingu.
* Plik zależności uruchamiania hostingu.
* Skrypt programu PowerShell, który tworzy lub modyfikuje `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` i `DOTNET_ADDITIONAL_DEPS`, aby obsługiwać aktywację uruchamiania hostingu. Uruchom skrypt z wiersza polecenia administracyjnego programu PowerShell w systemie wdrażania.

### <a name="nuget-package"></a>Pakiet NuGet

Rozszerzenie uruchamiania hostingu można dostarczyć w pakiecie NuGet. Pakiet ma atrybut `HostingStartup`. Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Plik projektu aplikacji rozszerzonej udostępnia odwołanie do pakietu dla uruchomienia hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji). Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji ( *. deps. JSON*). Takie podejście ma zastosowanie do pakietu zestawu startowego hostingu opublikowanego w [NuGet.org](https://www.nuget.org/).
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).

Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska uruchomieniowego, zobacz następujące tematy:

* [Jak utworzyć pakiet NuGet przy użyciu narzędzi międzyplatformowych](/dotnet/core/deploying/creating-nuget-packages)
* [Publikowanie pakietów](/nuget/create-packages/publish-a-package)
* [Magazyn pakietu środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Folder bin projektu

Rozszerzanie uruchamiania hostingu może być dostarczone przez zestaw wdrożony przez *bin*w aplikacji rozszerzonej. Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Plik projektu aplikacji rozszerzonej tworzy odwołanie do zestawu do uruchomienia hostingu (odwołanie w czasie kompilacji). Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji ( *. deps. JSON*). Takie podejście ma zastosowanie, gdy scenariusz wdrażania wywoła odwołanie do zestawu uruchamiania hostingu (plik *. dll* ) i przenosząc zestaw do jednego z tych metod:
  * Projekt zużywający.
  * Lokalizacja dostępna przez projekt zużywający.
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).
* Podczas określania .NET Framework, zestaw jest ładowany w domyślnym kontekście ładowania, który na .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:
  * Ścieżka bazowa aplikacji &ndash; folder *bin* , w którym znajduje się plik wykonywalny ( *. exe*) aplikacji.
  * Globalna pamięć podręczna zestawów (GAC) &ndash; Magazyn GAC przechowuje zestawy, które są dostępne dla kilku aplikacji .NET Framework. Aby uzyskać więcej informacji, zobacz [jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji .NET Framework.

## <a name="sample-code"></a>Przykładowy kod

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) pokazuje hosting scenariuszy implementacji uruchamiania:

* Dwa zestawy startowe hostingu (biblioteki klas) ustawiają parę par klucz-wartość konfiguracji w pamięci:
  * Pakiet NuGet (*HostingStartupPackage*)
  * Biblioteka klas (*HostingStartupLibrary*)
* Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w sklepie uruchomieniowym (*StartupDiagnostics*). Zestaw dodaje dwie middlewares do aplikacji podczas uruchamiania, które dostarczają informacji diagnostycznych na:
  * Zarejestrowane usługi
  * Adres (schemat, host, baza ścieżki, ścieżka, ciąg zapytania)
  * Połączenie (zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)
  * Nagłówki żądań
  * Zmienne środowiskowe

Aby uruchomić przykład:

**Aktywacja z pakietu NuGet**

1. Skompiluj pakiet *HostingStartupPackage* przy użyciu polecenia [pakietu dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Dodaj nazwę zestawu pakietu *HostingStartupPackage* do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Skompiluj i uruchom aplikację. Odwołanie do pakietu jest obecne w aplikacji rozszerzonej (odwołanie w czasie kompilacji). @No__t-0 w pliku projektu aplikacji określa dane wyjściowe projektu pakietu ( *.. /HostingStartupPackage/bin/Debug*) jako źródło pakietu. Dzięki temu aplikacja może korzystać z pakietu bez przekazywania pakietu do [NuGet.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę `ServiceKeyInjection.Configure` pakietu.

Jeśli wprowadzisz zmiany w projekcie *HostingStartupPackage* i skompilujesz go ponownie, wyczyść pamięć podręczną pakietów NuGet, aby upewnić się, że *HostingStartupApp* odbierze zaktualizowany pakiet, a nie stary pakiet z lokalnej pamięci podręcznej. Aby wyczyścić lokalne pamięci podręczne NuGet, należy wykonać następujące polecenie [locale NuGet programu dotnet](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktywacja z biblioteki klas**

1. Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) .
1. Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. *bin*— Wdróż zestaw biblioteki klas w aplikacji przez skopiowanie pliku *HostingStartupLibrary. dll* z skompilowanych danych wyjściowych biblioteki klas do folderu *bin/debug* aplikacji.
1. Skompiluj i uruchom aplikację. @No__t-0 w pliku projektu aplikacji odwołuje się do zestawu biblioteki klas ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odwołanie w czasie kompilacji). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę `ServiceKeyInjection.Configure` biblioteki klas.

**Aktywacja z zestawu wdrożonego w sklepie uruchomieniowym**

1. Projekt *StartupDiagnostics* używa [programu PowerShell](/powershell/scripting/powershell-scripting) do zmodyfikowania pliku *StartupDiagnostics. deps. JSON* . Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1. Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie programu Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Wykonaj skrypt *Build. ps1* w folderze *RuntimeStore* . Skrypt:
   * Generuje pakiet `StartupDiagnostics` w folderze *obj\packages* .
   * Generuje magazyn środowiska uruchomieniowego dla `StartupDiagnostics` w folderze *magazynu* . Polecenie `dotnet store` w skrypcie używa [identyfikatora środowiska uruchomieniowego `win7-x64` (RID)](/dotnet/core/rid-catalog) do uruchomienia hostingu wdrożonego w systemie Windows. Podczas zapewniania uruchamiania hostingu dla innego środowiska uruchomieniowego należy zastąpić prawidłowy identyfikator RID w wierszu 37 skryptu. Magazyn środowiska uruchomieniowego dla `StartupDiagnostics` będzie później przenoszony do magazynu środowiska uruchomieniowego użytkownika lub systemu na komputerze, na którym zostanie użyty zestaw. Lokalizacja instalacji magazynu środowiska uruchomieniowego użytkownika dla zestawu `StartupDiagnostics` to *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.
   * Generuje `additionalDeps` dla `StartupDiagnostics` w folderze *additionalDeps* . Dodatkowe zależności byłyby później przenoszone do dodatkowych zależności użytkownika lub systemu. Użytkownik `StartupDiagnostics` dodatkowe zależności lokalizacja instalacji to *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. servicecore. app/3.0.0/StartupDiagnostics. deps. JSON*.
   * Umieszcza plik *Deploy. ps1* w folderze *Deployment* .
1. Uruchom skrypt *Deploy. ps1* w folderze *Deployment* . Dołączenie skryptu:
   * `StartupDiagnostics` do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS`.
   * Ścieżka magazynu środowiska uruchomieniowego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej `DOTNET_SHARED_STORE`.
1. Uruchom przykładową aplikację.
1. Zażądaj punktu końcowego `/services`, aby zobaczyć zarejestrowane usługi aplikacji. Zażądaj punktu końcowego `/diag`, aby wyświetlić informacje diagnostyczne.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (uruchamianie hostingu) dodaje usprawnienia do aplikacji podczas uruchamiania z zestawu zewnętrznego. Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowym dostawcom konfiguracji lub usługom aplikację.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup — atrybut

Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) wskazuje obecność zestawu startowego hostingu do aktywowania w czasie wykonywania.

Zestaw wpisów lub zestaw zawierający klasę `Startup` jest automatycznie skanowany dla atrybutu `HostingStartup`. Lista zestawów do wyszukiwania atrybutów `HostingStartup` jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Lista zestawów do wykluczenia z odnajdywania jest ładowana z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Aby uzyskać więcej informacji, zobacz [host sieci Web: hosting zestawów startowych](xref:fundamentals/host/web-host#hosting-startup-assemblies) i [hosta sieci Web: hosting z wykluczeniem uruchomienia](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

W poniższym przykładzie przestrzeń nazw zestawu startowego hostingu jest `StartupEnhancement`. Klasa zawierająca kod uruchomienia hostingu jest `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy implementacji hostingu `IHostingStartup`.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Odkryj załadowane zestawy uruchamiania hostingu

Aby odnaleźć załadowane zestawy uruchamiania hostingu, Włącz rejestrowanie i Sprawdź dzienniki aplikacji. Błędy występujące podczas rejestrowania zestawów. Załadowane zestawy startowe hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Wyłącz automatyczne ładowanie zestawów startowych hostingu

Aby wyłączyć automatyczne ładowanie zestawów startowych hostingu, należy użyć jednej z następujących metod:

* Aby zapobiec ładowaniu wszystkich początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości `true` lub `1`:
  * [Zapobiegaj](xref:fundamentals/host/web-host#prevent-hosting-startup) ustawieniu konfiguracji hosta uruchamiania hostingu.
  * Zmienna środowiskowa `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Aby zapobiec ładowaniu określonych początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości rozdzielanej średnikami ciąg początkowych zestawów startowych do wykluczenia podczas uruchamiania:
  * [Uruchamianie hostingu Wyklucz](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ustawienia konfiguracja hosta.
  * Zmienna środowiskowa `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Jeśli ustawienia konfiguracji hosta i zmienna środowiskowa są ustawione, ustawienie hosta steruje zachowaniem.

Wyłączenie hostingu zestawów startowych przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.

## <a name="project"></a>Projekt

Utwórz uruchamianie hostingu przy użyciu jednego z następujących typów projektów:

* [Biblioteka klas](#class-library)
* [Aplikacja konsolowa bez punktu wejścia](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteka klas

Rozszerzenie uruchamiania hostingu można podać w bibliotece klas. Biblioteka zawiera atrybut `HostingStartup`.

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera Razor Pages aplikacji, *HostingStartupApp*i biblioteki klas *HostingStartupLibrary*. Biblioteka klas:

* Zawiera klasę uruchamiania hostingu, `ServiceKeyInjection`, która implementuje `IHostingStartup`. `ServiceKeyInjection` dodaje parę ciągów usługi do konfiguracji aplikacji za pomocą dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zawiera atrybut `HostingStartup`, który identyfikuje przestrzeń nazw i klasę uruchomienia hostingu.

Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> klasy `ServiceKeyInjection` używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.

*HostingStartupLibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący bibliotekę klas:

*HostingStartupApp/Pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*. Pakiet ma takie same charakterystyki biblioteki klas opisanej wcześniej. Pakiet:

* Zawiera klasę uruchamiania hostingu, `ServiceKeyInjection`, która implementuje `IHostingStartup`. `ServiceKeyInjection` dodaje parę ciągów usługi do konfiguracji aplikacji.
* Zawiera atrybut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący pakiet:

*HostingStartupApp/Pages/index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplikacja konsolowa bez punktu wejścia

*Ta metoda jest dostępna tylko dla aplikacji platformy .NET Core, a nie .NET Framework.*

Rozszerzenie dynamicznego uruchamiania hostingu, które nie wymaga odwołania do czasu kompilowania dla aktywacji, można dostarczyć w aplikacji konsolowej bez punktu wejścia, który zawiera atrybut `HostingStartup`. Opublikowanie aplikacji konsolowej tworzy zestaw startowy hostujący, który może być używany z magazynu środowiska uruchomieniowego.

W tym procesie jest używana aplikacja konsolowa bez punktu wejścia, ponieważ:

* Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu. Plik zależności to zasób aplikacji możliwy do uruchomienia, który jest tworzony przez opublikowanie aplikacji, a nie biblioteki.
* Biblioteki nie można dodać bezpośrednio do [magazynu pakietów środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store), który wymaga projektu możliwy do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.

Podczas tworzenia dynamicznego uruchamiania hostingu:

* Zestaw startowy obsługujący hosting jest tworzony z poziomu aplikacji konsolowej bez punktu wejścia, który:
  * Zawiera klasę, która zawiera implementację `IHostingStartup`.
  * Zawiera atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do identyfikowania klasy implementacji `IHostingStartup`.
* Aplikacja konsolowa zostanie opublikowana w celu uzyskania zależności uruchamiania hostingu. Wynikiem publikowania aplikacji konsolowej jest to, że nieużywane zależności są przycinane z pliku zależności.
* Plik zależności został zmodyfikowany w celu ustawienia lokalizacji środowiska uruchomieniowego zestawu startowego hostingu.
* Zestaw startowy hostingu i jego plik zależności są umieszczane w magazynie pakietów środowiska uruchomieniowego. Aby odnaleźć zestaw startowy hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.

Aplikacja konsolowa odwołuje się do pakietu [Microsoft. AspNetCore. hosting. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identyfikuje klasę jako implementację `IHostingStartup` do załadowania i wykonania podczas kompilowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>. W poniższym przykładzie przestrzeń nazw jest `StartupEnhancement`, a Klasa jest `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Klasa implementuje `IHostingStartup`. Metoda <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> w celu dodania ulepszeń do aplikacji. `IHostingStartup.Configure` w zestawie startowym hostingu jest wywoływany przez środowisko uruchomieniowe przed `Startup.Configure` w kodzie użytkownika, dzięki czemu kod użytkownika może zastępować każdą konfigurację podaną przez zestaw startowy hostingu.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Podczas kompilowania projektu `IHostingStartup` plik zależności ( *. deps. JSON*) ustawia lokalizację `runtime` zestawu do folderu *bin* :

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Wyświetlana jest tylko część pliku. Nazwa zestawu w przykładzie jest `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfiguracja dostarczona przez uruchomienie hostingu

Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma mieć pierwszeństwo:

1. Podaj konfigurację aplikacji przy użyciu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>, aby załadować konfigurację po wykonaniu delegatów <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji. Hostowanie konfiguracji uruchamiania ma wyższy priorytet niż konfiguracja aplikacji przy użyciu tego podejścia.
1. Podaj konfigurację aplikacji przy użyciu <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>, aby załadować konfigurację przed wykonaniem delegatów <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji. Wartości konfiguracyjne aplikacji mają pierwszeństwo przed tymi, które są udostępniane przez uruchamianie hostingu przy użyciu tego podejścia.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Określ zestaw startowy hostingu

W przypadku biblioteki klas lub uruchamiania hostingu obsługiwanej przez aplikację konsoli Określ nazwę zestawu startowego hostingu w zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Zmienna środowiskowa to rozdzielana średnikami lista zestawów.

Tylko hosting zestawów startowych jest skanowany pod kątem atrybutu `HostingStartup`. W przypadku przykładowej aplikacji, *HostingStartupApp*, aby odnaleźć opisane wcześniej uruchomienia hostingu, zmienna środowiskowa jest ustawiona na następującą wartość:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Zestaw startowy obsługujący hosting można również ustawić przy użyciu ustawienia konfiguracji hosta [zestawów startowych](xref:fundamentals/host/web-host#hosting-startup-assemblies) .

Gdy są obecne wiele asemblerów uruchamiania hostingu, ich metody <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> są wykonywane w kolejności, w jakiej są wyświetlane zestawy.

## <a name="activation"></a>Aktywacja

Opcje hostingu aktywacji uruchamiania są następujące:

* Aktywacja [magazynu środowiska uruchomieniowego](#runtime-store) &ndash; nie wymaga odwołania do czasu kompilacji na potrzeby aktywacji. Przykładowa aplikacja umieszcza zestaw startowy obsługujący i pliki zależności w folderze, *wdrożeniu*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielomaszynowym. Folder *wdrożenia* zawiera także skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby umożliwić uruchamianie hostingu.
* Odwołanie do czasu kompilacji wymagane do aktywacji
  * [Pakiet NuGet](#nuget-package)
  * [Folder bin projektu](#project-bin-folder)

### <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Implementacja uruchamiania hostingu jest umieszczana w [magazynie w czasie wykonywania](/dotnet/core/deploying/runtime-store). Informacje o odwołaniu do zestawu nie są wymagane przez rozszerzoną aplikację.

Po skompilowaniu uruchomienia hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet](/dotnet/core/tools/dotnet-store) .

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

W przykładowej aplikacji (projekt*RuntimeStore* ) używane jest następujące polecenie:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Aby środowisko uruchomieniowe wykrywało magazyn środowiska uruchomieniowego, lokalizacja magazynu środowiska uruchomieniowego jest dodawana do zmiennej środowiskowej `DOTNET_SHARED_STORE`.

**Modyfikuj i umieść plik zależności uruchamiania hostingu**

Aby aktywować rozszerzanie bez odwołania do pakietu do rozszerzenia, określ dodatkowe zależności dla środowiska uruchomieniowego z `additionalDeps`. `additionalDeps` umożliwia:

* Rozwiń Graf biblioteki aplikacji, dostarczając zestaw dodatkowych plików *. deps. JSON* do scalenia z własnym plikiem *. deps. JSON* podczas uruchamiania.
* Ustaw możliwość odnajdywania i ładowania zestawu uruchamiania hostingu.

Zalecanym podejściem do generowania dodatkowego pliku zależności jest:

 1. Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska uruchomieniowego, do którego odwołuje się w poprzedniej sekcji.
 1. Usuń odwołanie do manifestu z bibliotek i sekcji `runtime` pliku z wynikiem *. deps. JSON* .

W przykładowym projekcie Właściwość `store.manifest/1.0.0` jest usuwana z sekcji `targets` i `libraries`:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umieść plik *. deps. JSON* w następującej lokalizacji:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; lokalizacja dodana do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}` &ndash; współdzielona struktura wymagana dla tego pliku zależności dodatkowych.
* `{SHARED FRAMEWORK VERSION}` &ndash; minimalna udostępniona wersja platformy.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; Nazwa zestawu rozszerzenia.

W przykładowej aplikacji (projekt*RuntimeStore* ) plik dodatkowych zależności jest umieszczany w następującej lokalizacji:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Aby środowisko uruchomieniowe wykrywało lokalizację magazynu środowiska uruchomieniowego, do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS` dodawana jest lokalizacja pliku z dodatkowymi zależnościami.

W przykładowej aplikacji (projekt*RuntimeStore* ) Kompilowanie magazynu środowiska uruchomieniowego i generowanie pliku dodatkowych zależności odbywa się przy użyciu skryptu [programu PowerShell](/powershell/scripting/powershell-scripting) .

Przykłady sposobu ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych znajdują się w temacie [Używanie wielu środowisk](xref:fundamentals/environments).

**Wdrażanie**

Aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielokomputerowym, aplikacja Przykładowa tworzy folder *wdrożenia* w opublikowanych danych wyjściowych zawierający:

* Magazyn środowiska uruchomieniowego uruchamiania hostingu.
* Plik zależności uruchamiania hostingu.
* Skrypt programu PowerShell, który tworzy lub modyfikuje `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` i `DOTNET_ADDITIONAL_DEPS`, aby obsługiwać aktywację uruchamiania hostingu. Uruchom skrypt z wiersza polecenia administracyjnego programu PowerShell w systemie wdrażania.

### <a name="nuget-package"></a>Pakiet NuGet

Rozszerzenie uruchamiania hostingu można dostarczyć w pakiecie NuGet. Pakiet ma atrybut `HostingStartup`. Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Plik projektu aplikacji rozszerzonej udostępnia odwołanie do pakietu dla uruchomienia hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji). Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji ( *. deps. JSON*). Takie podejście ma zastosowanie do pakietu zestawu startowego hostingu opublikowanego w [NuGet.org](https://www.nuget.org/).
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).

Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska uruchomieniowego, zobacz następujące tematy:

* [Jak utworzyć pakiet NuGet przy użyciu narzędzi międzyplatformowych](/dotnet/core/deploying/creating-nuget-packages)
* [Publikowanie pakietów](/nuget/create-packages/publish-a-package)
* [Magazyn pakietu środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Folder bin projektu

Rozszerzanie uruchamiania hostingu może być dostarczone przez zestaw wdrożony przez *bin*w aplikacji rozszerzonej. Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Plik projektu aplikacji rozszerzonej tworzy odwołanie do zestawu do uruchomienia hostingu (odwołanie w czasie kompilacji). Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji ( *. deps. JSON*). Takie podejście ma zastosowanie, gdy scenariusz wdrażania wywoła odwołanie do zestawu uruchamiania hostingu (plik *. dll* ) i przenosząc zestaw do jednego z tych metod:
  * Projekt zużywający.
  * Lokalizacja dostępna przez projekt zużywający.
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).
* Podczas określania .NET Framework, zestaw jest ładowany w domyślnym kontekście ładowania, który na .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:
  * Ścieżka bazowa aplikacji &ndash; folder *bin* , w którym znajduje się plik wykonywalny ( *. exe*) aplikacji.
  * Globalna pamięć podręczna zestawów (GAC) &ndash; Magazyn GAC przechowuje zestawy, które są dostępne dla kilku aplikacji .NET Framework. Aby uzyskać więcej informacji, zobacz [jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji .NET Framework.

## <a name="sample-code"></a>Przykładowy kod

[Przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) pokazuje hosting scenariuszy implementacji uruchamiania:

* Dwa zestawy startowe hostingu (biblioteki klas) ustawiają parę par klucz-wartość konfiguracji w pamięci:
  * Pakiet NuGet (*HostingStartupPackage*)
  * Biblioteka klas (*HostingStartupLibrary*)
* Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w sklepie uruchomieniowym (*StartupDiagnostics*). Zestaw dodaje dwie middlewares do aplikacji podczas uruchamiania, które dostarczają informacji diagnostycznych na:
  * Zarejestrowane usługi
  * Adres (schemat, host, baza ścieżki, ścieżka, ciąg zapytania)
  * Połączenie (zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)
  * Nagłówki żądań
  * Zmienne środowiskowe

Aby uruchomić przykład:

**Aktywacja z pakietu NuGet**

1. Skompiluj pakiet *HostingStartupPackage* przy użyciu polecenia [pakietu dotnet Pack](/dotnet/core/tools/dotnet-pack) .
1. Dodaj nazwę zestawu pakietu *HostingStartupPackage* do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Skompiluj i uruchom aplikację. Odwołanie do pakietu jest obecne w aplikacji rozszerzonej (odwołanie w czasie kompilacji). @No__t-0 w pliku projektu aplikacji określa dane wyjściowe projektu pakietu ( *.. /HostingStartupPackage/bin/Debug*) jako źródło pakietu. Dzięki temu aplikacja może korzystać z pakietu bez przekazywania pakietu do [NuGet.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę `ServiceKeyInjection.Configure` pakietu.

Jeśli wprowadzisz zmiany w projekcie *HostingStartupPackage* i skompilujesz go ponownie, wyczyść pamięć podręczną pakietów NuGet, aby upewnić się, że *HostingStartupApp* odbierze zaktualizowany pakiet, a nie stary pakiet z lokalnej pamięci podręcznej. Aby wyczyścić lokalne pamięci podręczne NuGet, należy wykonać następujące polecenie [locale NuGet programu dotnet](/dotnet/core/tools/dotnet-nuget-locals) :

```dotnetcli
dotnet nuget locals all --clear
```

**Aktywacja z biblioteki klas**

1. Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) .
1. Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. *bin*— Wdróż zestaw biblioteki klas w aplikacji przez skopiowanie pliku *HostingStartupLibrary. dll* z skompilowanych danych wyjściowych biblioteki klas do folderu *bin/debug* aplikacji.
1. Skompiluj i uruchom aplikację. @No__t-0 w pliku projektu aplikacji odwołuje się do zestawu biblioteki klas ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odwołanie w czasie kompilacji). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę `ServiceKeyInjection.Configure` biblioteki klas.

**Aktywacja z zestawu wdrożonego w sklepie uruchomieniowym**

1. Projekt *StartupDiagnostics* używa [programu PowerShell](/powershell/scripting/powershell-scripting) do zmodyfikowania pliku *StartupDiagnostics. deps. JSON* . Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1. Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie programu Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Wykonaj skrypt *Build. ps1* w folderze *RuntimeStore* . Skrypt:
   * Generuje pakiet `StartupDiagnostics` w folderze *obj\packages* .
   * Generuje magazyn środowiska uruchomieniowego dla `StartupDiagnostics` w folderze *magazynu* . Polecenie `dotnet store` w skrypcie używa [identyfikatora środowiska uruchomieniowego `win7-x64` (RID)](/dotnet/core/rid-catalog) do uruchomienia hostingu wdrożonego w systemie Windows. Podczas zapewniania uruchamiania hostingu dla innego środowiska uruchomieniowego należy zastąpić prawidłowy identyfikator RID w wierszu 37 skryptu. Magazyn środowiska uruchomieniowego dla `StartupDiagnostics` będzie później przenoszony do magazynu środowiska uruchomieniowego użytkownika lub systemu na komputerze, na którym zostanie użyty zestaw. Lokalizacja instalacji magazynu środowiska uruchomieniowego użytkownika dla zestawu `StartupDiagnostics` to *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.
   * Generuje `additionalDeps` dla `StartupDiagnostics` w folderze *additionalDeps* . Dodatkowe zależności byłyby później przenoszone do dodatkowych zależności użytkownika lub systemu. Użytkownik `StartupDiagnostics` dodatkowe zależności lokalizacja instalacji to *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. servicecore. App/2.2.0/StartupDiagnostics. deps. JSON*.
   * Umieszcza plik *Deploy. ps1* w folderze *Deployment* .
1. Uruchom skrypt *Deploy. ps1* w folderze *Deployment* . Dołączenie skryptu:
   * `StartupDiagnostics` do zmiennej środowiskowej `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej `DOTNET_ADDITIONAL_DEPS`.
   * Ścieżka magazynu środowiska uruchomieniowego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej `DOTNET_SHARED_STORE`.
1. Uruchom przykładową aplikację.
1. Zażądaj punktu końcowego `/services`, aby zobaczyć zarejestrowane usługi aplikacji. Zażądaj punktu końcowego `/diag`, aby wyświetlić informacje diagnostyczne.

::: moniker-end
