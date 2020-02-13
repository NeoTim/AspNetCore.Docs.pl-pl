---
title: Dostawcy magazynu kluczy w programie ASP.NET Core
author: rick-anderson
description: Więcej informacji o dostawcy magazynu kluczy na platformie ASP.NET Core i jak skonfigurować lokalizacje magazynu kluczy.
ms.author: riande
ms.date: 12/05/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 219ebc471de32d15e4a43c938eef156c52e5f11e
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172591"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="4dcf0-103">Dostawcy magazynu kluczy w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dcf0-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="4dcf0-104">System ochrony danych [domyślnie stosuje mechanizm odnajdywania](xref:security/data-protection/configuration/default-settings) , aby określić, gdzie mają być utrwalane klucze kryptograficzne.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="4dcf0-105">Deweloper można zastąpić domyślny mechanizm odnajdywania i ręcznie określ lokalizację.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="4dcf0-106">Jeśli określisz lokalizacji jawne trwałość klucza, system ochrony danych deregisters domyślne szyfrowanie kluczy podczas mechanizmu rest, więc klucze nie są szyfrowane w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="4dcf0-107">Zalecane jest, aby dodatkowo [określić jawny mechanizm szyfrowania klucza](xref:security/data-protection/implementation/key-encryption-at-rest) dla wdrożeń produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="4dcf0-108">System plików</span><span class="sxs-lookup"><span data-stu-id="4dcf0-108">File system</span></span>

