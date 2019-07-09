---
title: Zasady programów w programie ASP.NET Core
author: rick-anderson
description: Schematy zasady uwierzytelniania ułatwiają ma jeden logiczne schemat uwierzytelniania
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: 1a2d92e6fa54189b8154fc501b31c8a99d1f9081
ms.sourcegitcommit: 357a7120632b20465801c093e4e5bd4a315496a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649180"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="2b473-103">Zasady programów w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b473-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="2b473-104">Schematy zasady uwierzytelniania ułatwiają ma jeden logiczne schemat uwierzytelniania za pomocą wielu metod.</span><span class="sxs-lookup"><span data-stu-id="2b473-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="2b473-105">Na przykład schemat zasad może na użytek uwierzytelniania Google wyzwania i uwierzytelniania plików cookie całą resztą.</span><span class="sxs-lookup"><span data-stu-id="2b473-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="2b473-106">Uwierzytelnianie zasad schematom:</span><span class="sxs-lookup"><span data-stu-id="2b473-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="2b473-107">Można łatwo przekazywać wszelkie działania uwierzytelniania do innego schematu.</span><span class="sxs-lookup"><span data-stu-id="2b473-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="2b473-108">Do przodu dynamicznie na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="2b473-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="2b473-109">Wszystkie schematy uwierzytelniania, które pochodne użyj <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> oraz skojarzonych z nimi [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="2b473-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="2b473-110">Są wykonywane automatycznie schematy zasad w programie ASP.NET Core 2.1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="2b473-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="2b473-111">Można ją włączyć za pośrednictwem Konfigurowanie opcje schematu.</span><span class="sxs-lookup"><span data-stu-id="2b473-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="2b473-112">Przykłady</span><span class="sxs-lookup"><span data-stu-id="2b473-112">Examples</span></span>

<span data-ttu-id="2b473-113">Poniższy przykład pokazuje wyższe schematu poziomu, który łączy niższym poziomie schematów.</span><span class="sxs-lookup"><span data-stu-id="2b473-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="2b473-114">Uwierzytelnianie serwisu Google jest używany dla wyzwań i uwierzytelniania plików cookie jest używany dla wszystkich innych:</span><span class="sxs-lookup"><span data-stu-id="2b473-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="2b473-115">Poniższy przykład umożliwia dynamiczne Wybieranie schematów na podstawie danego żądania.</span><span class="sxs-lookup"><span data-stu-id="2b473-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="2b473-116">Oznacza to jak łączyć pliki cookie i uwierzytelniania interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="2b473-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
