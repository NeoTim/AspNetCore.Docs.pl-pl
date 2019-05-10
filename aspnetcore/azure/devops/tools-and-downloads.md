---
title: Narzędzia i pliki do pobrania — metodyki DevOps z platformą ASP.NET Core i platformy Azure
author: CamSoper
description: Narzędzia i pliki do pobrania wymagane dla metodyki DevOps z platformą ASP.NET Core i platformy Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ae4827f735274405021e5ee539d1029b7ddb9553
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898615"
---
# <a name="tools-and-downloads"></a>Narzędzia i pliki do pobrania

Platforma Azure oferuje kilka interfejsów do inicjowania obsługi administracyjnej zasobów i zarządzania, takich jak [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](/cli/azure/), [programu Azure PowerShell](/powershell/azure/overview), [w chmurze platformy Azure Shell](https://shell.azure.com/bash)i Visual Studio. Ten przewodnik minimalistyczny podejście i korzysta z usługi Azure Cloud Shell zawsze, gdy jest to możliwe zmniejszenie kroki wymagane. Jednak należy użyć witryny Azure portal dla niektórych części.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest spełnienie następujących subskrypcji:

* Azure &mdash; Jeśli nie masz konta, [bezpłatna wersja próbna](https://azure.microsoft.com/free/).
* Usługi Azure DevOps &mdash; subskrypcji DevOps platformy Azure i organizacji jest tworzony w rozdziale 4.
* GitHub &mdash; Jeśli nie masz konta, [Utwórz bezpłatne konto](https://github.com/join).

Wymagane są następujące narzędzia:

* [Git](https://git-scm.com/downloads) &mdash; powinieneś rozumieć podstawy systemu Git jest zalecane w przypadku tego przewodnika. Przegląd [dokumentacja usługi Git](https://git-scm.com/doc), konkretnie [git zdalnego](https://git-scm.com/docs/git-remote) i [wypchnięcia narzędzia git](https://git-scm.com/docs/git-push).
* [Zestaw .NET core SDK](https://www.microsoft.com/net/download/) &mdash; wersji 2.1.300 lub nowszy jest wymagany do kompilowania i uruchamiania przykładowej aplikacji. Jeśli zainstalowano program Visual Studio za pomocą **programowanie dla wielu platform .NET Core** obciążenia .NET Core SDK jest już zainstalowana.

    Zweryfikuj instalację zestawu .NET Core SDK. Otwórz powłokę wiersza polecenia, a następnie uruchom następujące polecenie:

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Zalecanych narzędzi (tylko Windows)

* [Program Visual Studio](https://visualstudio.microsoft.com)użytkownika niezawodne narzędzia platformy Azure zapewnia graficzny interfejs użytkownika dla większości funkcji opisanych w tym przewodniku. W każdej wersji programu Visual Studio będzie działać, łącznie z bezpłatnego programu Visual Studio Community Edition. Samouczki są zapisywane do zademonstrowania programowania, wdrażania i metodyki DevOps z usługą i bez programu Visual Studio.

  Upewnij się, że program Visual Studio zawiera następujące [obciążeń](/visualstudio/install/modify-visual-studio) zainstalowane:

  * ASP.NET i tworzenie aplikacji internetowych
  * Programowanie na platformie Azure
  * Programowanie dla wielu platform .NET core
