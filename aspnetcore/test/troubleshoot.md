---
title: Rozwiązywanie problemów i debugowanie ASP.NET Core projektów
author: Rick-Anderson
description: Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 042e1c84a7226cb4bf56bcc0ff4e576b67e68e1b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655303"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="d5322-103">Rozwiązywanie problemów i debugowanie ASP.NET Core projektów</span><span class="sxs-lookup"><span data-stu-id="d5322-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="d5322-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5322-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d5322-105">Poniższe linki udostępniają wskazówki dotyczące rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="d5322-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="d5322-106">Konferencja NDC (Londyn, 2018): diagnozowanie problemów w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5322-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="d5322-107">Blog ASP.NET: Rozwiązywanie problemów z wydajnością ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5322-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="d5322-108">Ostrzeżenia zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="d5322-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="d5322-109">Są zainstalowane zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="d5322-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="d5322-110">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="d5322-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d5322-111">Zainstalowane są zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="d5322-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="d5322-112">Wyświetlane są tylko szablony z wersji 64-bitowych zainstalowanych w języku "C:\\Program Files\\dotnet\\SDK\\".</span><span class="sxs-lookup"><span data-stu-id="d5322-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d5322-113">To ostrzeżenie jest wyświetlane, gdy są zainstalowane zarówno wersje 32-bitowe (x86), jak i 64-bitowe (x64) [zestaw .NET Core SDK](https://www.microsoft.com/net/download/all) .</span><span class="sxs-lookup"><span data-stu-id="d5322-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="d5322-114">Typowe przyczyny instalacji obu wersji obejmują:</span><span class="sxs-lookup"><span data-stu-id="d5322-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="d5322-115">Instalator zestaw .NET Core SDK został pierwotnie pobrany przy użyciu maszyny 32-bitowej, ale następnie został skopiowany na komputer z systemem 64-bitowym.</span><span class="sxs-lookup"><span data-stu-id="d5322-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="d5322-116">32-bitowy zestaw .NET Core SDK został zainstalowany przez inną aplikację.</span><span class="sxs-lookup"><span data-stu-id="d5322-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="d5322-117">Pobrano i zainstalowano nieprawidłową wersję.</span><span class="sxs-lookup"><span data-stu-id="d5322-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="d5322-118">Odinstaluj 32-bitowy zestaw .NET Core SDK, aby uniknąć tego ostrzeżenia.</span><span class="sxs-lookup"><span data-stu-id="d5322-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="d5322-119">Odinstaluj z **Panelu sterowania** > **programy i funkcje** > **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="d5322-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d5322-120">Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="d5322-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="d5322-121">Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach</span><span class="sxs-lookup"><span data-stu-id="d5322-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="d5322-122">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="d5322-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d5322-123">Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach.</span><span class="sxs-lookup"><span data-stu-id="d5322-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="d5322-124">Wyświetlane są tylko szablony z zestawów SDK zainstalowanych w języku "C:\\Program Files\\dotnet\\SDK\\".</span><span class="sxs-lookup"><span data-stu-id="d5322-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d5322-125">Ten komunikat jest wyświetlany, gdy istnieje co najmniej jedna instalacja zestaw .NET Core SDK w katalogu poza językiem *C:\\pliki programu\\dotnet\\SDK\\* .</span><span class="sxs-lookup"><span data-stu-id="d5322-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="d5322-126">Zwykle zdarza się to, gdy zestaw .NET Core SDK został wdrożony na komputerze przy użyciu funkcji kopiowania/wklejania zamiast Instalatora MSI.</span><span class="sxs-lookup"><span data-stu-id="d5322-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="d5322-127">Odinstaluj wszystkie 32-bitowe zestawy SDK platformy .NET Core i środowiska uruchomieniowe, aby uniknąć tego ostrzeżenia.</span><span class="sxs-lookup"><span data-stu-id="d5322-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="d5322-128">Odinstaluj z **Panelu sterowania** > **programy i funkcje** > **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="d5322-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d5322-129">Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="d5322-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="d5322-130">Nie wykryto żadnych zestawów .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="d5322-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="d5322-131">W oknie dialogowym **Nowy projekt** programu Visual Studio dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="d5322-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="d5322-132">Nie wykryto żadnych zestawów .NET Core SDK, upewnij się, że są one uwzględnione w zmiennej środowiskowej `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d5322-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="d5322-133">Gdy wykonujesz polecenie `dotnet`, ostrzeżenie jest wyświetlane jako:</span><span class="sxs-lookup"><span data-stu-id="d5322-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="d5322-134">Nie można znaleźć żadnych zainstalowanych zestawów SDK dotnet.</span><span class="sxs-lookup"><span data-stu-id="d5322-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="d5322-135">Te ostrzeżenia są wyświetlane, gdy zmienna środowiskowa `PATH` nie wskazuje żadnych zestawów SDK platformy .NET Core na komputerze.</span><span class="sxs-lookup"><span data-stu-id="d5322-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="d5322-136">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="d5322-136">To resolve this problem:</span></span>

* <span data-ttu-id="d5322-137">Zainstaluj zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="d5322-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="d5322-138">Uzyskaj najnowszy Instalator z [programu .NET downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d5322-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="d5322-139">Sprawdź, czy zmienna środowiskowa `PATH` wskazuje lokalizację, w której zainstalowano zestaw SDK (`C:\Program Files\dotnet\` dla 64-bitowego/x64 lub `C:\Program Files (x86)\dotnet\` dla 32-bitowych/x86).</span><span class="sxs-lookup"><span data-stu-id="d5322-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="d5322-140">Instalator zestawu SDK zwykle ustawia `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d5322-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="d5322-141">Zawsze Instaluj te same zestawy SDK i środowiska uruchomieniowe na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="d5322-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="d5322-142">Brak zestawu SDK po zainstalowaniu pakietu hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d5322-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d5322-143">Podczas instalowania [pakietu .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modyfikuje `PATH` podczas instalowania środowiska uruchomieniowego platformy .NET Core w celu wskazywania wersji 32-bitowej (x86) platformy .net core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="d5322-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="d5322-144">Może to spowodować brak zestawów SDK, gdy zostanie użyte polecenie programu .NET Core `dotnet` 32-bitowego (x86) ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="d5322-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d5322-145">Aby rozwiązać ten problem, Przenieś `C:\Program Files\dotnet\` do pozycji przed `C:\Program Files (x86)\dotnet\` na `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d5322-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="d5322-146">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="d5322-146">Obtain data from an app</span></span>

<span data-ttu-id="d5322-147">Jeśli aplikacja może odpowiadać na żądania, można uzyskać następujące dane z aplikacji za pomocą oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d5322-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="d5322-148">Żądanie &ndash; Metoda, schemat, host, pathbase, ścieżka, ciąg zapytania, nagłówki</span><span class="sxs-lookup"><span data-stu-id="d5322-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="d5322-149">Połączenie &ndash; zdalnego adresu IP, portu zdalnego, lokalnego adresu IP, portu lokalnego, certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="d5322-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="d5322-150">Nazwa &ndash; tożsamości, nazwa wyświetlana</span><span class="sxs-lookup"><span data-stu-id="d5322-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="d5322-151">Ustawienia konfiguracji</span><span class="sxs-lookup"><span data-stu-id="d5322-151">Configuration settings</span></span>
* <span data-ttu-id="d5322-152">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="d5322-152">Environment variables</span></span>

<span data-ttu-id="d5322-153">Umieść poniższy kod [oprogramowania pośredniczącego](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na początku potoku przetwarzania żądania metody `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d5322-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="d5322-154">Środowisko jest sprawdzane przed uruchomieniem oprogramowania pośredniczącego, aby upewnić się, że kod jest wykonywany tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="d5322-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="d5322-155">Aby uzyskać środowisko, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d5322-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="d5322-156">Wsuń `IHostingEnvironment` do metody `Startup.Configure` i Sprawdź środowisko przy użyciu zmiennej lokalnej.</span><span class="sxs-lookup"><span data-stu-id="d5322-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="d5322-157">Poniższy przykładowy kod demonstruje takie podejście.</span><span class="sxs-lookup"><span data-stu-id="d5322-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="d5322-158">Przypisz środowisko do właściwości w klasie `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d5322-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="d5322-159">Sprawdź środowisko przy użyciu właściwości (na przykład `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="d5322-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="d5322-160">Debuguj ASP.NET Core aplikacje</span><span class="sxs-lookup"><span data-stu-id="d5322-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="d5322-161">Poniższe linki zawierają informacje dotyczące debugowania ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d5322-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="d5322-162">Debugowanie środowiska ASP Core w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="d5322-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="d5322-163">Debugowanie programu .NET Core w systemie UNIX za pośrednictwem protokołu SSH</span><span class="sxs-lookup"><span data-stu-id="d5322-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="d5322-164">Szybki Start: debugowanie ASP.NET z debugerem programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5322-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="d5322-165">Aby uzyskać więcej informacji o debugowaniu, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/2960) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="d5322-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
