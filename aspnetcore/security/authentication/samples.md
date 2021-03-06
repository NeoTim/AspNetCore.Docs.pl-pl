---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630747"
---
# <a name="authentication-samples-for-aspnet-core"></a>Przykłady uwierzytelniania dla ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :

* [Przekształcanie oświadczeń](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie ponowne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Niestandardowy dostawca zasad — IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Dynamiczne schematy i opcje uwierzytelniania](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Ogranicza dostęp do plików statycznych](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Uruchamianie przykładów

* Wybierz [gałąź](https://github.com/dotnet/AspNetCore). Na przykład `release/3.1`
* Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.
* Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :

* [Przekształcanie oświadczeń](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookie ponowne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [Niestandardowy dostawca zasad — IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [Dynamiczne schematy i opcje uwierzytelniania](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [Ogranicza dostęp do plików statycznych](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Uruchamianie przykładów

* Wybierz [gałąź](https://github.com/dotnet/AspNetCore). Na przykład `release/2.1`
* Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.
* Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .

::: moniker-end
