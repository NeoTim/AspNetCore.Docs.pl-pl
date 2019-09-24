---
title: Pakiet Microsoft. AspNetCore. App dla ASP.NET Core
author: Rick-Anderson
description: Współdzielona platforma Microsoft. AspNetCore. App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 8435445890ce00f33ab9a8692f5442b1609192da
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219113"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="0ef8d-103">Microsoft. AspNetCore. App dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ef8d-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="0ef8d-104">ASP.NET Core Shared Framework (`Microsoft.AspNetCore.App`) zawiera zestawy, które są opracowywane i obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="0ef8d-105">`Microsoft.AspNetCore.App`Program jest instalowany podczas instalowania [zestawu SDK platformy .NET Core 3,0 lub nowszego](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="0ef8d-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="0ef8d-106">*Platforma udostępniona* to zestaw zestawów (plików*dll* ), które są zainstalowane na komputerze i zawiera składnik środowiska uruchomieniowego oraz pakiet docelowy.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="0ef8d-107">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="0ef8d-108">Projekty przeznaczone dla `Microsoft.NET.Sdk.Web` zestawu SDK niejawnie odwołują się do `Microsoft.AspNetCore.App` struktury.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="0ef8d-109">Dla tych projektów nie są wymagane żadne dodatkowe odwołania:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="0ef8d-110">ASP.NET Core współdzielona struktura:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="0ef8d-111">Nie obejmuje zależności innych firm.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="0ef8d-112">Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0ef8d-113">Ta funkcja wymaga ASP.NET Core 2. x przeznaczonych dla platformy .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="0ef8d-114">Pakiet [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [](/dotnet/core/packages#metapackages) dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="0ef8d-115">Nie obejmuje zależności innych firm, z wyjątkiem [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [remotion. LINQ](https://www.nuget.org/packages/Remotion.Linq/)i [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="0ef8d-116">Te zależności innych firm są uważane za niezbędne do zapewnienia funkcji głównych funkcji.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="0ef8d-117">Obejmuje wszystkie obsługiwane pakiety przez zespół ASP.NET Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wspomniane wcześniej).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="0ef8d-118">Obejmuje wszystkie obsługiwane pakiety przez zespół Entity Framework Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wspomniane wcześniej).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="0ef8d-119">Wszystkie funkcje ASP.NET Core 2. x i Entity Framework Core 2. x są zawarte w `Microsoft.AspNetCore.App` pakiecie.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="0ef8d-120">Domyślne szablony projektu dla ASP.NET Core 2. x używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="0ef8d-121">Zalecamy stosowanie aplikacji przeznaczonych dla `Microsoft.AspNetCore.App` ASP.NET Core 2. x i Entity Framework Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="0ef8d-122">Numer `Microsoft.AspNetCore.App` wersji pakietubinding reprezentuje minimalną wersję ASP.NET Core i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="0ef8d-123">Użycie pakietu `Microsoft.AspNetCore.App` z pakietem zawiera ograniczenia wersji chroniące Twoją aplikację:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="0ef8d-124">W przypadku dołączenia pakietu, który ma zależność przechodnią (nie bezpośrednią) w `Microsoft.AspNetCore.App`pakiecie w programie, a numery wersji różnią się, pakiet NuGet wygeneruje błąd.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="0ef8d-125">Inne pakiety dodane do aplikacji nie mogą zmienić wersji pakietów uwzględnionych w programie `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="0ef8d-126">Spójność wersji zapewnia niezawodne środowisko pracy.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="0ef8d-127">`Microsoft.AspNetCore.App`została zaprojektowana tak, aby uniemożliwić nietestowaną kombinację pokrewnych bitów w tej samej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="0ef8d-128">Aplikacje korzystające z `Microsoft.AspNetCore.App` pakietubinding automatycznie wykorzystują ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="0ef8d-129">W przypadku korzystania `Microsoft.AspNetCore.App` z pakietubinding nie są wdrażane **żadne** zasoby z przywoływanych ASP.NET Core pakietów NuGet z&mdash;aplikacją, ASP.NET Core udostępnione środowisko zawiera te zasoby.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="0ef8d-130">Zasoby w udostępnionej architekturze są wstępnie skompilowane w celu poprawienia czasu uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="0ef8d-131">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="0ef8d-132">Następujący plik projektu odwołuje się `Microsoft.AspNetCore.App` do pakietu dla ASP.NET Core i reprezentuje typowy szablon ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="0ef8d-133">Poprzedzający znacznik reprezentuje typowy szablon ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="0ef8d-134">Nie określa numeru wersji odwołania do `Microsoft.AspNetCore.App` pakietu.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="0ef8d-135">W przypadku nieokreślenia wersji [niejawna](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) wersja jest określana przez zestaw SDK, czyli `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="0ef8d-136">Zalecamy użycie niejawnej wersji określonej przez zestaw SDK i niejawne ustawienie numeru wersji w odwołaniu do pakietu.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="0ef8d-137">Jeśli masz pytania dotyczące tego podejścia, pozostaw komentarz w serwisie GitHub w [dyskusji dotyczącej wersji niejawnej Microsoft. AspNetCore. app](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="0ef8d-138">Niejawna wersja jest ustawiona `major.minor.0` na dla aplikacji przenośnych.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="0ef8d-139">Mechanizm przekazujący przechodzenie do platformy udostępnionej będzie uruchamiał aplikację w najnowszej zgodnej wersji wśród zainstalowanych platform udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="0ef8d-140">Aby zagwarantować, że ta sama wersja jest używana w środowisku deweloperskim, testowym i produkcyjnym, upewnij się, że ta sama wersja udostępnionej platformy jest zainstalowana we wszystkich środowiskach.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="0ef8d-141">W przypadku aplikacji z własnym niejawnym numerem wersji jest ustawiana `major.minor.patch` struktura udostępniona w zainstalowanym zestawie SDK.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="0ef8d-142">Określenie numeru wersji w `Microsoft.AspNetCore.App` odwołaniu nie gwarantuje **,** że zostanie wybrana wersja udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="0ef8d-143">Na przykład załóżmy, że jest określona wersja "2.2.1", ale zainstalowano "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="0ef8d-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="0ef8d-144">W takim przypadku aplikacja będzie używać "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="0ef8d-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="0ef8d-145">Chociaż nie jest to zalecane, można wyłączyć funkcję wycofywania do przodu (poprawka i/lub pomocnicza).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="0ef8d-146">Aby uzyskać więcej informacji na temat przetworzenia i konfigurowania zachowań hosta dotnet, zobacz [przewinięcie hosta dotnet do przodu](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="0ef8d-147">`<Project Sdk`musi być ustawiona na `Microsoft.NET.Sdk.Web` , aby można było używać `Microsoft.AspNetCore.App`niejawnej wersji.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="0ef8d-148">Gdy `<Project Sdk="Microsoft.NET.Sdk">` jest używane (bez `.Web`końcowego):</span><span class="sxs-lookup"><span data-stu-id="0ef8d-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="0ef8d-149">Zostanie wygenerowane następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-149">The following warning is generated:</span></span>

  <span data-ttu-id="0ef8d-150">*Ostrzeżenie NU1604: Zależność projektu Microsoft. AspNetCore. App nie zawiera mniejszej granicy. Uwzględnij dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*</span><span class="sxs-lookup"><span data-stu-id="0ef8d-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="0ef8d-151">Jest to znany problem z zestawem SDK platformy .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="0ef8d-152">ASP.NET Core aktualizacji</span><span class="sxs-lookup"><span data-stu-id="0ef8d-152">Update ASP.NET Core</span></span>

