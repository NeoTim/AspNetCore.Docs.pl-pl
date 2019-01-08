---
title: Migracja z Microsoft.Extensions.Logging 2.1 lub 2.2 lub 3.0
author: pakrym
description: Dowiedz się, jak przeprowadzić migrację aplikacji bez platformy ASP.NET Core, która używa Microsoft.Extensions.Logging z 2.1 2.2 lub 3.0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099537"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="271a0-103">Migracja z Microsoft.Extensions.Logging 2.1 lub 2.2 lub 3.0</span><span class="sxs-lookup"><span data-stu-id="271a0-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="271a0-104">W tym artykule przedstawiono typowe kroki dotyczące migrowania aplikacji bez platformy ASP.NET Core, która używa `Microsoft.Extensions.Logging` z 2.1 2.2 lub 3.0.</span><span class="sxs-lookup"><span data-stu-id="271a0-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="271a0-105">2.1 lub 2.2</span><span class="sxs-lookup"><span data-stu-id="271a0-105">2.1 to 2.2</span></span>

<span data-ttu-id="271a0-106">Ręczne tworzenie `ServiceCollection` i wywołać `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="271a0-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="271a0-107">przykład 2.1:</span><span class="sxs-lookup"><span data-stu-id="271a0-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="271a0-108">przykład 2,2:</span><span class="sxs-lookup"><span data-stu-id="271a0-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="271a0-109">2.1 i 3.0</span><span class="sxs-lookup"><span data-stu-id="271a0-109">2.1 to 3.0</span></span>

<span data-ttu-id="271a0-110">3.0, należy użyć `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="271a0-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="271a0-111">przykład 2.1:</span><span class="sxs-lookup"><span data-stu-id="271a0-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="271a0-112">przykład 3.0:</span><span class="sxs-lookup"><span data-stu-id="271a0-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="271a0-113">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="271a0-113">Additional resources</span></span>

<xref:fundamentals/logging/index>