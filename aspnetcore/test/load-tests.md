---
title: ASP.NET Core testowanie obciążenia/obciążeniowego
author: Jeremy-Meng
description: Poznaj kilka istotnych narzędzi i metod testowania obciążeniowego i testowania obciążeniowego ASP.NET Core aplikacji.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289024"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="fc8ee-103">ASP.NET Core testowanie obciążenia/obciążeniowego</span><span class="sxs-lookup"><span data-stu-id="fc8ee-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="fc8ee-104">Testowanie obciążeniowe i testowanie obciążeniowe są ważne, aby zapewnić, że aplikacja sieci Web jest wydajna i skalowalna.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="fc8ee-105">Ich cele są różne, chociaż często korzystają z podobnych testów.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="fc8ee-106">**Testy obciążenia** &ndash; sprawdzić, czy aplikacja może obsłużyć określone obciążenie użytkownikami w pewnym scenariuszu, zachowując jednocześnie cel odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="fc8ee-107">Aplikacja jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="fc8ee-108">**Testy obciążeniowe** &ndash;ą stabilność aplikacji testowej w przypadku uruchamiania w skrajnych warunkach, często przez długi czas.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="fc8ee-109">Testy zapewniają duże obciążenie użytkownikami, przerastają lub stopniowo zwiększają obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="fc8ee-110">Testy obciążeniowe określają, czy aplikacja poddawana obciążeniom może odzyskać sprawność po awarii i bezpiecznie wrócić do oczekiwanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="fc8ee-111">W obszarze naprężenie aplikacja nie jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="fc8ee-112">Program Visual Studio 2019 to Ostatnia wersja programu Visual Studio z funkcjami testów obciążenia.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="fc8ee-113">W przypadku klientów wymagających narzędzi testowania obciążenia w przyszłości zalecamy używanie alternatywnych narzędzi, takich jak Apache JMeter, Akamai CloudTest i BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="fc8ee-114">Aby uzyskać więcej informacji, zobacz [Informacje o wersji programu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="fc8ee-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="fc8ee-115">Narzędzia programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc8ee-115">Visual Studio tools</span></span>

<span data-ttu-id="fc8ee-116">Program Visual Studio umożliwia użytkownikom tworzenie, opracowywanie i debugowanie testów wydajności i obciążenia sieci Web.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="fc8ee-117">Dostępna jest opcja tworzenia testów przez rejestrowanie akcji w przeglądarce internetowej.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="fc8ee-118">Aby uzyskać informacje na temat sposobu tworzenia, konfigurowania i uruchamiania projektów testów obciążenia przy użyciu programu Visual Studio 2017, zobacz [Szybki Start: Tworzenie projektu testu obciążenia](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="fc8ee-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="fc8ee-119">Testy obciążenia można skonfigurować do uruchamiania lokalnego lub uruchamiania w chmurze przy użyciu usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="fc8ee-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="fc8ee-120">Narzędzia innych firm</span><span class="sxs-lookup"><span data-stu-id="fc8ee-120">Third-party tools</span></span>

<span data-ttu-id="fc8ee-121">Poniższa lista zawiera narzędzia do oceny wydajności sieci Web innych firm z różnymi zestawami funkcji:</span><span class="sxs-lookup"><span data-stu-id="fc8ee-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="fc8ee-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="fc8ee-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="fc8ee-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="fc8ee-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="fc8ee-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="fc8ee-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="fc8ee-125">Chleb</span><span class="sxs-lookup"><span data-stu-id="fc8ee-125">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="fc8ee-126">Moje przepięcio zachodnie wiatru</span><span class="sxs-lookup"><span data-stu-id="fc8ee-126">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="fc8ee-127">W ramach</span><span class="sxs-lookup"><span data-stu-id="fc8ee-127">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="fc8ee-128">Vegeta</span><span class="sxs-lookup"><span data-stu-id="fc8ee-128">Vegeta</span></span>](https://github.com/tsenart/vegeta)
