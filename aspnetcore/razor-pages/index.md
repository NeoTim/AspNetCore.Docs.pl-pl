---
title: Wprowadzenie do stron Razor programu ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak stron Razor w programie ASP.NET Core umożliwia kodowania scenariuszy skoncentrowane na stronie łatwiejsze i bardziej wydajne niż przy użyciu platformy MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 7df57153efc58b6a19ce663eb31d173da11b1005
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898633"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="eb008-103">Wprowadzenie do stron Razor programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb008-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="eb008-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="eb008-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="eb008-105">Strony razor jest nowy aspekt ASP.NET Core MVC, która sprawia, że kodowania skoncentrowane na stronie scenariuszy łatwiejsze i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="eb008-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="eb008-106">Jeśli szukasz samouczka, który korzysta z metody Model-View-Controller [Rozpoczynanie pracy z platformą ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="eb008-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="eb008-107">Ten dokument zawiera wprowadzenie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="eb008-108">Nie jest samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="eb008-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="eb008-109">Jeśli znajdziesz niektóre sekcje zbyt zaawansowany, zobacz [Rozpoczynanie pracy ze stronami Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="eb008-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="eb008-110">Aby uzyskać omówienie platformy ASP.NET Core, zobacz [wprowadzenie do platformy ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="eb008-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb008-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="eb008-111">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="eb008-112">Utwórz projekt stron Razor</span><span class="sxs-lookup"><span data-stu-id="eb008-112">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb008-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb008-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb008-114">Zobacz [Rozpoczynanie pracy ze stronami Razor](xref:tutorials/razor-pages/razor-pages-start) szczegółowe instrukcje dotyczące sposobu tworzenia projektu stron Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-114">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eb008-115">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="eb008-115">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb008-116">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="eb008-116">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb008-117">Uruchom `dotnet new razor` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="eb008-117">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="eb008-118">Otwórz wygenerowany *.csproj* pliku z programu Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="eb008-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eb008-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb008-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb008-120">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="eb008-120">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb008-121">Uruchom `dotnet new razor` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="eb008-121">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="eb008-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="eb008-122">Razor Pages</span></span>

<span data-ttu-id="eb008-123">Strony razor jest włączone w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="eb008-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="eb008-124">Należy wziąć pod uwagę strona podstawowa: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="eb008-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="eb008-125">Powyższy kod wygląda o wiele [plik widoku Razor](xref:tutorials/first-mvc-app/adding-view) używane w aplikacji ASP.NET Core za pomocą kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="eb008-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="eb008-126">Czym różni się on jest `@page` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="eb008-126">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="eb008-127">`@page` sprawia, że plik do Akcja kontrolera MVC — oznacza to, obsługi żądań bezpośrednio, bez przechodzenia przez kontrolera.</span><span class="sxs-lookup"><span data-stu-id="eb008-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="eb008-128">`@page` musi być pierwszym dyrektywy Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="eb008-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="eb008-129">`@page` wpływa na działanie innych konstrukcji Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-129">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="eb008-130">Podobny strony, `PageModel` klasy, jest wyświetlany w następujące dwa pliki.</span><span class="sxs-lookup"><span data-stu-id="eb008-130">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="eb008-131">*Pages/Index2.cshtml* pliku:</span><span class="sxs-lookup"><span data-stu-id="eb008-131">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="eb008-132">*Pages/Index2.cshtml.cs* modelu strony:</span><span class="sxs-lookup"><span data-stu-id="eb008-132">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="eb008-133">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak plik strony Razor za pomocą *.cs* dołączane.</span><span class="sxs-lookup"><span data-stu-id="eb008-133">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="eb008-134">Na przykład jest poprzedniej strony Razor *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb008-134">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="eb008-135">Plik zawierający `PageModel` nosi nazwę klasy *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb008-135">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="eb008-136">Skojarzenia ścieżki adresu URL do strony są określane według lokalizacji strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="eb008-136">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="eb008-137">W poniższej tabeli przedstawiono ścieżki strony Razor i dopasowywania adresu URL:</span><span class="sxs-lookup"><span data-stu-id="eb008-137">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="eb008-138">Nazwa pliku i ścieżka</span><span class="sxs-lookup"><span data-stu-id="eb008-138">File name and path</span></span>               | <span data-ttu-id="eb008-139">dopasowanie adresu URL</span><span class="sxs-lookup"><span data-stu-id="eb008-139">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eb008-140">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-140">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="eb008-141">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="eb008-141">`/` or `/Index`</span></span> |
| <span data-ttu-id="eb008-142">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-142">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="eb008-143">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-143">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="eb008-144">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-144">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="eb008-145">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="eb008-145">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="eb008-146">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="eb008-146">Notes:</span></span>

* <span data-ttu-id="eb008-147">Środowisko uruchomieniowe szuka plików stron Razor w *stron* folderu domyślnie.</span><span class="sxs-lookup"><span data-stu-id="eb008-147">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="eb008-148">`Index` jest domyślną stroną, podczas gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-148">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="eb008-149">Zapis formularza podstawowego</span><span class="sxs-lookup"><span data-stu-id="eb008-149">Write a basic form</span></span>

<span data-ttu-id="eb008-150">Strony razor zaprojektowaną do podejmowania typowe wzorce używane w przeglądarkach, łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb008-150">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="eb008-151">[Wiązanie modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i wszystkich pomocników HTML *po prostu działają* z właściwościami, zdefiniowanej w klasie strony Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-151">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="eb008-152">Należy wziąć pod uwagę strona, która implementuje podstawową "Skontaktuj się z nami" formularza dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="eb008-152">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="eb008-153">Aby wyświetlić przykłady w niniejszym dokumencie `DbContext` jest inicjowana w [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) pliku.</span><span class="sxs-lookup"><span data-stu-id="eb008-153">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="eb008-154">Model danych:</span><span class="sxs-lookup"><span data-stu-id="eb008-154">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="eb008-155">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="eb008-155">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="eb008-156">*Pages/Create.cshtml* Wyświetl plik:</span><span class="sxs-lookup"><span data-stu-id="eb008-156">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="eb008-157">*Pages/Create.cshtml.cs* modelu strony:</span><span class="sxs-lookup"><span data-stu-id="eb008-157">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="eb008-158">Zgodnie z Konwencją `PageModel` nosi nazwę klasy `<PageName>Model` i znajduje się w tej samej przestrzeni nazw, gdy ta strona.</span><span class="sxs-lookup"><span data-stu-id="eb008-158">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="eb008-159">`PageModel` Klasa umożliwia oddzielenie logiki strony z jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="eb008-159">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="eb008-160">Definiuje stronę obsługi żądań wysyłanych do strony i dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-160">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="eb008-161">Ta separacja pozwala na zarządzanie zależnościami strony za pomocą [wstrzykiwanie zależności](xref:fundamentals/dependency-injection) i [testu jednostkowego](xref:test/razor-pages-tests) stron.</span><span class="sxs-lookup"><span data-stu-id="eb008-161">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="eb008-162">Strona ta zawiera `OnPostAsync` *metody obsługi*, która działa w `POST` żądania (po użytkownik wpisy formularza).</span><span class="sxs-lookup"><span data-stu-id="eb008-162">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="eb008-163">Możesz dodać metody obsługi dla dowolnego zlecenie HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb008-163">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="eb008-164">Najbardziej typowe procedury obsługi są:</span><span class="sxs-lookup"><span data-stu-id="eb008-164">The most common handlers are:</span></span>

* <span data-ttu-id="eb008-165">`OnGet` Aby zainicjować stanu potrzebne dla strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-165">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="eb008-166">[OnGet](#OnGet) próbki.</span><span class="sxs-lookup"><span data-stu-id="eb008-166">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="eb008-167">`OnPost` do obsługi przesyłania formularza.</span><span class="sxs-lookup"><span data-stu-id="eb008-167">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="eb008-168">`Async` Sufiks nazwy jest opcjonalne, ale jest często używana przez Konwencję w funkcji asynchronicznej.</span><span class="sxs-lookup"><span data-stu-id="eb008-168">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="eb008-169">`OnPostAsync` Kodu w powyższym przykładzie jest podobny do będzie zwykle napisany w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="eb008-169">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="eb008-170">Powyższy kod jest typowe dla stron Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-170">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="eb008-171">Większość elementów podstawowych MVC, takie jak [wiązanie modelu](xref:mvc/models/model-binding), [weryfikacji](xref:mvc/models/validation), wyniki akcji są współdzielone.</span><span class="sxs-lookup"><span data-stu-id="eb008-171">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="eb008-172">Poprzedni `OnPostAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="eb008-172">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="eb008-173">Podstawowy przepływ `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="eb008-173">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="eb008-174">Sprawdź, czy błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="eb008-174">Check for validation errors.</span></span>

* <span data-ttu-id="eb008-175">Jeśli nie ma żadnych błędów, Zapisz dane i przekierować.</span><span class="sxs-lookup"><span data-stu-id="eb008-175">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="eb008-176">Jeśli występują błędy, wyświetlenie strony ponownie przy użyciu komunikatów dotyczących sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="eb008-176">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="eb008-177">Weryfikacja po stronie klienta jest identyczna z tradycyjnych aplikacji ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="eb008-177">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="eb008-178">W wielu przypadkach błędy sprawdzania poprawności będzie być wykryte na komputerze klienckim i nigdy nie zostały przekazane do serwera.</span><span class="sxs-lookup"><span data-stu-id="eb008-178">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="eb008-179">Po pomyślnym wprowadzeniu danych `OnPostAsync` wywołań metody obsługi `RedirectToPage` metody pomocnika do zwrócenia wystąpienia `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="eb008-179">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="eb008-180">`RedirectToPage` to nowy wynik akcji, podobnie jak `RedirectToAction` lub `RedirectToRoute`, ale dostosowane dla stron.</span><span class="sxs-lookup"><span data-stu-id="eb008-180">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="eb008-181">W poprzednim przykładzie, zostanie przekierowany do strony indeksu głównego (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eb008-181">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="eb008-182">`RedirectToPage` została szczegółowo opisana w [Generowanie adresu URL dla stron](#url_gen) sekcji.</span><span class="sxs-lookup"><span data-stu-id="eb008-182">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="eb008-183">Kiedy przesłanego formularza ma błędy sprawdzania poprawności, (które są przekazywane do serwera),`OnPostAsync` wywołań metody obsługi `Page` metody pomocnika.</span><span class="sxs-lookup"><span data-stu-id="eb008-183">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="eb008-184">`Page` Zwraca wystąpienie `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="eb008-184">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="eb008-185">Zwracanie `Page` jest podobny do sposobu Zwróć akcje w kontrolerach `View`.</span><span class="sxs-lookup"><span data-stu-id="eb008-185">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="eb008-186">`PageResult` jest wartością domyślną</span><span class="sxs-lookup"><span data-stu-id="eb008-186">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="eb008-187">zwracany typ metody programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="eb008-187">return type for a handler method.</span></span> <span data-ttu-id="eb008-188">Metody obsługi, która zwraca `void` renderuje stronę.</span><span class="sxs-lookup"><span data-stu-id="eb008-188">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="eb008-189">`Customer` Używa właściwości `[BindProperty]` atrybutu zgadzaj się na wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="eb008-189">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="eb008-190">Strony razor domyślnie powiązania właściwości tylko zlecenia-GET.</span><span class="sxs-lookup"><span data-stu-id="eb008-190">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="eb008-191">Powiązanie z właściwościami może zmniejszyć ilość kodu, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="eb008-191">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="eb008-192">Powiązanie zmniejsza kodu przy użyciu tej samej właściwości do renderowania pól formularza (`<input asp-for="Customer.Name">`) i akceptuje dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="eb008-192">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="eb008-193">Strona główna (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="eb008-193">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="eb008-194">Skojarzone `PageModel` klasy (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="eb008-194">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="eb008-195">*Index.cshtml* plik zawiera następujące znaczniki, aby utworzyć link edycji dla każdego kontaktu:</span><span class="sxs-lookup"><span data-stu-id="eb008-195">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="eb008-196">[Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) używane `asp-route-{value}` atrybutu, aby wygenerować łącze do strony edytowania.</span><span class="sxs-lookup"><span data-stu-id="eb008-196">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="eb008-197">Ten link zawiera dane trasy z kontaktem identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="eb008-197">The link contains route data with the contact ID.</span></span> <span data-ttu-id="eb008-198">Na przykład `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="eb008-198">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="eb008-199">Użyj `asp-area` atrybutu, aby określić obszar.</span><span class="sxs-lookup"><span data-stu-id="eb008-199">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="eb008-200">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="eb008-200">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="eb008-201">*Pages/Edit.cshtml* pliku:</span><span class="sxs-lookup"><span data-stu-id="eb008-201">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="eb008-202">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="eb008-202">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="eb008-203">Ograniczenia routingu`"{id:int}"` informuje strony aby akceptował żądania do strony, które zawierają `int` przekierowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="eb008-203">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="eb008-204">Jeśli żądanie strony nie zawiera danych trasy, który może zostać przekonwertowany na `int`, środowisko wykonawcze zwraca błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="eb008-204">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="eb008-205">Aby wprowadzić identyfikator jest opcjonalne, należy dołączyć `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="eb008-205">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="eb008-206">*Pages/Edit.cshtml.cs* pliku:</span><span class="sxs-lookup"><span data-stu-id="eb008-206">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="eb008-207">*Index.cshtml* plik zawiera także znaczników, aby utworzyć przycisk Usuń, dla każdego kontaktu klienta:</span><span class="sxs-lookup"><span data-stu-id="eb008-207">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="eb008-208">Gdy przycisk Usuń jest renderowany w języku HTML, jego `formaction` obejmuje parametry:</span><span class="sxs-lookup"><span data-stu-id="eb008-208">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="eb008-209">Określony przez identyfikator kontaktu klienta z `asp-route-id` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="eb008-209">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="eb008-210">`handler` Określony przez `asp-page-handler` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="eb008-210">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="eb008-211">Oto przykład przycisku Usuń renderowany przy użyciu klienta skontaktować się z Identyfikatora `1`:</span><span class="sxs-lookup"><span data-stu-id="eb008-211">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="eb008-212">Po wybraniu przycisku, formularz `POST` żądanie jest wysyłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="eb008-212">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="eb008-213">Zgodnie z Konwencją Nazwa metody obsługi jest zaznaczona, na podstawie wartości z `handler` parametru, zgodnie ze schematem `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="eb008-213">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="eb008-214">Ponieważ `handler` jest `delete` w tym przykładzie `OnPostDeleteAsync` metody obsługi jest używany do procesu `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="eb008-214">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="eb008-215">Jeśli `asp-page-handler` jest ustawiona na inną wartość, takich jak `remove`, metodę programu obsługi strony o nazwie `OnPostRemoveAsync` jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="eb008-215">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="eb008-216">`OnPostDeleteAsync` Metody:</span><span class="sxs-lookup"><span data-stu-id="eb008-216">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="eb008-217">Akceptuje `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="eb008-217">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="eb008-218">Zapytania bazy danych dla kontaktu klienta z `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="eb008-218">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="eb008-219">Jeśli zostanie znaleziony kontakt z klientem, zostali oni usunięci z listy kontaktów klienta.</span><span class="sxs-lookup"><span data-stu-id="eb008-219">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="eb008-220">Baza danych jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="eb008-220">The database is updated.</span></span>
* <span data-ttu-id="eb008-221">Wywołania `RedirectToPage` do przekierowania do strony indeksu głównego (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eb008-221">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="eb008-222">Oznacz właściwości strony zgodnie z potrzebami</span><span class="sxs-lookup"><span data-stu-id="eb008-222">Mark page properties as required</span></span>

<span data-ttu-id="eb008-223">Właściwości `PageModel` być dekorowane za pomocą [wymagane](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atrybutu:</span><span class="sxs-lookup"><span data-stu-id="eb008-223">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="eb008-224">Aby uzyskać więcej informacji, zobacz [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eb008-224">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="eb008-225">Zarządzanie żądaniami HEAD za pomocą obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="eb008-225">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="eb008-226">Żądania HEAD umożliwiają pobieranie nagłówki dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="eb008-226">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="eb008-227">Inaczej niż w przypadku żądania GET HEAD żądań nie zwracają treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="eb008-227">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="eb008-228">Zazwyczaj obsługi HEAD jest tworzony i wywołana dla żądania HEAD:</span><span class="sxs-lookup"><span data-stu-id="eb008-228">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="eb008-229">Jeśli żadna procedura obsługi HEAD (`OnHead`) jest zdefiniowany, stron Razor powraca do wywoływania procedury obsługi strony GET (`OnGet`) w programie ASP.NET Core 2.1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="eb008-229">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="eb008-230">W programie ASP.NET Core 2.1 i 2.2 to zachowanie dotyczy [SetCompatibilityVersion](xref:mvc/compatibility-version) w `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="eb008-230">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="eb008-231">Generowanie szablonów domyślnych `SetCompatibilityVersion` wywołania platformy ASP.NET Core 2.1 i 2.2.</span><span class="sxs-lookup"><span data-stu-id="eb008-231">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="eb008-232">`SetCompatibilityVersion` efektywnie ustawia opcję stron Razor `AllowMappingHeadRequestsToGetHandler` do `true`.</span><span class="sxs-lookup"><span data-stu-id="eb008-232">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="eb008-233">Zamiast włączenie wszystkich 2.1 zachowania `SetCompatibilityVersion`, użytkownik może jawnie wyrazić zgodę na konkretne zachowania.</span><span class="sxs-lookup"><span data-stu-id="eb008-233">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="eb008-234">Poniższy kod zdecyduje się na żądania HEAD mapowanie programu obsługi GET.</span><span class="sxs-lookup"><span data-stu-id="eb008-234">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="eb008-235">XSRF/CSRF i stron Razor</span><span class="sxs-lookup"><span data-stu-id="eb008-235">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="eb008-236">Nie trzeba pisać kodu dla [antiforgery weryfikacji](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="eb008-236">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="eb008-237">Antiforgery generowania tokenu i sprawdzanie poprawności są automatycznie uwzględniane stron Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-237">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="eb008-238">Za pomocą układów, częściowe, szablonów i pomocnicy tagów ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="eb008-238">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="eb008-239">Strony pracować ze wszystkimi funkcjami aparatu widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-239">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="eb008-240">Układy, częściowe, szablony, pomocników tagów *_ViewStart.cshtml*, *_ViewImports.cshtml* działają w taki sam sposób jak w przypadku konwencjonalnych widokami Razor.</span><span class="sxs-lookup"><span data-stu-id="eb008-240">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="eb008-241">Teraz declutter tę stronę, wykorzystując niektóre z tych możliwości.</span><span class="sxs-lookup"><span data-stu-id="eb008-241">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb008-242">Dodaj [stronę układu](xref:mvc/views/layout) do *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb008-242">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb008-243">Dodaj [stronę układu](xref:mvc/views/layout) do *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb008-243">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="eb008-244">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="eb008-244">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="eb008-245">Kontroluje układ każdej strony (chyba że strona oznacza brak zgody na układ).</span><span class="sxs-lookup"><span data-stu-id="eb008-245">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="eb008-246">Importuje HTML struktur, takich jak JavaScript i arkusze stylów.</span><span class="sxs-lookup"><span data-stu-id="eb008-246">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="eb008-247">Zobacz [stronę układu](xref:mvc/views/layout) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="eb008-247">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="eb008-248">[Układ](xref:mvc/views/layout#specifying-a-layout) właściwość jest ustawiona *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb008-248">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb008-249">Układ jest *stron/Shared* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-249">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="eb008-250">Strony wyszukać inne widoki (układów, szablony, częściowe) hierarchiczny, w tym samym folderze co bieżącej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-250">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eb008-251">Układ w *stron/Shared* folderu można używać na dowolnej strony Razor, w obszarze *stron* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-251">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="eb008-252">Plik układu powinny przejść *stron/Shared* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-252">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb008-253">Układ jest *stron* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-253">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="eb008-254">Strony wyszukać inne widoki (układów, szablony, częściowe) hierarchiczny, w tym samym folderze co bieżącej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-254">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eb008-255">Układ w *stron* folderu można używać na dowolnej strony Razor, w obszarze *stron* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-255">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="eb008-256">Firma Microsoft zaleca **nie** umieścić ten plik układu w *widoków/Shared* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-256">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="eb008-257">*Widoki/Shared* jest wzorzec widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="eb008-257">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="eb008-258">Strony razor są przeznaczone do zależą od hierarchii folderów, nie ścieżka Konwencji.</span><span class="sxs-lookup"><span data-stu-id="eb008-258">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="eb008-259">Wyszukiwanie w widoku ze strony Razor obejmuje *stron* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-259">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="eb008-260">Układy, szablony i częściowych używasz konwencjonalnych Razor widoków i kontrolerów MVC *po prostu działają*.</span><span class="sxs-lookup"><span data-stu-id="eb008-260">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="eb008-261">Dodaj *Pages/_ViewImports.cshtml* pliku:</span><span class="sxs-lookup"><span data-stu-id="eb008-261">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="eb008-262">`@namespace` zostało wyjaśnione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="eb008-262">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="eb008-263">`@addTagHelper` Wiąże dyrektywy [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w *stron* folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-263">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="eb008-264">Gdy `@namespace` dyrektywa jest używany jawnie na stronie:</span><span class="sxs-lookup"><span data-stu-id="eb008-264">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="eb008-265">Dyrektywa ustawia obszar nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-265">The directive sets the namespace for the page.</span></span> <span data-ttu-id="eb008-266">`@model` Dyrektywy nie musi zawierać przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="eb008-266">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="eb008-267">Gdy `@namespace` dyrektywy znajduje się w *_ViewImports.cshtml*, określonego obszaru nazw dostarcza prefiksu dla przestrzeni nazw wygenerowanej na stronie, który importuje `@namespace` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="eb008-267">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="eb008-268">Pozostała część wygenerowana przestrzeń nazw (sufiks fragment) jest oddzielona względnej ścieżki między folder zawierający *_ViewImports.cshtml* i folderu zawierającego strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-268">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="eb008-269">Na przykład `PageModel` klasy *Pages/Customers/Edit.cshtml.cs* jawnie określa przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="eb008-269">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="eb008-270">*Pages/_ViewImports.cshtml* pliku ustawia następująca przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="eb008-270">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="eb008-271">Wygenerowany obszar nazw dla *Pages/Customers/Edit.cshtml* strona Razor jest taka sama jak `PageModel` klasy.</span><span class="sxs-lookup"><span data-stu-id="eb008-271">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="eb008-272">`@namespace` *współpracuje również z konwencjonalnych widokami Razor.*</span><span class="sxs-lookup"><span data-stu-id="eb008-272">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="eb008-273">Oryginalny *Pages/Create.cshtml* Wyświetl plik:</span><span class="sxs-lookup"><span data-stu-id="eb008-273">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="eb008-274">Zaktualizowany interfejs *Pages/Create.cshtml* Wyświetl plik:</span><span class="sxs-lookup"><span data-stu-id="eb008-274">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="eb008-275">[Projekt startowy stron Razor](#rpvs17) zawiera *Pages/_ValidationScriptsPartial.cshtml*, która przechwytuje sprawdzania poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="eb008-275">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="eb008-276">Aby uzyskać więcej informacji na temat widoki częściowe, zobacz <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="eb008-276">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="eb008-277">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="eb008-277">URL generation for Pages</span></span>

<span data-ttu-id="eb008-278">`Create` Strona, pokazana wcześniej, używa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="eb008-278">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="eb008-279">Aplikacja ma następującą strukturę pliku/folderu:</span><span class="sxs-lookup"><span data-stu-id="eb008-279">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="eb008-280">*/ Stron*</span><span class="sxs-lookup"><span data-stu-id="eb008-280">*/Pages*</span></span>

  * <span data-ttu-id="eb008-281">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-281">*Index.cshtml*</span></span>
  * <span data-ttu-id="eb008-282">*/ Klientów*</span><span class="sxs-lookup"><span data-stu-id="eb008-282">*/Customers*</span></span>

    * <span data-ttu-id="eb008-283">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-283">*Create.cshtml*</span></span>
    * <span data-ttu-id="eb008-284">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-284">*Edit.cshtml*</span></span>
    * <span data-ttu-id="eb008-285">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb008-285">*Index.cshtml*</span></span>

<span data-ttu-id="eb008-286">*Pages/Customers/Create.cshtml* i *Pages/Customers/Edit.cshtml* strony przekierowania do *Pages/Index.cshtml* po pomyślnym zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="eb008-286">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="eb008-287">Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-287">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="eb008-288">Ciąg `/Index` może służyć do generowania identyfikatorów URI *Pages/Index.cshtml* strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-288">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="eb008-289">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="eb008-289">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="eb008-290">Nazwa strony jest ścieżką do strony z katalogu głównego */strony* folder, w tym wiodący `/` (na przykład `/Index`).</span><span class="sxs-lookup"><span data-stu-id="eb008-290">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="eb008-291">Poprzednich przykładów generowania adresu URL oferują rozszerzone opcje i funkcji za pośrednictwem hardcoding adresu URL.</span><span class="sxs-lookup"><span data-stu-id="eb008-291">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="eb008-292">Generowanie adresu URL używa [routingu](xref:mvc/controllers/routing) można wygenerować i kodowanie parametrów, zgodnie z jak trasy jest zdefiniowany w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="eb008-292">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="eb008-293">Generowanie adresu URL dla stron obsługuje nazwy względnej.</span><span class="sxs-lookup"><span data-stu-id="eb008-293">URL generation for pages supports relative names.</span></span> <span data-ttu-id="eb008-294">W poniższej tabeli przedstawiono strony indeksu, który wybrano z różnymi `RedirectToPage` parametry z *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb008-294">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="eb008-295">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="eb008-295">RedirectToPage(x)</span></span>| <span data-ttu-id="eb008-296">Strona</span><span class="sxs-lookup"><span data-stu-id="eb008-296">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eb008-297">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="eb008-297">RedirectToPage("/Index")</span></span> | <span data-ttu-id="eb008-298">*Indeks strony*</span><span class="sxs-lookup"><span data-stu-id="eb008-298">*Pages/Index*</span></span> |
| <span data-ttu-id="eb008-299">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="eb008-299">RedirectToPage("./Index");</span></span> | <span data-ttu-id="eb008-300">*Strony/klientów/indeksu*</span><span class="sxs-lookup"><span data-stu-id="eb008-300">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="eb008-301">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="eb008-301">RedirectToPage("../Index")</span></span> | <span data-ttu-id="eb008-302">*Indeks strony*</span><span class="sxs-lookup"><span data-stu-id="eb008-302">*Pages/Index*</span></span> |
| <span data-ttu-id="eb008-303">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="eb008-303">RedirectToPage("Index")</span></span>  | <span data-ttu-id="eb008-304">*Strony/klientów/indeksu*</span><span class="sxs-lookup"><span data-stu-id="eb008-304">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="eb008-305">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, i `RedirectToPage("../Index")` są *względne nazwy*.</span><span class="sxs-lookup"><span data-stu-id="eb008-305">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="eb008-306">`RedirectToPage` Parametr jest *połączone* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="eb008-306">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="eb008-307">Nazwa względna łączenia jest przydatne w przypadku, gdy tworzenia witryn z złożonej strukturze.</span><span class="sxs-lookup"><span data-stu-id="eb008-307">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="eb008-308">Jeśli używasz względne nazwy do połączenia między stronami w folderze, można zmienić nazwę tego folderu.</span><span class="sxs-lookup"><span data-stu-id="eb008-308">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="eb008-309">Wszystkie łącza nadal działać (ponieważ nie obejmują one nazwę folderu).</span><span class="sxs-lookup"><span data-stu-id="eb008-309">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb008-310">Przekierowywanie do strony w innej [obszaru](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="eb008-310">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="eb008-311">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="eb008-311">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="eb008-312">Atrybut viewData</span><span class="sxs-lookup"><span data-stu-id="eb008-312">ViewData attribute</span></span>

<span data-ttu-id="eb008-313">Dane mogą być przekazywane do stron z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="eb008-313">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="eb008-314">Właściwości na kontrolerach ani w modelach strony Razor ozdobione `[ViewData]` ich wartości przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="eb008-314">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="eb008-315">W poniższym przykładzie `AboutModel` zawiera `Title` ozdobione właściwość `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="eb008-315">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="eb008-316">`Title` Właściwość jest ustawiona na tytuł strony informacje:</span><span class="sxs-lookup"><span data-stu-id="eb008-316">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="eb008-317">Na stronie informacje dostępu `Title` właściwość jako właściwość modelu:</span><span class="sxs-lookup"><span data-stu-id="eb008-317">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="eb008-318">W układzie tytuł jest do odczytu ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="eb008-318">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="eb008-319">TempData</span><span class="sxs-lookup"><span data-stu-id="eb008-319">TempData</span></span>

<span data-ttu-id="eb008-320">Udostępnia platformy ASP.NET Core [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) właściwość [kontrolera](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="eb008-320">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="eb008-321">Ta właściwość przechowuje dane, dopóki nie jest do odczytu.</span><span class="sxs-lookup"><span data-stu-id="eb008-321">This property stores data until it's read.</span></span> <span data-ttu-id="eb008-322">`Keep` i `Peek` metody może służyć do sprawdzenia danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="eb008-322">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="eb008-323">`TempData` jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jedno żądanie.</span><span class="sxs-lookup"><span data-stu-id="eb008-323">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="eb008-324">`[TempData]` Atrybut jest nowego w programie ASP.NET Core 2.0 i jest obsługiwane na stronach i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="eb008-324">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="eb008-325">Poniższy kod ustawia wartość `Message` przy użyciu `TempData`:</span><span class="sxs-lookup"><span data-stu-id="eb008-325">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="eb008-326">Następujące znaczniki w *Pages/Customers/Index.cshtml* pliku wyświetlana jest wartość `Message` przy użyciu `TempData`.</span><span class="sxs-lookup"><span data-stu-id="eb008-326">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="eb008-327">*Pages/Customers/Index.cshtml.cs* stosowany model strony `[TempData]` atrybutu `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="eb008-327">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="eb008-328">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="eb008-328">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="eb008-329">Procedury obsługi wielu na stronę</span><span class="sxs-lookup"><span data-stu-id="eb008-329">Multiple handlers per page</span></span>

<span data-ttu-id="eb008-330">Następująca strona generuje kod znaczników dla dwóch stron przy użyciu programów obsługi `asp-page-handler` Pomocnik tagu:</span><span class="sxs-lookup"><span data-stu-id="eb008-330">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="eb008-331">Formularz w poprzednim przykładzie ma dwa przesłać przyciski, za pomocą każdego `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="eb008-331">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="eb008-332">`asp-page-handler` Atrybutu jest uzupełnieniem do `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="eb008-332">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="eb008-333">`asp-page-handler` generuje adresy URL, którzy przesłali do każdej metody obsługi zdefiniowane przez stronę.</span><span class="sxs-lookup"><span data-stu-id="eb008-333">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="eb008-334">`asp-page` nie jest określona, ponieważ plik jest tworzenie łączy do bieżącej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-334">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="eb008-335">Model strony:</span><span class="sxs-lookup"><span data-stu-id="eb008-335">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="eb008-336">W poprzednim kodzie użyto *o nazwie metody obsługi*.</span><span class="sxs-lookup"><span data-stu-id="eb008-336">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="eb008-337">Metody obsługi o nazwie są tworzone przez przyjęcie tekst nazwę po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="eb008-337">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="eb008-338">W poprzednim przykładzie, metody strony są OnPost**JoinList**Async i OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="eb008-338">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="eb008-339">Za pomocą *OnPost* i *Async* usunięty, są nazwy programów obsługi `JoinList` i `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eb008-339">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="eb008-340">Za pomocą poprzedniego kodu Ścieżka adresu URL, które są przesyłane `OnPostJoinListAsync` jest `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eb008-340">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="eb008-341">Ścieżka adresu URL, które są przesyłane `OnPostJoinListUCAsync` jest `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eb008-341">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="eb008-342">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="eb008-342">Custom routes</span></span>

<span data-ttu-id="eb008-343">Użyj `@page` dyrektywę:</span><span class="sxs-lookup"><span data-stu-id="eb008-343">Use the `@page` directive to:</span></span>

* <span data-ttu-id="eb008-344">Określ niestandardowe trasy do strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-344">Specify a custom route to a page.</span></span> <span data-ttu-id="eb008-345">Na przykład można ustawić trasy do strony informacje `/Some/Other/Path` z `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eb008-345">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="eb008-346">Dołącz segmentów do trasy domyślnej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-346">Append segments to a page's default route.</span></span> <span data-ttu-id="eb008-347">Na przykład "item" segmentu można dodać do trasy domyślnej strony z `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="eb008-347">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="eb008-348">Dodaj parametry do trasy domyślnej strony.</span><span class="sxs-lookup"><span data-stu-id="eb008-348">Append parameters to a page's default route.</span></span> <span data-ttu-id="eb008-349">Na przykład parametru ID, `id`, może być wymagane dla strony z `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="eb008-349">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="eb008-350">Ścieżka względem katalogu głównego, w wyznaczonym przez tyldy (`~`) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="eb008-350">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="eb008-351">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eb008-351">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="eb008-352">Możesz zmienić ciągu zapytania `?handler=JoinList` w adresie URL do segmentu trasy `/JoinList` , określając szablon trasy `@page "{handler?}"`.</span><span class="sxs-lookup"><span data-stu-id="eb008-352">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="eb008-353">Jeśli nie chcesz, aby ciąg zapytania `?handler=JoinList` w adresie URL, możesz zmienić trasy, aby umieścić nazwę programu obsługi w część ścieżki adresu URL.</span><span class="sxs-lookup"><span data-stu-id="eb008-353">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="eb008-354">Trasy można dostosować, dodając szablon trasy, ujęte w podwójny cudzysłów po `@page` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="eb008-354">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="eb008-355">Za pomocą poprzedniego kodu Ścieżka adresu URL, które są przesyłane `OnPostJoinListAsync` jest `http://localhost:5000/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eb008-355">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="eb008-356">Ścieżka adresu URL, które są przesyłane `OnPostJoinListUCAsync` jest `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eb008-356">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="eb008-357">`?` Następujące `handler` oznacza, że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="eb008-357">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="eb008-358">Konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="eb008-358">Configuration and settings</span></span>

<span data-ttu-id="eb008-359">Aby skonfigurować opcje zaawansowane, należy użyć metody rozszerzenia `AddRazorPagesOptions` w Konstruktorze MVC:</span><span class="sxs-lookup"><span data-stu-id="eb008-359">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="eb008-360">Obecnie możesz używać `RazorPagesOptions` katalog główny strony lub Dodaj konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="eb008-360">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="eb008-361">Firma Microsoft będzie włączyć więcej rozszerzeń w ten sposób w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="eb008-361">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="eb008-362">Przeprowadzać prekompilowanie widoków, zobacz [kompilacja widoku Razor](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="eb008-362">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="eb008-363">[Pobieranie i wyświetlanie przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="eb008-363">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="eb008-364">Zobacz [Rozpoczynanie pracy ze stronami Razor](xref:tutorials/razor-pages/razor-pages-start), która jest oparta na wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="eb008-364">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="eb008-365">Określ, czy w katalogu głównym zawartości stron Razor</span><span class="sxs-lookup"><span data-stu-id="eb008-365">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="eb008-366">Domyślnie strony Razor są umieszczone w */strony* katalogu.</span><span class="sxs-lookup"><span data-stu-id="eb008-366">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="eb008-367">Dodaj [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) do określenia, czy strony Razor zawartości katalogu głównego ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikacji:</span><span class="sxs-lookup"><span data-stu-id="eb008-367">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="eb008-368">Określ, czy w katalogu głównym niestandardowych stron Razor</span><span class="sxs-lookup"><span data-stu-id="eb008-368">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="eb008-369">Dodaj [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) do określenia, czy strony Razor w katalogu głównym niestandardowego w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="eb008-369">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="eb008-370">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="eb008-370">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
