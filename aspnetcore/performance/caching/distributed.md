---
title: Rozproszonej pamięci podręcznej w programie ASP.NET Core
author: guardrex
description: Dowiedz się, jak poprawić wydajność aplikacji i skalowalności, szczególnie w środowisku farmy, chmurze i na serwerze za pomocą platformy ASP.NET Core rozproszonej pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/caching/distributed
ms.openlocfilehash: a157eb075874d2118e3e34b51410b539a1ec37df
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248591"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="b8cbc-103">Rozproszonej pamięci podręcznej w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8cbc-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="b8cbc-104">Przez [Steve Smith](https://ardalis.com/) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8cbc-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8cbc-105">Rozproszonej pamięci podręcznej jest pamięć podręczna współużytkowane przez wiele serwerów aplikacji, zazwyczaj przechowywane jako zewnętrznej usługi na serwerach aplikacji, mających do niej dostęp.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b8cbc-106">Rozproszonej pamięci podręcznej może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie w przypadku, gdy aplikacja jest hostowana w usłudze usługi w chmurze lub w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b8cbc-107">Rozproszonej pamięci podręcznej ma kilka zalet w stosunku do innych scenariuszy buforowania, gdzie buforowane dane są przechowywane na serwerach poszczególnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b8cbc-108">Gdy dane w pamięci podręcznej jest rozpowszechniana, dane:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b8cbc-109">Jest *spójnego* (spójność) w ramach żądania na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b8cbc-110">Przeżyje serwer zostanie ponownie uruchomiony i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b8cbc-111">Nie korzysta z lokalnej pamięci.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-111">Doesn't use local memory.</span></span>