<span data-ttu-id="4dcf0-109">Aby skonfigurować repozytorium kluczy oparte na systemie plików, wywołaj procedurę konfiguracji [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="4dcf0-110">Podaj [DirectoryInfo](/dotnet/api/system.io.directoryinfo) wskazujący repozytorium, w którym powinny być przechowywane klucze:</span><span class="sxs-lookup"><span data-stu-id="4dcf0-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="4dcf0-111">`DirectoryInfo` może wskazywać na katalog na komputerze lokalnym lub może wskazywać na folder w udziale sieciowym.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="4dcf0-112">Jeśli wskazujesz katalog na komputerze lokalnym (a scenariuszem jest to, że tylko aplikacje na komputerze lokalnym wymagają dostępu do tego repozytorium), rozważ użycie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) (w systemie Windows), aby zaszyfrować klucze w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="4dcf0-113">W przeciwnym razie Rozważ użycie [certyfikatu X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) , aby zaszyfrować klucze w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="4dcf0-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="4dcf0-114">Azure Storage</span></span>

<span data-ttu-id="4dcf0-115">Pakiet [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umożliwia przechowywanie kluczy ochrony danych w usłudze Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="4dcf0-116">Klucze mogą być współużytkowane przez wiele wystąpień aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="4dcf0-117">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub CSRF ochronę na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="4dcf0-118">Aby skonfigurować dostawcę usługi Azure Blob Storage, wywołaj jedno z przeciążeń [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="4dcf0-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="4dcf0-119">Jeśli aplikacja sieci Web działa jako usługa platformy Azure, tokeny uwierzytelniania mogą być tworzone automatycznie przy użyciu [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="4dcf0-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

<span data-ttu-id="4dcf0-120">Zobacz [więcej szczegółów na temat konfigurowania uwierzytelniania](/azure/key-vault/service-to-service-authentication) między usługami.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="4dcf0-121">Redis</span><span class="sxs-lookup"><span data-stu-id="4dcf0-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4dcf0-122">Pakiet [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="4dcf0-123">Klucze mogą być współużytkowane przez wiele wystąpień aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="4dcf0-124">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub CSRF ochronę na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4dcf0-125">Pakiet [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="4dcf0-126">Klucze mogą być współużytkowane przez wiele wystąpień aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="4dcf0-127">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub CSRF ochronę na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4dcf0-128">Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="4dcf0-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4dcf0-129">Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="4dcf0-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="4dcf0-130">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="4dcf0-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="4dcf0-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="4dcf0-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="4dcf0-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="4dcf0-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="4dcf0-133">Przykłady ASP.NET Core ochrony</span><span class="sxs-lookup"><span data-stu-id="4dcf0-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="4dcf0-134">Rejestr</span><span class="sxs-lookup"><span data-stu-id="4dcf0-134">Registry</span></span>

<span data-ttu-id="4dcf0-135">**Dotyczy tylko wdrożeń systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="4dcf0-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="4dcf0-136">Czasami aplikacja ma uprawnienia do zapisu w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="4dcf0-137">Rozważmy scenariusz, w którym aplikacja działa jako konto usługi wirtualnej (na przykład tożsamość puli aplikacji *w3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="4dcf0-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="4dcf0-138">W takich przypadkach administrator może aprowizować klucz rejestru, który jest dostępny za pomocą tożsamości konta usługi.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="4dcf0-139">Wywołaj metodę rozszerzenia [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="4dcf0-140">Podaj [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) wskazujący lokalizację, w której mają być przechowywane klucze kryptograficzne:</span><span class="sxs-lookup"><span data-stu-id="4dcf0-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="4dcf0-141">Zalecamy używanie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) do szyfrowania kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="4dcf0-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4dcf0-142">Entity Framework Core</span></span>

<span data-ttu-id="4dcf0-143">Pakiet [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) udostępnia mechanizm przechowywania kluczy ochrony danych w bazie danych przy użyciu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="4dcf0-144">Pakiet NuGet `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` należy dodać do pliku projektu, nie jest on częścią [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4dcf0-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4dcf0-145">Z tego pakietu kluczy mogą być współużytkowane przez wiele wystąpień aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="4dcf0-146">Aby skonfigurować dostawcę EF Core, wywołaj metodę [PersistKeysToDbContext\<TContext >](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="4dcf0-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="4dcf0-147">Parametr generyczny, `TContext`, musi dziedziczyć z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i implementować [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="4dcf0-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="4dcf0-148">Utwórz tabelę `DataProtectionKeys`.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dcf0-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dcf0-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dcf0-150">W oknie **konsola Menedżera pakietów** (PMC) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="4dcf0-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4dcf0-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4dcf0-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4dcf0-152">Wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="4dcf0-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="4dcf0-153">`MyKeysContext` to `DbContext` zdefiniowane w poprzednim przykładzie kodu.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="4dcf0-154">Jeśli używasz `DbContext` z inną nazwą, podstaw `DbContext` nazwę `MyKeysContext`.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="4dcf0-155">Klasa `DataProtectionKeys`/jednostka przyjmuje strukturę pokazaną w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="4dcf0-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="4dcf0-156">Właściwość/pole</span><span class="sxs-lookup"><span data-stu-id="4dcf0-156">Property/Field</span></span> | <span data-ttu-id="4dcf0-157">Typ CLR</span><span class="sxs-lookup"><span data-stu-id="4dcf0-157">CLR Type</span></span> | <span data-ttu-id="4dcf0-158">Typ SQL</span><span class="sxs-lookup"><span data-stu-id="4dcf0-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="4dcf0-159">`int`, PK, not null</span><span class="sxs-lookup"><span data-stu-id="4dcf0-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="4dcf0-160">`nvarchar(MAX)`, wartość null</span><span class="sxs-lookup"><span data-stu-id="4dcf0-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="4dcf0-161">`nvarchar(MAX)`, wartość null</span><span class="sxs-lookup"><span data-stu-id="4dcf0-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="4dcf0-162">Niestandardowe repozytorium klucza</span><span class="sxs-lookup"><span data-stu-id="4dcf0-162">Custom key repository</span></span>

<span data-ttu-id="4dcf0-163">Jeśli mechanizmy wbudowane nie są odpowiednie, deweloper może określić własny klucz trwałości, dostarczając niestandardowy [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="4dcf0-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
