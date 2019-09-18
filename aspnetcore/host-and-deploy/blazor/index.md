---
title: Hostowanie i wdrażanie ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak hostować i wdrażać aplikacje Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 0ded2979b8576f10812e20ae3385c94fd29689c2
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081036"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="e7ee3-103">Hostowanie i wdrażanie ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e7ee3-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="e7ee3-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e7ee3-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="e7ee3-105">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e7ee3-105">Publish the app</span></span>

<span data-ttu-id="e7ee3-106">Aplikacje są publikowane do wdrożenia w konfiguracji wydania.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7ee3-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7ee3-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e7ee3-108">Wybierz pozycję **kompilacja** > **Opublikuj aplikację {aplikacja}** na pasku nawigacyjnym.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="e7ee3-109">Wybierz *element docelowy publikowania*.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-109">Select the *publish target*.</span></span> <span data-ttu-id="e7ee3-110">Aby opublikować lokalnie, wybierz pozycję **folder**.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="e7ee3-111">Zaakceptuj lokalizację domyślną w polu **Wybierz folder** lub określ inną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="e7ee3-112">Wybierz przycisk **Publikuj** .</span><span class="sxs-lookup"><span data-stu-id="e7ee3-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7ee3-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e7ee3-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e7ee3-114">Użyj [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia, aby opublikować aplikację z konfiguracją wydania:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="e7ee3-115">Opublikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompiluje](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="e7ee3-116">W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasów ładowania.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="e7ee3-117">Aplikacja webassembly Blazor jest publikowana w folderze */bin/Release/{Target Framework}/Publish/{Assembly Name}/dist* .</span><span class="sxs-lookup"><span data-stu-id="e7ee3-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="e7ee3-118">Aplikacja serwera Blazor jest publikowana w folderze */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="e7ee3-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="e7ee3-119">Zasoby w folderze są wdrażane na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="e7ee3-120">Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="e7ee3-121">Ścieżka podstawowa aplikacji</span><span class="sxs-lookup"><span data-stu-id="e7ee3-121">App base path</span></span>

<span data-ttu-id="e7ee3-122">*Ścieżka podstawowa aplikacji* jest ścieżką głównego adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="e7ee3-123">Weź pod uwagę następujące aplikacje główne i Blazor:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="e7ee3-124">Główna aplikacja jest wywoływana `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="e7ee3-125">Aplikacja znajduje się fizycznie w lokalizacji *\\d: MojaApl*.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="e7ee3-126">Żądania są odbierane `https://www.contoso.com/{MYAPP RESOURCE}`o.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="e7ee3-127">Aplikacja Blazor o nazwie `CoolApp` jest podaplikacją z: `MyApp`</span><span class="sxs-lookup"><span data-stu-id="e7ee3-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="e7ee3-128">Aplikacja podrzędna fizycznie znajduje się w lokalizacji *d:\\MojaApl\\CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="e7ee3-129">Żądania są odbierane `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`o.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="e7ee3-130">Bez określania dodatkowej konfiguracji `CoolApp`programu aplikacja podrzędna w tym scenariuszu nie ma informacji o tym, gdzie znajduje się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="e7ee3-131">Na przykład aplikacja nie może utworzyć prawidłowych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się w względnej ścieżce `/CoolApp/`adresu URL.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="e7ee3-132">`https://www.contoso.com/CoolApp/`Aby zapewnić konfigurację ścieżki podstawowej aplikacji Blazor `<base>` , `href` atrybut tagu jest ustawiany na ścieżkę względną root w pliku *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="e7ee3-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="e7ee3-133">Dostarczając względną ścieżkę adresu URL, składnik, który nie znajduje się w katalogu głównym, może konstruować adresy URL względem ścieżki katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="e7ee3-134">Składniki na różnych poziomach struktury katalogów mogą tworzyć linki do innych zasobów w lokalizacji w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="e7ee3-135">Ścieżka podstawowa aplikacji jest również używana do przechwytywania hiperłącze, gdzie `href` element docelowy linku znajduje się w przestrzeni&mdash;URI ścieżki bazowej aplikacji. router Blazor obsługuje nawigację wewnętrzną.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="e7ee3-136">W wielu scenariuszach hostingu względna ścieżka URL do aplikacji jest katalogiem głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="e7ee3-137">W takich przypadkach Ścieżka bazowa względnego adresu URL aplikacji jest ukośnikiem (`<base href="/" />`), który jest domyślną konfiguracją dla aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="e7ee3-138">W innych scenariuszach hostingu, takich jak strony GitHub i aplikacje podrzędne IIS, ścieżka podstawowa aplikacji musi być ustawiona na ścieżkę URL względną dla serwera do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="e7ee3-139">Aby ustawić ścieżkę bazową aplikacji, zaktualizuj `<base>` tag `<head>` wewnątrz elementów tagów pliku *wwwroot/index.html* .</span><span class="sxs-lookup"><span data-stu-id="e7ee3-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="e7ee3-140">Ustaw wartość `/{RELATIVE URL PATH}/`atrybutuna (wymagany jest końcowy ukośnik), gdzie `{RELATIVE URL PATH}` jest pełną względną ścieżkę adresu URL aplikacji. `href`</span><span class="sxs-lookup"><span data-stu-id="e7ee3-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="e7ee3-141">W przypadku aplikacji z ścieżką względną adresu URL niegłówną (na przykład `<base href="/CoolApp/">`) aplikacja nie będzie mogła znaleźć zasobów w *przypadku uruchamiania lokalnego*.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="e7ee3-142">Aby rozwiązać ten problem podczas lokalnego tworzenia i testowania, można podać podstawowy argument *ścieżki* , który jest zgodny `href` z wartością `<base>` tagu w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="e7ee3-143">Aby przekazać argument podstawowy ścieżki podczas lokalnego uruchamiania aplikacji, należy wykonać `dotnet run` polecenie z katalogu aplikacji `--pathbase` z opcją:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="e7ee3-144">W przypadku aplikacji ze względną ścieżką `/CoolApp/` URL (`<base href="/CoolApp/">`) polecenie to:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="e7ee3-145">Aplikacja reaguje lokalnie o `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="e7ee3-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="e7ee3-146">wdrażania</span><span class="sxs-lookup"><span data-stu-id="e7ee3-146">Deployment</span></span>

<span data-ttu-id="e7ee3-147">Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="e7ee3-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
