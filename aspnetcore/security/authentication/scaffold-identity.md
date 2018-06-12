---
title: Tożsamość szkieletu w projektów platformy ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć szkielet tożsamości w projekcie platformy ASP.NET Core.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 5/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/scaffold-identity
ms.openlocfilehash: e7a2cf3633ed48a0d2030739cdc092441fcae2ff
ms.sourcegitcommit: 63fb07fb3f71b32daf2c9466e132f2e7cc617163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2018
ms.locfileid: "35252038"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="782f9-103">Tożsamość szkieletu w projektów platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="782f9-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="782f9-104">przez [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="782f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="782f9-105">Udostępnia platformy ASP.NET Core 2.1 i nowsze [ASP.NET Core Identity](xref:security/authentication/identity) jako [biblioteki klas Razor](xref:mvc/razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="782f9-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:mvc/razor-pages/ui-class).</span></span> <span data-ttu-id="782f9-106">Aplikacje, które obejmują tożsamości można zastosować tworzenia szkieletu selektywnie dodać kod źródłowy zawartych w bibliotece klasy Razor tożsamości (RCL).</span><span class="sxs-lookup"><span data-stu-id="782f9-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="782f9-107">Można wygenerować kodu źródłowego, aby można było zmodyfikować kod i zmiany zachowania.</span><span class="sxs-lookup"><span data-stu-id="782f9-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="782f9-108">Na przykład można nakazać tworzenia szkieletu, aby wygenerować kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="782f9-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="782f9-109">Wygenerowany kod mają pierwszeństwo przed ten sam kod w RCL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="782f9-109">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="782f9-110">Aplikacje, które wykonują **nie** obejmują uwierzytelniania można zastosować tworzenia szkieletu, aby dodać pakiet RCL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="782f9-110">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="782f9-111">Istnieje możliwość wybrania tożsamości kod zostanie wygenerowany.</span><span class="sxs-lookup"><span data-stu-id="782f9-111">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="782f9-112">Chociaż tworzenia szkieletu generuje większość niezbędne kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="782f9-112">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="782f9-113">W tym dokumencie opisano kroki niezbędne do ukończenia aktualizacji szkieletów tożsamości.</span><span class="sxs-lookup"><span data-stu-id="782f9-113">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="782f9-114">Uruchomienie tworzenia szkieletu tożsamości *ScaffoldingReadme.txt* plik jest tworzony w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="782f9-114">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="782f9-115">*ScaffoldingReadme.txt* plik zawiera ogólne wskazówki, co jest potrzebne do ukończenia aktualizacji szkieletów tożsamości.</span><span class="sxs-lookup"><span data-stu-id="782f9-115">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="782f9-116">Ten dokument zawiera bardziej szczegółowe instrukcje niż *ScaffoldingReadme.txt* pliku.</span><span class="sxs-lookup"><span data-stu-id="782f9-116">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="782f9-117">Zalecamy użycie systemu kontroli źródła, przedstawiono różnice pliku, który umożliwia tworzenie kopii poza zmiany.</span><span class="sxs-lookup"><span data-stu-id="782f9-117">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="782f9-118">Sprawdź, czy zmiany po zakończeniu tworzenia szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="782f9-118">Inspect the changes after running the Identity scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="782f9-119">Szkieletu tożsamości do pustego projektu</span><span class="sxs-lookup"><span data-stu-id="782f9-119">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="782f9-120">Dodaj następujący wyróżniony wywołania metody `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="782f9-120">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[Main](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="782f9-121">Tożsamość szkieletu do projektu Razor bez autoryzacji istniejących</span><span class="sxs-lookup"><span data-stu-id="782f9-121">Scaffold identity into a Razor project without existing authorization</span></span>

<!--
set projNam=RPnoAuth
set projType=razor
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="782f9-122">Tożsamość jest skonfigurowana w *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="782f9-122">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="782f9-123">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="782f9-123">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="782f9-124">Migracje, UseAuthentication i układu</span><span class="sxs-lookup"><span data-stu-id="782f9-124">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="782f9-125">W `Configure` metody `Startup` klasy, należy wywołać [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="782f9-125">In the `Configure` method of the `Startup` class, call [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[Main](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="782f9-126">Zmian układu</span><span class="sxs-lookup"><span data-stu-id="782f9-126">Layout changes</span></span>

<span data-ttu-id="782f9-127">Opcjonalnie: Dodaj częściowego logowania (`_LoginPartial`) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="782f9-127">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="782f9-128">Tożsamość szkieletu do projektu Razor z autoryzacji</span><span class="sxs-lookup"><span data-stu-id="782f9-128">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register

[!INCLUDE[](~/includes/webapp-alias-notice.md)]
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="782f9-129">Niektóre opcje tożsamości są konfigurowane w *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="782f9-129">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="782f9-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="782f9-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="782f9-131">Tożsamość szkieletu do projektu programu MVC bez autoryzacji istniejących</span><span class="sxs-lookup"><span data-stu-id="782f9-131">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="782f9-132">Opcjonalnie: Dodaj częściowego logowania (`_LoginPartial`) do *Views/Shared/_Layout.cshtml* pliku:</span><span class="sxs-lookup"><span data-stu-id="782f9-132">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="782f9-133">Przenieś *Pages/Shared/_LoginPartial.cshtml* pliku *Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="782f9-133">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="782f9-134">Tożsamość jest skonfigurowana w *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="782f9-134">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="782f9-135">Aby uzyskać więcej informacji zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="782f9-135">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="782f9-136">Wywołanie [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="782f9-136">Call [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[Main](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="782f9-137">Tożsamość szkieletu do projektu programu MVC z autoryzacji</span><span class="sxs-lookup"><span data-stu-id="782f9-137">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="782f9-138">Usuń *stron/Shared* folderów i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="782f9-138">Delete the *Pages/Shared* folder and the files in that folder.</span></span>