<span data-ttu-id="0ef8d-153">Pakiet nie jest tradycyjnym pakietem, który został zaktualizowany przy użyciu narzędzia NuGet. [](/dotnet/core/packages#metapackages) `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="0ef8d-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="0ef8d-154">Podobnie jak `Microsoft.AspNetCore.App` , reprezentuje udostępnione środowisko uruchomieniowe, które ma specjalną semantykę wersji obsłużoną poza pakietem NuGet. `Microsoft.NETCore.App`</span><span class="sxs-lookup"><span data-stu-id="0ef8d-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="0ef8d-155">Aby uzyskać więcej informacji, zobacz [pakiety, aplikacje i struktury](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="0ef8d-156">Aby zaktualizować ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0ef8d-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="0ef8d-157">Na komputerach deweloperskich i serwerach kompilacji: Pobierz i zainstaluj [zestaw .NET Core SDK](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-157">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="0ef8d-158">Na serwerach wdrożenia: Pobierz i zainstaluj [środowisko uruchomieniowe programu .NET Core](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-158">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="0ef8d-159">Aplikacje zostaną przesunięte do najnowszej zainstalowanej wersji przy ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="0ef8d-160">Nie trzeba aktualizować `Microsoft.AspNetCore.App` numeru wersji w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="0ef8d-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="0ef8d-161">Aby uzyskać więcej informacji, zobacz [aplikacje zależne od platformy — przewinięcie do przodu](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="0ef8d-162">Jeśli aplikacja była wcześniej używana `Microsoft.AspNetCore.All`, zobacz [Migrowanie z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="0ef8d-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
