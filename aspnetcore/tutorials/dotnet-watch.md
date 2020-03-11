---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików
author: rick-anderson
description: W tym samouczku pokazano, jak zainstalować i używać narzędzia obserwatora plików interfejs wiersza polecenia platformy .NET Core (czujka dotnet) w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667413"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="f5018-103">Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików</span><span class="sxs-lookup"><span data-stu-id="f5018-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="f5018-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="f5018-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="f5018-105">[czujka dotnet](https://www.nuget.org/packages/dotnet-watch) jest narzędziem, które uruchamia [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools) polecenie, gdy pliki źródłowe zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="f5018-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="f5018-106">Na przykład zmiana pliku może wyzwolić kompilację, wykonanie testu lub wdrożenie.</span><span class="sxs-lookup"><span data-stu-id="f5018-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="f5018-107">W tym samouczku jest wykorzystywany istniejący internetowy interfejs API z dwoma punktami końcowymi: jeden, który zwraca sumę i jeden z nich zwraca produkt.</span><span class="sxs-lookup"><span data-stu-id="f5018-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="f5018-108">Metoda produktu zawiera usterkę, która została rozwiązana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="f5018-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="f5018-109">Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="f5018-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="f5018-110">Składa się z dwóch projektów: *webapp* (ASP.NET Core Web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).</span><span class="sxs-lookup"><span data-stu-id="f5018-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="f5018-111">W powłoce poleceń przejdź do folderu *webapp* .</span><span class="sxs-lookup"><span data-stu-id="f5018-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="f5018-112">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f5018-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="f5018-113">Możesz użyć `dotnet run --project <PROJECT>`, aby określić projekt do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="f5018-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="f5018-114">Na przykład uruchomienie `dotnet run --project WebApp` z poziomu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *webapp* .</span><span class="sxs-lookup"><span data-stu-id="f5018-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="f5018-115">W danych wyjściowych konsoli są wyświetlane komunikaty podobne do następujących (wskazuje, że aplikacja działa i oczekuje na żądania):</span><span class="sxs-lookup"><span data-stu-id="f5018-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="f5018-116">W przeglądarce sieci Web przejdź do `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="f5018-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="f5018-117">Powinien zostać wyświetlony wynik `9`.</span><span class="sxs-lookup"><span data-stu-id="f5018-117">You should see the result of `9`.</span></span>

<span data-ttu-id="f5018-118">Przejdź do interfejsu API produktu (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="f5018-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="f5018-119">Zwraca `9`, nie `20` zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="f5018-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="f5018-120">Ten problem został rozwiązany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="f5018-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="f5018-121">Dodawanie `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="f5018-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="f5018-122">Narzędzie obserwatora plików `dotnet watch` jest dołączone do wersji 2.1.300 zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f5018-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="f5018-123">Poniższe kroki są wymagane w przypadku korzystania ze starszej wersji zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f5018-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="f5018-124">Dodaj odwołanie do pakietu `Microsoft.DotNet.Watcher.Tools` do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="f5018-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="f5018-125">Zainstaluj pakiet `Microsoft.DotNet.Watcher.Tools`, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f5018-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="f5018-126">Uruchamianie poleceń interfejs wiersza polecenia platformy .NET Core przy użyciu `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f5018-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="f5018-127">Każde [polecenie interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools#cli-commands) można uruchomić z `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="f5018-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="f5018-128">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f5018-128">For example:</span></span>

| <span data-ttu-id="f5018-129">Polecenie</span><span class="sxs-lookup"><span data-stu-id="f5018-129">Command</span></span> | <span data-ttu-id="f5018-130">Polecenie z zegarkiem</span><span class="sxs-lookup"><span data-stu-id="f5018-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="f5018-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="f5018-131">dotnet run</span></span> | <span data-ttu-id="f5018-132">uruchomienie czujka dotnet</span><span class="sxs-lookup"><span data-stu-id="f5018-132">dotnet watch run</span></span> |
| <span data-ttu-id="f5018-133">uruchomienie dotnet-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="f5018-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="f5018-134">uruchomienie czujka dotnet-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="f5018-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="f5018-135">uruchomienie dotnet-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="f5018-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="f5018-136">uruchomienie czujka dotnet-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="f5018-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="f5018-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="f5018-137">dotnet test</span></span> | <span data-ttu-id="f5018-138">Test czujki dotnet</span><span class="sxs-lookup"><span data-stu-id="f5018-138">dotnet watch test</span></span> |

<span data-ttu-id="f5018-139">Uruchom `dotnet watch run` w folderze *webapp* .</span><span class="sxs-lookup"><span data-stu-id="f5018-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="f5018-140">Dane wyjściowe konsoli wskazują, `watch` zostało uruchomione.</span><span class="sxs-lookup"><span data-stu-id="f5018-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="f5018-141">Możesz użyć `dotnet watch --project <PROJECT>`, aby określić projekt do obejrzenia.</span><span class="sxs-lookup"><span data-stu-id="f5018-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="f5018-142">Na przykład uruchomienie `dotnet watch --project WebApp run` z poziomu głównego przykładowej aplikacji spowoduje również uruchomienie i obejrzyj projekt *webapp* .</span><span class="sxs-lookup"><span data-stu-id="f5018-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="f5018-143">Wprowadzanie zmian przy użyciu `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f5018-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="f5018-144">Upewnij się, że `dotnet watch` jest uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="f5018-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="f5018-145">Usuń usterkę w `Product` metodzie *MathController.cs* , aby zwracała produkt, a nie sumę:</span><span class="sxs-lookup"><span data-stu-id="f5018-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="f5018-146">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f5018-146">Save the file.</span></span> <span data-ttu-id="f5018-147">Dane wyjściowe konsoli wskazują, że `dotnet watch` wykryła zmianę pliku i ponownie uruchomiła aplikację.</span><span class="sxs-lookup"><span data-stu-id="f5018-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="f5018-148">Sprawdź, czy `http://localhost:<port number>/api/math/product?a=4&b=5` zwraca prawidłowy wynik.</span><span class="sxs-lookup"><span data-stu-id="f5018-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="f5018-149">Uruchom testy przy użyciu `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f5018-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="f5018-150">Zmień metodę `Product` *MathController.cs* z powrotem, aby zwracać sumę.</span><span class="sxs-lookup"><span data-stu-id="f5018-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="f5018-151">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f5018-151">Save the file.</span></span>
1. <span data-ttu-id="f5018-152">W powłoce poleceń przejdź do folderu *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="f5018-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="f5018-153">Uruchom [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="f5018-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="f5018-154">Uruchom polecenie `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="f5018-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="f5018-155">Dane wyjściowe wskazują, że test zakończył się niepowodzeniem i że obserwator oczekuje na zmiany plików:</span><span class="sxs-lookup"><span data-stu-id="f5018-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="f5018-156">Popraw kod metody `Product`, aby zwracał produkt.</span><span class="sxs-lookup"><span data-stu-id="f5018-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="f5018-157">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f5018-157">Save the file.</span></span>

<span data-ttu-id="f5018-158">`dotnet watch` wykrywa zmianę pliku i ponownie uruchamia testy.</span><span class="sxs-lookup"><span data-stu-id="f5018-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="f5018-159">Dane wyjściowe konsoli wskazują zakończono testy.</span><span class="sxs-lookup"><span data-stu-id="f5018-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="f5018-160">Dostosuj listę plików do czujki</span><span class="sxs-lookup"><span data-stu-id="f5018-160">Customize files list to watch</span></span>

<span data-ttu-id="f5018-161">Domyślnie program `dotnet-watch` śledzi wszystkie pliki zgodne z następującymi wzorcami globalizowania:</span><span class="sxs-lookup"><span data-stu-id="f5018-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="f5018-162">Więcej elementów można dodać do listy obserwacje, edytując plik *csproj* .</span><span class="sxs-lookup"><span data-stu-id="f5018-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="f5018-163">Elementy można określać pojedynczo lub przy użyciu wzorców globalizowania.</span><span class="sxs-lookup"><span data-stu-id="f5018-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="f5018-164">Rezygnacja z plików, które mają być obserwowane</span><span class="sxs-lookup"><span data-stu-id="f5018-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="f5018-165">`dotnet-watch` można skonfigurować do ignorowania ustawień domyślnych.</span><span class="sxs-lookup"><span data-stu-id="f5018-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="f5018-166">Aby zignorować określone pliki, Dodaj atrybut `Watch="false"` do definicji elementu w pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="f5018-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="f5018-167">Niestandardowe projekty czujki</span><span class="sxs-lookup"><span data-stu-id="f5018-167">Custom watch projects</span></span>

<span data-ttu-id="f5018-168">`dotnet-watch` nie jest ograniczony C# do projektów.</span><span class="sxs-lookup"><span data-stu-id="f5018-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="f5018-169">Niestandardowe projekty czujki mogą być tworzone w celu obsługi różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f5018-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="f5018-170">Rozważmy następujący układ projektu:</span><span class="sxs-lookup"><span data-stu-id="f5018-170">Consider the following project layout:</span></span>

* <span data-ttu-id="f5018-171">**badan**</span><span class="sxs-lookup"><span data-stu-id="f5018-171">**test/**</span></span>
  * <span data-ttu-id="f5018-172">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="f5018-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="f5018-173">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="f5018-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="f5018-174">Jeśli celem jest oglądanie obu projektów, Utwórz niestandardowy plik projektu, który został skonfigurowany do oglądania obu projektów:</span><span class="sxs-lookup"><span data-stu-id="f5018-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="f5018-175">Aby rozpocząć obserwowanie plików w obu projektach, przejdź do folderu *testowego* .</span><span class="sxs-lookup"><span data-stu-id="f5018-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="f5018-176">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f5018-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="f5018-177">VSTest wykonuje się, gdy jakiekolwiek zmiany pliku w każdym projekcie testowym.</span><span class="sxs-lookup"><span data-stu-id="f5018-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="f5018-178">`dotnet-watch` w serwisie GitHub</span><span class="sxs-lookup"><span data-stu-id="f5018-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="f5018-179">`dotnet-watch` jest częścią [repozytorium dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="f5018-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
