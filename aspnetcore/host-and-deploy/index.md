---
title: Hostowanie i wdrażanie ASP.NET Core
author: guardrex
description: Dowiedz się, jak konfigurować środowiska hostingu i wdrażać ASP.NET Core aplikacje.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/index
ms.openlocfilehash: 8c7c131ca328f3118c45e822d6d5c86f0d44001f
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803269"
---
# <a name="host-and-deploy-aspnet-core"></a>Hostowanie i wdrażanie ASP.NET Core

Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:

* Wdróż opublikowaną aplikację w folderze na serwerze hostingu.
* Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.
* W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.

## <a name="publish-to-a-folder"></a>Publikowanie w folderze

[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* . Podczas wdrażania z programu Visual Studio krok `dotnet publish` następuje automatycznie przed skopiowaniem plików do lokalizacji docelowej wdrożenia.

### <a name="folder-contents"></a>Zawartość folderu

Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.

Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*. Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* . Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze. Domyślny model wdrażania jest zależny od platformy. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC. Aby uzyskać więcej informacji, zobacz temat <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Konfigurowanie Menedżera procesów

Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera. Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów. Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Usługa systemu Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Konfigurowanie zwrotnego serwera proxy

Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy. Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.

&mdash;konfiguracji z serwerem zwrotnego serwera proxy lub bez niego&mdash;jest obsługiwaną konfiguracją hostingu. Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Serwer proxy i scenariuszy usługi równoważenia obciążenia

Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia. Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie platformy ASP.NET Core pracować z serwerów proxy i moduły równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń

Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer. Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* . Program Visual Studio używa programu MSBuild do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i [Używanie programu MSBuild i Team Foundation Build](http://msbuildbook.com/) Book.

Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service. Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp). Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Zobacz <xref:tutorials/publish-to-azure-webapp-using-vs>, aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio. Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikowanie z MSDeploy w systemie Windows

Zobacz <xref:host-and-deploy/visual-studio-publish-profiles>, aby uzyskać instrukcje dotyczące publikowania aplikacji z profilem publikacji programu Visual Studio, w tym z poziomu wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *Web. config* zapoznaj się z artykułami w obszarze <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hosting w farmie internetowej

Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm>.

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a>Przeprowadzanie kontroli kondycji

Używaj oprogramowania do sprawdzania kondycji, aby przeprowadzać kontrole kondycji aplikacji i jej zależności. Aby uzyskać więcej informacji, zobacz temat <xref:host-and-deploy/health-checks>.

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
* [Hosting ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