<span data-ttu-id="b8cbc-112">Konfiguracja rozproszonej pamięci podręcznej jest zależne od implementacji.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b8cbc-113">W tym artykule opisano sposób konfigurowania programu SQL Server i rozproszonej pamięci podręczne redis Cache.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b8cbc-114">Implementacje firm również są dostępne, takich jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b8cbc-115">Niezależnie od tego, która implementacja jest zaznaczone, aplikacja korzysta z pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b8cbc-116">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([sposobu pobierania](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8cbc-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b8cbc-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b8cbc-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b8cbc-118">Używanie programu SQL Server rozproszonej pamięci podręcznej, odwołanie [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b8cbc-119">Do użycia pamięci podręcznej Redis rozproszonej pamięci podręcznej, odwołanie [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) i Dodaj odwołanie do pakietu [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="b8cbc-120">Pakiet Redis nie jest zawarty w `Microsoft.AspNetCore.App` pakietu, więc użytkownik musi odwoływać się pakietu Redis oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b8cbc-121">Używanie programu SQL Server rozproszonej pamięci podręcznej, odwołanie [pakiet meta Microsoft.aspnetcore.all](xref:fundamentals/metapackage) lub Dodaj odwołanie do pakietu [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b8cbc-122">Do użycia pamięci podręcznej Redis rozproszonej pamięci podręcznej, odwołanie [pakiet meta Microsoft.aspnetcore.all](xref:fundamentals/metapackage) lub Dodaj odwołanie do pakietu [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="b8cbc-123">Pakiet Redis znajduje się w `Microsoft.AspNetCore.All` pakietu, więc nie ma potrzeby odwołują się do pakietu Redis oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-123">The Redis package is included in `Microsoft.AspNetCore.All` package, so you don't need to reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b8cbc-124">Używanie programu SQL Server rozproszonej pamięci podręcznej, należy dodać odwołanie do pakietu [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-124">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b8cbc-125">Aby używanie pamięci podręcznej Redis rozproszonej pamięci podręcznej, należy dodać odwołania do pakietu do [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pakietu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-125">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="b8cbc-126">Interfejs IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="b8cbc-126">IDistributedCache interface</span></span>

<span data-ttu-id="b8cbc-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Interfejsu udostępnia następujące metody do manipulowania elementów w implementacja rozproszonej pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b8cbc-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akceptuje klucza typu ciąg i pobiera element pamięci podręcznej jako `byte[]` tablicy, jeśli znaleziono w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b8cbc-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Dodaje element (jako `byte[]` tablicy) do pamięci podręcznej przy użyciu klucza typu ciąg.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b8cbc-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Odświeża element w pamięci podręcznej na podstawie jego klucza zresetowanie jej przewijania limit czasu wygaśnięcia (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b8cbc-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Usuwa element pamięci podręcznej na podstawie jego ciągu klucza.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b8cbc-132">Ustanowić rozproszonego usługi pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b8cbc-132">Establish distributed caching services</span></span>

<span data-ttu-id="b8cbc-133">Zarejestruj implementację <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8cbc-134">Implementacje dostarczone przez Framework opisane w tym temacie obejmują:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b8cbc-135">Rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b8cbc-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b8cbc-136">Rozproszonej pamięci podręcznej programu SQL Server</span><span class="sxs-lookup"><span data-stu-id="b8cbc-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b8cbc-137">Rozproszonej pamięci podręcznej redis Cache</span><span class="sxs-lookup"><span data-stu-id="b8cbc-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b8cbc-138">Rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b8cbc-138">Distributed Memory Cache</span></span>

<span data-ttu-id="b8cbc-139">Rozproszonej pamięci podręcznej (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) to implementacja dostarczone przez framework <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , elementy są przechowywane w pamięci.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b8cbc-140">Rozproszonej pamięci podręcznej nie jest rzeczywisty rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b8cbc-141">Elementy pamięci podręcznej są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b8cbc-142">Rozproszonej pamięci podręcznej jest przydatne w implementacji:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b8cbc-143">W zakresie projektowania i testowania scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="b8cbc-144">Gdy jeden serwer jest używany w produkcji i pamięci, użycie nie będzie to problemem.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b8cbc-145">Implementowanie streszczenia rozproszonej pamięci podręcznej pamięci podręcznej magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b8cbc-146">Umożliwia ona wdrażanie true rozproszonych rozwiązanie pamięci podręcznej w przyszłości w przypadku wielu węzłów lub odporności na uszkodzenia stają się niezbędne.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b8cbc-147">Przykładowa aplikacja korzysta z rozproszonej pamięci podręcznej, gdy aplikacja jest uruchamiana w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b8cbc-148">Rozproszone programu SQL Server w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b8cbc-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b8cbc-149">Implementacja rozproszonej pamięci podręcznej serwera SQL (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umożliwia rozproszonej pamięci podręcznej do użycia bazę danych programu SQL Server jako jego magazyn zapasowy.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b8cbc-150">Aby utworzyć tabelę element pamięci podręcznej programu SQL Server w wystąpieniu programu SQL Server, można użyć `sql-cache` narzędzia.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b8cbc-151">Narzędzie tworzy tabelę o nazwie i schemat, który określisz.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-151">The tool creates a table with the name and schema that you specify.</span></span>

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="b8cbc-152">Dodaj `SqlConfig.Tools` do `<ItemGroup>` elementu w pliku projektu i uruchom `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-152">Add `SqlConfig.Tools` to the `<ItemGroup>` element of the project file and run `dotnet restore`.</span></span>

```xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.Extensions.Caching.SqlConfig.Tools"
                          Version="2.0.2" />
</ItemGroup>
```

::: moniker-end

<span data-ttu-id="b8cbc-153">Utwórz tabelę w programie SQL Server, uruchamiając `sql-cache create` polecenia.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-153">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b8cbc-154">Podaj wystąpienie programu SQL Server (`Data Source`), bazy danych (`Initial Catalog`), schematu (na przykład `dbo`) i nazwę tabeli (na przykład `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="b8cbc-154">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b8cbc-155">Aby wskazać, że narzędzie zakończyła się pomyślnie, zostanie zarejestrowany komunikat:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-155">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b8cbc-156">Tabelę utworzoną przez `sql-cache` narzędzie posiada zgodny z następującym schematem:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-156">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer Cache Table](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b8cbc-158">Aplikacja powinna manipulowania wartości pamięci podręcznej przy użyciu wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, a nie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-158">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b8cbc-159">Implementuje aplikacji przykładowej <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> w środowisku deweloperskim niż:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-159">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=9-15)]

> [!NOTE]
> <span data-ttu-id="b8cbc-160">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (i, opcjonalnie, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) są zazwyczaj przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera klucz tajny](xref:security/app-secrets) lub *appsettings.json* / *appsettings. {Środowiska} .json* plików).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-160">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{Environment}.json* files).</span></span> <span data-ttu-id="b8cbc-161">Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane poza systemów kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-161">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b8cbc-162">Rozproszonej pamięci podręcznej Redis Cache</span><span class="sxs-lookup"><span data-stu-id="b8cbc-162">Distributed Redis Cache</span></span>

<span data-ttu-id="b8cbc-163">[Redis](https://redis.io/) to magazyn danych w pamięci "open source", która jest często używana jako rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-163">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="b8cbc-164">Magazynu Redis można używać lokalnie i można skonfigurować [usługi Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji hostowanych na platformie Azure ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-164">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span> <span data-ttu-id="b8cbc-165">Konfiguruje aplikację, za pomocą implementacji pamięci podręcznej <xref:Microsoft.Extensions.Caching.Redis.RedisCache> wystąpienia (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="b8cbc-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="b8cbc-166">Aby zainstalować usługi Redis na maszynie lokalnej:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-166">To install Redis on your local machine:</span></span>

* <span data-ttu-id="b8cbc-167">Zainstaluj [pakietu Chocolatey Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-167">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="b8cbc-168">Uruchom `redis-server` z poziomu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-168">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b8cbc-169">Przy użyciu rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b8cbc-169">Use the distributed cache</span></span>

<span data-ttu-id="b8cbc-170">Do użycia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, żądań wystąpienie <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z dowolnym konstruktora w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-170">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b8cbc-171">Wystąpienie jest dostarczany przez [wstrzykiwanie zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-171">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b8cbc-172">Po uruchomieniu aplikacji, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> są wstrzykiwane do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-172">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b8cbc-173">Bieżący czas jest buforowana przy użyciu <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [hosta sieci Web: Interfejs IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="b8cbc-173">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b8cbc-174">Przykładowa aplikacja wprowadza <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` do użytku przez stronę indeksu.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-174">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b8cbc-175">Każdym załadowaniu strony indeksu pamięci podręcznej są sprawdzane pod kątem pamięci podręcznej czas w `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-175">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b8cbc-176">Jeśli nie upłynął czas pamięci podręcznej, jest wyświetlany czas.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-176">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b8cbc-177">Po upływie 20 sekund od czasu ostatniego czasu pamięci podręcznej uzyskano dostęp (ostatni czas, ta strona została załadowana), zostanie wyświetlona strona *upłynął limit czasu pamięci podręcznej*.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-177">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b8cbc-178">Natychmiast zaktualizować pamięci podręcznej czas bieżący czas, wybierając **zresetować czasu pamięci podręcznej** przycisku.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-178">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b8cbc-179">Wyzwalacze przycisk `OnPostResetCachedTime` metodę procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-179">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="b8cbc-180">Nie ma potrzeby używania pojedynczego wystąpienia lub polu Okres istnienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienia (co najmniej wbudowanych implementacji).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-180">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b8cbc-181">Można również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienia wszędzie tam, gdzie może być konieczne zamiast DI, ale Tworzenie wystąpienia usługi w kodzie może być trudniejsze do testowania kodu i narusza [jawne zależności zasady](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="b8cbc-181">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b8cbc-182">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="b8cbc-182">Recommendations</span></span>

<span data-ttu-id="b8cbc-183">Podczas podejmowania decyzji o życie <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> sprawdza się najlepiej w swojej aplikacji, należy wziąć pod uwagę następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b8cbc-183">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b8cbc-184">Istniejącą infrastrukturę</span><span class="sxs-lookup"><span data-stu-id="b8cbc-184">Existing infrastructure</span></span>
* <span data-ttu-id="b8cbc-185">Wymagania dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="b8cbc-185">Performance requirements</span></span>
* <span data-ttu-id="b8cbc-186">Koszt</span><span class="sxs-lookup"><span data-stu-id="b8cbc-186">Cost</span></span>
* <span data-ttu-id="b8cbc-187">Doświadczenia zespołu użytkownika</span><span class="sxs-lookup"><span data-stu-id="b8cbc-187">Team experience</span></span>

<span data-ttu-id="b8cbc-188">Rozwiązań buforowania na ogół magazynu w pamięci w celu zapewnienia szybkiego pobierania danych z pamięci podręcznej pamięci jest jednak ograniczona zasobów i kosztowna rozwinąć.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-188">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b8cbc-189">Tylko magazynu często używane dane w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-189">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b8cbc-190">Ogólnie rzecz biorąc usługi Redis cache zapewnia wyższą przepływność i mniejsze opóźnienia niż pamięć podręczna programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-190">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b8cbc-191">Jednak testów porównawczych jest zazwyczaj wymagane, aby określić charakterystyki wydajności strategii buforowania.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-191">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b8cbc-192">Gdy program SQL Server jest używana jako magazyn zapasowy rozproszonej pamięci podręcznej, użycie tej samej bazy danych dla pamięci podręcznej i przechowywanie danych zwykłych aplikacji i pobierania może niekorzystnie wpłynąć na wydajność zarówno.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-192">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b8cbc-193">Zalecamy używanie dedykowanego wystąpienia programu SQL Server dla rozproszonej pamięci podręcznej, magazyn zapasowy.</span><span class="sxs-lookup"><span data-stu-id="b8cbc-193">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8cbc-194">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b8cbc-194">Additional resources</span></span>

* [<span data-ttu-id="b8cbc-195">Usługa redis Cache na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b8cbc-195">Redis Cache on Azure</span></span>](https://azure.microsoft.com/documentation/services/redis-cache/)
* [<span data-ttu-id="b8cbc-196">Bazy danych SQL na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b8cbc-196">SQL Database on Azure</span></span>](https://azure.microsoft.com/documentation/services/sql-database/)
* <span data-ttu-id="b8cbc-197">[ASP.NET Core IDistributedCache dostarczyciela NCache w formularzach sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="b8cbc-197">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
