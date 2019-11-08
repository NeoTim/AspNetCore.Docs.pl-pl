---
title: ASP.NET Core hosta w kolektywie serwerów sieci Web
author: guardrex
description: Dowiedz się, jak hostować wiele wystąpień aplikacji ASP.NET Core z zasobami udostępnionymi w środowisku kolektywu serwerów sieci Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/web-farm
ms.openlocfilehash: 16ec2162be8199857d0f2d0ff989ec4cdc6c3277
ms.sourcegitcommit: 68d804d60e104c81fe77a87a9af70b5df2726f60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73830705"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>ASP.NET Core hosta w kolektywie serwerów sieci Web

[Luke Latham](https://github.com/guardrex) i [Krzysztof Ross](https://github.com/Tratcher)

*Farma sieci Web* jest grupą zawierającą co najmniej dwa serwery sieci Web (lub *węzły*), które obsługują wiele wystąpień aplikacji. Gdy żądania użytkowników docierają do kolektywu serwerów sieci Web, *moduł równoważenia obciążenia* dystrybuuje żądania do węzłów kolektywu serwerów sieci Web. Usprawnienia farmy sieci Web:

* **Niezawodność/dostępność** &ndash; w przypadku awarii co najmniej jednego węzła moduł równoważenia obciążenia może kierować żądania do innych działających węzłów, aby kontynuować przetwarzanie żądań.
* **Pojemność/wydajność** &ndash; wiele węzłów może przetwarzać więcej żądań niż pojedynczy serwer. Moduł równoważenia obciążenia równoważy obciążenie przez dystrybuowanie żądań do węzłów.
* **Skalowalność** &ndash;, gdy wymagana jest większa lub mniejsza pojemność, liczbę aktywnych węzłów można zwiększyć lub zmniejszyć w celu dopasowania do obciążenia. Technologie platformy farmy sieci Web, takie jak [Azure App Service](https://azure.microsoft.com/services/app-service/), mogą automatycznie dodawać lub usuwać węzły na żądanie administratora systemu lub automatycznie bez udziału człowieka.
* **Łatwość utrzymania** &ndash; węzły kolektywu serwerów sieci Web mogą polegać na zestawie usług udostępnionych, co ułatwia zarządzanie systemem. Na przykład węzły kolektywu serwerów sieci Web mogą polegać na jednym serwerze bazy danych i wspólnej lokalizacji sieciowej dla zasobów statycznych, takich jak obrazy i pliki do pobrania.

W tym temacie opisano konfigurację i zależności dla aplikacji ASP.NET Core hostowanych w kolektywie serwerów sieci Web, które są zależne od udostępnionych zasobów.

## <a name="general-configuration"></a>Konfiguracja ogólna

<xref:host-and-deploy/index>  
Dowiedz się, jak konfigurować środowiska hostingu i wdrażać ASP.NET Core aplikacje. Skonfiguruj Menedżera procesów na każdym węźle kolektywu serwerów sieci Web, aby zautomatyzować uruchamianie i ponowne uruchamianie aplikacji. Każdy węzeł wymaga środowiska uruchomieniowego ASP.NET Core. Aby uzyskać więcej informacji, zobacz tematy w obszarze [host i wdrażanie](xref:host-and-deploy/index) w dokumentacji.

<xref:host-and-deploy/proxy-load-balancer>  
Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy obliczeniowej w chmurze firmy Microsoft](https://azure.microsoft.com/) do hostowania aplikacji sieci web, w tym ASP.NET Core. App Service to w pełni zarządzana platforma, która zapewnia automatyczne skalowanie, równoważenie obciążenia, stosowanie poprawek i ciągłe wdrażanie.

## <a name="app-data"></a>Dane aplikacji

Gdy aplikacja jest skalowana w wielu wystąpieniach, może być stan aplikacji, który wymaga udostępniania między węzłami. Jeśli stan jest przejściowy, rozważ udostępnienie [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Jeśli współużytkowany stan wymaga trwałości, rozważ zapisanie stanu udostępnionego w bazie danych.

## <a name="required-configuration"></a>Wymagana konfiguracja

Ochrona danych i buforowanie wymagają konfiguracji aplikacji wdrożonych w kolektywie serwerów sieci Web.

### <a name="data-protection"></a>Ochrona danych

[System ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez aplikacje do ochrony danych. Ochrona danych opiera się na zestawie kluczy kryptograficznych przechowywanych w *pęku kluczy*. Gdy system ochrony danych jest zainicjowany, stosuje [domyślne ustawienia](xref:security/data-protection/configuration/default-settings) , które przechowują pierścień kluczy lokalnie. W ramach konfiguracji domyślnej unikatowy pierścień kluczy jest przechowywany w każdym węźle kolektywu serwerów sieci Web. W związku z tym każdy węzeł kolektywu serwerów sieci Web nie może odszyfrować danych szyfrowanych przez aplikację w żadnym innym węźle. Konfiguracja domyślna nie jest zazwyczaj odpowiednia do hostowania aplikacji w kolektywie serwerów sieci Web. Alternatywą dla implementacji pierścienia klucza współdzielonego jest zawsze kierowanie żądań użytkowników do tego samego węzła. Aby uzyskać więcej informacji na temat konfiguracji systemu ochrony danych dla wdrożeń farmy sieci Web, zobacz <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Buforowanie

W środowisku kolektywu serwerów sieci Web mechanizm buforowania musi współdzielić elementy w pamięci podręcznej w węzłach kolektywu serwerów sieci Web. Buforowanie musi być zależne od typowej pamięci podręcznej Redis, udostępnionej bazy danych SQL Server lub niestandardowej implementacji buforowania, która udostępnia elementy w pamięci podręcznej w ramach kolektywu serwerów sieci Web. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Składniki zależne

Poniższe scenariusze nie wymagają dodatkowej konfiguracji, ale zależą od technologii, które wymagają konfiguracji farmy serwerów sieci Web.

| Scenariusz | Zależy od &hellip; |
| -------- | ------------------- |
| Uwierzytelnianie | Ochrona danych (zobacz <xref:security/data-protection/configuration/overview>).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/cookie> i <xref:security/cookie-sharing>. |
| Tożsamość | Konfiguracja uwierzytelniania i bazy danych.<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>. |
| Sesja | Ochrona danych (zaszyfrowane pliki cookie) (zobacz <xref:security/data-protection/configuration/overview>) i buforowanie (zobacz <xref:performance/caching/distributed>).<br><br>Aby uzyskać więcej informacji, zobacz informacje o [stanie sesji i aplikacji: stan sesji](xref:fundamentals/app-state#session-state). |
| TempData | Ochrona danych (zaszyfrowane pliki cookie) (zobacz <xref:security/data-protection/configuration/overview>) lub sesja (zobacz [stan sesji i aplikacji: stan sesji](xref:fundamentals/app-state#session-state)).<br><br>Aby uzyskać więcej informacji, zobacz [sesja i stan aplikacji: TempData](xref:fundamentals/app-state#tempdata). |
| Ochrona przed fałszowaniem | Ochrona danych (zobacz <xref:security/data-protection/configuration/overview>).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="data-protection-and-caching"></a>Ochrona i buforowanie danych

Gdy ochrona danych lub buforowanie nie jest skonfigurowane dla środowiska kolektywu serwerów sieci Web, podczas przetwarzania żądań występują sporadyczne błędy. Dzieje się tak, ponieważ węzły nie współdzielą tych samych zasobów i żądania użytkownika nie zawsze są kierowane do tego samego węzła.

Rozważ użytkownikowi, który zaloguje się do aplikacji przy użyciu uwierzytelniania plików cookie. Użytkownik loguje się do aplikacji w jednym węźle kolektywu serwerów sieci Web. Jeśli następne żądanie zostanie odebrane w tym samym węźle, na którym się zalogowano, aplikacja będzie mogła odszyfrować plik cookie uwierzytelniania i zezwalać na dostęp do zasobu aplikacji. Jeśli kolejne żądanie dociera do innego węzła, aplikacja nie może odszyfrować pliku cookie uwierzytelniania z węzła, w którym zalogowany jest użytkownik, a autoryzacja dla żądanego zasobu kończy się niepowodzeniem.

Gdy którykolwiek z następujących objawów występuje **sporadycznie**, problem zwykle jest śledzony do nieprawidłowej ochrony danych lub konfiguracji buforowania dla środowiska farmy sieci Web:

* Przerwy uwierzytelniania &ndash; plik cookie uwierzytelniania jest niepoprawnie skonfigurowany lub nie można go odszyfrować. Logowanie OAuth (Facebook, Microsoft, Twitter) lub OpenIdConnect kończy się niepowodzeniem z błędem "korelacja nie powiodła się".
* Przerwania autoryzacji &ndash; tożsamość zostanie utracona.
* Stan sesji utraci dane.
* Wyznikane elementy w pamięci podręcznej.
* TempData kończy się niepowodzeniem.
* Wpisy nie powiodą się, &ndash; sprawdzenie ochrony przed fałszerstwem nie powiedzie się.

Aby uzyskać więcej informacji na temat konfiguracji ochrony danych dla wdrożeń farmy sieci Web, zobacz <xref:security/data-protection/configuration/overview>. Aby uzyskać więcej informacji o konfigurowaniu pamięci podręcznej dla wdrożeń farmy sieci Web, zobacz <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacje kolektywu serwerów sieci Web mogą odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala. Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Rozszerzenie niestandardowego skryptu dla systemu Windows](/azure/virtual-machines/extensions/custom-script-windows) &ndash; pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure, co jest przydatne w przypadku konfiguracji po wdrożeniu i instalacji oprogramowania.
