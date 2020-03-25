---
title: Skonfiguruj konsolidator dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania aplikacji Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218664"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="ba3c7-103">Skonfiguruj konsolidator dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ba3c7-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ba3c7-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ba3c7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ba3c7-105">Blazor webassembly wykonuje konsolidację [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) podczas kompilacji, aby przyciąć niepotrzebny kod IL z zestawów wyjściowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="ba3c7-106">Konsolidator jest wyłączony podczas kompilowania w konfiguracji debugowania.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="ba3c7-107">Aplikacje muszą kompilować w konfiguracji wydania, aby umożliwić konsolidator.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="ba3c7-108">Zalecamy Kompilowanie w wersji podczas wdrażania aplikacji webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="ba3c7-109">Łączenie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="ba3c7-110">Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ konsolidator nie wie o tym zachowaniu dynamicznym i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="ba3c7-111">Aby przyciąć takie aplikacje, konsolidator musi być informowany o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="ba3c7-112">Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby przetestować kompilacje wydań aplikacji często podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="ba3c7-113">Łączenie aplikacji Blazor można skonfigurować za pomocą tych funkcji MSBuild:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="ba3c7-114">Skonfiguruj konsolidację globalnie za pomocą [Właściwości programu MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="ba3c7-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="ba3c7-115">Kontrolowanie łączenia poszczególnych zestawów z [plikiem konfiguracyjnym](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="ba3c7-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="ba3c7-116">Sterowanie łączeniem z właściwością programu MSBuild</span><span class="sxs-lookup"><span data-stu-id="ba3c7-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="ba3c7-117">Łączenie jest włączone, gdy aplikacja jest wbudowana w `Release` konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="ba3c7-118">Aby to zmienić, skonfiguruj Właściwość `BlazorWebAssemblyEnableLinking` MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ba3c7-119">Kontrola łączenia z plikiem konfiguracji</span><span class="sxs-lookup"><span data-stu-id="ba3c7-119">Control linking with a configuration file</span></span>

<span data-ttu-id="ba3c7-120">Kontroluj łączenie dla poszczególnych zestawów, dostarczając plik konfiguracyjny XML i określając plik jako element programu MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="ba3c7-121">*LinkerConfig. XML*:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="ba3c7-122">Aby uzyskać więcej informacji, zobacz [łącza do plików XML](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="ba3c7-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="ba3c7-123">Dodawanie pliku konfiguracji konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="ba3c7-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="ba3c7-124">Aby skonfigurować konsolidator dla określonej biblioteki, Dodaj plik konfiguracji konsolidatora XML do biblioteki jako zasób osadzony.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="ba3c7-125">Osadzony zasób musi mieć taką samą nazwę jak zestaw.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="ba3c7-126">W poniższym przykładzie plik *LinkerConfig. XML* jest określony jako zasób osadzony, który ma taką samą nazwę jak zestaw biblioteki:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="ba3c7-127">Konfigurowanie konsolidatora dla celów wielojęzycznych</span><span class="sxs-lookup"><span data-stu-id="ba3c7-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="ba3c7-128">Domyślnie konfiguracja konsolidatora Blazor dla aplikacji Blazor webassembly umożliwia rozłączenie informacji o danych wielojęzycznych z wyjątkiem lokalizacji lokalnych jawnie żądanych.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ba3c7-129">Usunięcie tych zestawów minimalizuje rozmiar aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba3c7-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="ba3c7-130">Aby kontrolować, które zestawy I18N są zachowywane, ustaw właściwość `<MonoLinkerI18NAssemblies>` MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba3c7-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="ba3c7-131">Wartość regionu</span><span class="sxs-lookup"><span data-stu-id="ba3c7-131">Region Value</span></span>     | <span data-ttu-id="ba3c7-132">Zestaw regionów mono</span><span class="sxs-lookup"><span data-stu-id="ba3c7-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="ba3c7-133">Uwzględnione wszystkie zestawy</span><span class="sxs-lookup"><span data-stu-id="ba3c7-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="ba3c7-134">*I18N. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="ba3c7-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="ba3c7-135">*I18N. Środkowy wschód. dll*</span><span class="sxs-lookup"><span data-stu-id="ba3c7-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="ba3c7-136">`none` (wartość domyślna)</span><span class="sxs-lookup"><span data-stu-id="ba3c7-136">`none` (default)</span></span> | <span data-ttu-id="ba3c7-137">None</span><span class="sxs-lookup"><span data-stu-id="ba3c7-137">None</span></span>                    |
| `other`          | <span data-ttu-id="ba3c7-138">*I18N. Inne. dll*</span><span class="sxs-lookup"><span data-stu-id="ba3c7-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="ba3c7-139">*I18N. Rzadki. dll*</span><span class="sxs-lookup"><span data-stu-id="ba3c7-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="ba3c7-140">*I18N. Zachodni. dll*</span><span class="sxs-lookup"><span data-stu-id="ba3c7-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="ba3c7-141">Użyj przecinka, aby rozdzielić wiele wartości (na przykład `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="ba3c7-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="ba3c7-142">Aby uzyskać więcej informacji, zobacz [i18n: Pnetlib — Biblioteka struktury wielojęzycznej (repozytorium usługi GitHub/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="ba3c7-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
