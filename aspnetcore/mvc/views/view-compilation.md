---
title: Kompilowanie pliku razor i wstępnej kompilacji w programie ASP.NET Core
author: rick-anderson
description: Więcej informacji na temat zalet wstępnej kompilacji w plikach Razor i sposób wykonania wstępnej kompilacji pliku Razor, w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 2720708f8e58fdc55b82bfb56665005170e79934
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889759"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilacja pliku razor w programie ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Pliku Razor jest kompilowana w czasie wykonywania, gdy jest wywoływany skojarzonego widoku MVC. Publikowanie pliku Razor w czasie kompilacji nie jest obsługiwane. Opcjonalnie można kompilować plikach razor na czas publikacji i wdrożonych przy użyciu aplikacji&mdash;przy użyciu narzędzia wstępnej kompilacji.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pliku Razor jest kompilowana w czasie wykonywania, po wywołaniu skojarzonego widoku Razor strony lub MVC. Publikowanie pliku Razor w czasie kompilacji nie jest obsługiwane. Opcjonalnie można kompilować plikach razor na czas publikacji i wdrożonych przy użyciu aplikacji&mdash;przy użyciu narzędzia wstępnej kompilacji.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Pliku Razor jest kompilowana w czasie wykonywania, po wywołaniu skojarzonego widoku Razor strony lub MVC. Pliki razor są kompilowane w obu kompilacji i opublikować przy użyciu [Razor SDK](xref:razor-pages/sdk).

::: moniker-end

## <a name="precompilation-considerations"></a>Zagadnienia dotyczące wstępnej kompilacji

Prekompilowanie plikach Razor efekty uboczne są następujące:

* Mniejsze opublikowany pakiet
* Krótszy czas uruchamiania
* Nie można edytować pliki Razor&mdash;Brak powiązanej zawartości z opublikowanych pakietu.

## <a name="deploy-precompiled-files"></a>Wdrażanie wstępnie skompilowanych plików

::: moniker range=">= aspnetcore-2.1"

Kompilacja i publikowania w czasie kompilacji plików Razor jest domyślnie włączona, przez zestaw SDK Razor. Po te są aktualizowane w plikach Razor do edycji jest obsługiwana w czasie kompilacji. Domyślnie tylko skompilowanych *Views.dll* i nie *.cshtml* pliki zostały wdrożone za pomocą aplikacji.

> [!IMPORTANT]
> Narzędzie wstępnej kompilacji zostaną usunięte w programie ASP.NET Core 3.0. Zalecamy przeprowadzić migrację do [Razor Sdk](xref:razor-pages/sdk).
>
> Zestaw Razor SDK jest efektywne tylko wtedy, gdy brak właściwości specyficzne dla wstępnej kompilacji są ustawione w pliku projektu. Na przykład ustawienie *.csproj* pliku `MvcRazorCompileOnPublish` właściwość `true` wyłącza zestaw Razor SDK.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Jeśli projekt jest przeznaczony dla .NET Framework, należy zainstalować [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) pakietu NuGet:

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Jeśli projekt jest przeznaczony dla platformy .NET Core, żadne zmiany nie są niezbędne.

Szablony projektów programu ASP.NET Core 2.x niejawnie ustaw `MvcRazorCompileOnPublish` właściwość `true` domyślnie. W związku z tym, ten element można bezpiecznie usunąć z *.csproj* pliku.

> [!IMPORTANT]
> Narzędzie wstępnej kompilacji zostaną usunięte w programie ASP.NET Core 3.0. Zalecamy przeprowadzić migrację do [Razor Sdk](xref:razor-pages/sdk).
>
> Wstępnej kompilacji pliku razor jest niedostępna podczas wykonywania [niezależna wdrożenia (— SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) programu ASP.NET Core 2.0.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Ustaw `MvcRazorCompileOnPublish` właściwości `true`i zainstaluj [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) pakietu NuGet. Następujące *.csproj* przykładzie wyróżniono tych ustawień:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Przygotowywanie aplikacji dla [wdrożenia zależny od struktury](/dotnet/core/deploying/#framework-dependent-deployments-fdd) z [polecenia publikowania interfejsu wiersza polecenia platformy .NET Core](/dotnet/core/tools/dotnet-publish). Na przykład wykonaj następujące polecenie w katalogu głównym projektu:

```console
dotnet publish -c Release
```

A *< project_name >. PrecompiledViews.dll* zawierający skompilowane pliki Razor jest generowany po pomyślnym zakończeniu wstępnej kompilacji. Na przykład, poniższy zrzut ekranu przedstawia zawartość *Index.cshtml* w ramach *WebApplication1.PrecompiledViews.dll*:

![Widokami razor wewnątrz biblioteki DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a>Skompiluj ponownie plikach Razor przy zmianie

<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> `AllowRecompilingViewsOnFileChange` Pobiera lub ustawia wartość określającą, jeśli pliki Razor (widokami Razor i stron Razor) są ponownie kompilowane i zaktualizowany w przypadku plików zmienią się na dysku.

Po ustawieniu `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) skonfigurowane zegarki zmian w plikach Razor w <xref:Microsoft.Extensions.FileProviders.IFileProvider> wystąpień.

Wartość domyślna to `true` dla:

* Platforma ASP.NET Core 2.1 lub starszej aplikacji.
* Aplikacje platformy ASP.NET Core 2,2 lub nowszym w środowisku programistycznym.

`AllowRecompilingViewsOnFileChange` jest skojarzony z przełącznikiem zgodności i zapewniają różne zachowanie w zależności od skonfigurowanych zgodność wersji aplikacji. Konfigurowanie aplikacji przez ustawienie `AllowRecompilingViewsOnFileChange` ma pierwszeństwo przed wartość też dorozumianych przez wersja zgodności aplikacji.

Jeśli wersja zgodności aplikacji jest równa <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> lub wcześniej, `AllowRecompilingViewsOnFileChange` ustawiono `true` , chyba że jawnie skonfigurowane.

Jeśli wersja zgodności aplikacji jest równa `CompatibilityVersion.Version_2_2` lub nowszym, `AllowRecompilingViewsOnFileChange` ustawiono `false` chyba, że środowisko jest rozwoju lub wartość jest jawnie skonfigurowany.

Wskazówki i przykłady ustawienie wersji zgodności aplikacji, zobacz <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Dodatkowe zasoby

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
