---
title: Dodawanie widoku do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Dodawanie widoku do prostej aplikacji ASP.NET Core MVC
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: de75c3b0651c0cda6629af786d7db9dc83bc4fef
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288823"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="410d9-103">Dodawanie widoku do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="410d9-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="410d9-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="410d9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="410d9-105">W tej sekcji zmodyfikujesz klasę `HelloWorldController`, aby używać plików widoku [Razor](xref:mvc/views/razor) do czystego hermetyzacji procesu generowania odpowiedzi HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="410d9-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="410d9-106">Tworzysz plik szablonu widoku przy użyciu Razor.</span><span class="sxs-lookup"><span data-stu-id="410d9-106">You create a view template file using Razor.</span></span> <span data-ttu-id="410d9-107">Szablony widoku oparte na Razor mają rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="410d9-108">Zapewniają elegancki sposób tworzenia danych wyjściowych HTML za pomocą C#.</span><span class="sxs-lookup"><span data-stu-id="410d9-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="410d9-109">Obecnie Metoda `Index` zwraca ciąg z komunikatem, który jest zakodowany w klasie Controller.</span><span class="sxs-lookup"><span data-stu-id="410d9-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="410d9-110">W klasie `HelloWorldController` Zastąp metodę `Index` następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="410d9-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="410d9-111">Poprzedni kod wywołuje metodę <xref:Microsoft.AspNetCore.Mvc.Controller.View*> kontrolera.</span><span class="sxs-lookup"><span data-stu-id="410d9-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="410d9-112">Używa szablonu widoku do wygenerowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="410d9-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="410d9-113">Metody kontrolera (znane również jako *metody akcji*), takie jak powyższa metoda `Index`, zazwyczaj zwracają <xref:Microsoft.AspNetCore.Mvc.IActionResult> (lub klasę pochodną <xref:Microsoft.AspNetCore.Mvc.ActionResult>), a nie typ podobny do `string`.</span><span class="sxs-lookup"><span data-stu-id="410d9-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="410d9-114">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="410d9-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="410d9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="410d9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="410d9-116">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="410d9-117">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="410d9-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="410d9-118">W oknie dialogowym **Dodaj nowy element — MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="410d9-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="410d9-119">W polu wyszukiwania w prawym górnym rogu wprowadź *Widok*</span><span class="sxs-lookup"><span data-stu-id="410d9-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="410d9-120">Wybieranie **widoku Razor**</span><span class="sxs-lookup"><span data-stu-id="410d9-120">Select **Razor View**</span></span>

  * <span data-ttu-id="410d9-121">Zachowaj wartość pola **Nazwa** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="410d9-122">Wybierz pozycję **Dodaj**</span><span class="sxs-lookup"><span data-stu-id="410d9-122">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="410d9-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="410d9-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="410d9-125">Dodaj widok `Index` dla `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="410d9-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="410d9-126">Dodaj nowy folder o nazwie *viewss/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="410d9-127">Dodaj nowy plik do pliku *viewss/HelloWorld* Name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="410d9-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="410d9-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="410d9-129">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="410d9-130">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="410d9-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="410d9-131">W **nowy plik** okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="410d9-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="410d9-132">W lewym okienku wybierz pozycję **Sieć Web** .</span><span class="sxs-lookup"><span data-stu-id="410d9-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="410d9-133">W środkowym okienku wybierz pozycję **pusty plik HTML** .</span><span class="sxs-lookup"><span data-stu-id="410d9-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="410d9-134">Wpisz *index. cshtml* w polu **Nazwa** .</span><span class="sxs-lookup"><span data-stu-id="410d9-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="410d9-135">Wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="410d9-135">Select **New**.</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="410d9-137">Zastąp zawartość pliku widoku Razor *widoków/HelloWorld/index. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="410d9-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="410d9-138">Przejdź do adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="410d9-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="410d9-139">Metoda `Index` w `HelloWorldController` nie zrobiła wiele; uruchomiono instrukcję `return View();`, która określa, że metoda powinna używać pliku szablonu widoku, aby renderować odpowiedź do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="410d9-140">Ponieważ nazwa pliku szablonu widoku nie została określona, MVC domyślnie używa domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="410d9-141">Domyślny plik widoku ma taką samą nazwę jak Metoda (`Index`), więc jest używana */views/HelloWorld/index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="410d9-142">Na poniższej ilustracji przedstawiono ciąg "Hello z naszego szablonu widoku!"</span><span class="sxs-lookup"><span data-stu-id="410d9-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="410d9-143">zakodowane w widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-143">hard-coded in the view.</span></span>

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="410d9-145">Zmień widoki i strony układu</span><span class="sxs-lookup"><span data-stu-id="410d9-145">Change views and layout pages</span></span>

<span data-ttu-id="410d9-146">Wybierz linki menu (**MvcMovie**, **Home**i **privacy**).</span><span class="sxs-lookup"><span data-stu-id="410d9-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="410d9-147">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="410d9-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="410d9-148">Układ menu jest implementowany w pliku *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="410d9-149">Otwórz plik *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="410d9-150">Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="410d9-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="410d9-151">Znajdź wiersz `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="410d9-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="410d9-152">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone strony specyficzne dla widoku są *widoczne na stronie* układ.</span><span class="sxs-lookup"><span data-stu-id="410d9-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="410d9-153">Na przykład po wybraniu linku **prywatności** widok **widoki/główna/prywatność. cshtml** jest renderowany wewnątrz metody `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="410d9-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="410d9-154">Zmień tytuł, stopkę i łącze menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="410d9-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="410d9-155">Zastąp zawartość pliku *viewss/Shared/_Layout. cshtml* następującym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="410d9-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="410d9-156">Zmiany są wyróżnione:</span><span class="sxs-lookup"><span data-stu-id="410d9-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="410d9-157">Poprzednia Adiustacja wprowadziła następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="410d9-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="410d9-158">3 wystąpienia `MvcMovie` do `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="410d9-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="410d9-159">Element zakotwiczenia `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`, aby `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="410d9-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="410d9-160">W powyższym znaczniku [atrybut pomocnika `asp-area=""` tagu zakotwiczonego](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) został pominięty, ponieważ ta aplikacja nie korzysta z [obszarów](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="410d9-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="410d9-161">**Uwaga**: kontroler `Movies` nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="410d9-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="410d9-162">W tym momencie łącze `Movie App` nie działa.</span><span class="sxs-lookup"><span data-stu-id="410d9-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="410d9-163">Zapisz zmiany i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="410d9-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="410d9-164">Zwróć uwagę, jak tytuł na karcie Przeglądarka wyświetla **zasady zachowania poufności informacji — aplikacja dla filmów** zamiast **zasad ochrony prywatności — film MVC**:</span><span class="sxs-lookup"><span data-stu-id="410d9-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="410d9-166">Wybierz link **domowy** i zwróć uwagę na to, że tytuł i tekst zakotwiczenia również wyświetlają **aplikację Movie**.</span><span class="sxs-lookup"><span data-stu-id="410d9-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="410d9-167">Udało nam się wprowadzić zmiany w szablonie układu i wszystkie strony w witrynie będą odzwierciedlały nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="410d9-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="410d9-168">Przejrzyj plik *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="410d9-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="410d9-169">Plik *views/_ViewStart. cshtml* umieszcza w pliku *views/Shared/_Layout. cshtml* w każdym widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="410d9-170">Właściwość `Layout` może służyć do ustawiania innego widoku układu lub ustawiania jej na `null`, co spowoduje, że żaden plik układu nie zostanie użyty.</span><span class="sxs-lookup"><span data-stu-id="410d9-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="410d9-171">Zmień tytuł i element `<h2>` w pliku widoku */HelloWorld/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="410d9-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="410d9-172">Tytuł i element `<h2>` są nieco inne, więc można zobaczyć, który bit kodu zmienia ekran.</span><span class="sxs-lookup"><span data-stu-id="410d9-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="410d9-173">`ViewData["Title"] = "Movie List";` w powyższym kodzie ustawia właściwość `Title` słownika `ViewData` na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="410d9-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="410d9-174">Właściwość `Title` jest używana w elemencie `<title>` HTML na stronie układ:</span><span class="sxs-lookup"><span data-stu-id="410d9-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="410d9-175">Zapisz zmianę i przejdź do `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="410d9-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="410d9-176">Zwróć uwagę, że tytuł przeglądarki, nagłówek podstawowy i pomocnicze nagłówki zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="410d9-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="410d9-177">(Jeśli w przeglądarce nie są widoczne zmiany, może być wyświetlana zawartość z pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="410d9-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="410d9-178">Naciśnij kombinację klawiszy CTRL + F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera. Tytuł przeglądarki jest tworzony przy użyciu `ViewData["Title"]` ustawionych w szablonie *index. cshtml* , a dodatkowa "-Movie App" dodana w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="410d9-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="410d9-179">Zawartość szablonu widoku *index. cshtml* jest scalana z szablonem widok *widoki/udostępnione/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="410d9-180">Pojedyncza odpowiedź HTML jest wysyłana do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="410d9-181">Szablony układów ułatwiają wprowadzanie zmian, które są stosowane na wszystkich stronach w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="410d9-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="410d9-182">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="410d9-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="410d9-184">Nasz mały bit "Data" (w tym przypadku "Hello z naszego szablonu widoku!")</span><span class="sxs-lookup"><span data-stu-id="410d9-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="410d9-185">komunikat) jest zakodowany na stałe.</span><span class="sxs-lookup"><span data-stu-id="410d9-185">message) is hard-coded, though.</span></span> <span data-ttu-id="410d9-186">Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie jest jeszcze "M" (model).</span><span class="sxs-lookup"><span data-stu-id="410d9-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="410d9-187">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="410d9-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="410d9-188">Akcje kontrolera są wywoływane w odpowiedzi na żądanie przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="410d9-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="410d9-189">Klasa kontrolera to miejsce, w którym zapisano kod obsługujący żądania przeglądarki przychodzącej.</span><span class="sxs-lookup"><span data-stu-id="410d9-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="410d9-190">Kontroler pobiera dane ze źródła danych i decyduje o typie odpowiedzi wysyłanej z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="410d9-191">Szablony widoków mogą być używane z poziomu kontrolera do generowania i formatowania odpowiedzi HTML w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="410d9-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="410d9-192">Kontrolery są odpowiedzialne za dostarczanie danych wymaganych w celu renderowania odpowiedzi przez szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="410d9-193">Najlepsze rozwiązanie: szablony widoków **nie** powinny wykonywać logiki biznesowej ani bezpośrednio korzystać z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="410d9-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="410d9-194">Zamiast tego szablon widoku powinien współpracować tylko z danymi, które są udostępniane przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="410d9-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="410d9-195">Utrzymywanie tego "separacji zagadnień" pomaga zachować kod, weryfikowalne i łatwość utrzymania.</span><span class="sxs-lookup"><span data-stu-id="410d9-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="410d9-196">Obecnie Metoda `Welcome` w klasie `HelloWorldController` przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="410d9-197">Zamiast przetworzyć tę odpowiedź jako ciąg, należy zmienić kontroler tak, aby używał szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="410d9-198">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą zostać przesłane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="410d9-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="410d9-199">W tym celu należy mieć kontroler umieszczający dane dynamiczne (parametry) wymagane przez szablon widoku w słowniku `ViewData`, do którego ma dostęp szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="410d9-200">W *HelloWorldController.cs*zmień metodę `Welcome`, aby dodać `Message` i `NumTimes` wartość do słownika `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="410d9-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="410d9-201">Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; Obiekt `ViewData` nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim elementu.</span><span class="sxs-lookup"><span data-stu-id="410d9-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="410d9-202">[System powiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje nazwane parametry (`name` i `numTimes`) z ciągu zapytania na pasku adresu do parametrów w metodzie.</span><span class="sxs-lookup"><span data-stu-id="410d9-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="410d9-203">Pełny plik *HelloWorldController.cs* wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="410d9-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="410d9-204">Obiekt słownika `ViewData` zawiera dane, które zostaną przesłane do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="410d9-205">Utwórz szablon widoku powitalnego o nazwie *przeglądający/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="410d9-206">Utworzysz pętlę w szablonie widoku *Welcome. cshtml* , który wyświetla "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="410d9-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="410d9-207">Zastąp zawartość *widoków/HelloWorld/Welcome. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="410d9-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="410d9-208">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="410d9-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="410d9-209">Dane są pobierane z adresu URL i przesyłane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="410d9-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="410d9-210">Kontroler pakuje dane do słownika `ViewData` i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="410d9-211">Widok następnie renderuje dane jako HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-211">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności pokazujący etykietę powitalną i frazę Hello Rick pokazywane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="410d9-213">W powyższym przykładzie słownik `ViewData` był używany do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="410d9-214">W dalszej części tego samouczka model widoku służy do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="410d9-215">Podejście model widoku do przekazywania danych jest ogólnie preferowane przez podejście `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="410d9-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="410d9-216">Aby uzyskać więcej informacji [, zobacz Kiedy używać ViewBag, ViewData lub TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="410d9-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="410d9-217">W następnym samouczku zostanie utworzona baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="410d9-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="410d9-218">[Poprzedni](adding-controller.md)
> [Następny](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="410d9-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="410d9-219">W tej sekcji zmodyfikujesz klasę `HelloWorldController`, aby używać plików widoku [Razor](xref:mvc/views/razor) do czystego hermetyzacji procesu generowania odpowiedzi HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="410d9-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="410d9-220">Tworzysz plik szablonu widoku przy użyciu Razor.</span><span class="sxs-lookup"><span data-stu-id="410d9-220">You create a view template file using Razor.</span></span> <span data-ttu-id="410d9-221">Szablony widoku oparte na Razor mają rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="410d9-222">Zapewniają elegancki sposób tworzenia danych wyjściowych HTML za pomocą C#.</span><span class="sxs-lookup"><span data-stu-id="410d9-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="410d9-223">Obecnie Metoda `Index` zwraca ciąg z komunikatem, który jest zakodowany w klasie Controller.</span><span class="sxs-lookup"><span data-stu-id="410d9-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="410d9-224">W klasie `HelloWorldController` Zastąp metodę `Index` następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="410d9-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="410d9-225">Poprzedni kod wywołuje metodę <xref:Microsoft.AspNetCore.Mvc.Controller.View*> kontrolera.</span><span class="sxs-lookup"><span data-stu-id="410d9-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="410d9-226">Używa szablonu widoku do wygenerowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="410d9-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="410d9-227">Metody kontrolera (znane również jako *metody akcji*), takie jak powyższa metoda `Index`, zazwyczaj zwracają <xref:Microsoft.AspNetCore.Mvc.IActionResult> (lub klasę pochodną <xref:Microsoft.AspNetCore.Mvc.ActionResult>), a nie typ podobny do `string`.</span><span class="sxs-lookup"><span data-stu-id="410d9-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="410d9-228">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="410d9-228">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="410d9-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="410d9-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="410d9-230">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="410d9-231">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="410d9-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="410d9-232">W oknie dialogowym **Dodaj nowy element — MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="410d9-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="410d9-233">W polu wyszukiwania w prawym górnym rogu wprowadź *Widok*</span><span class="sxs-lookup"><span data-stu-id="410d9-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="410d9-234">Wybieranie **widoku Razor**</span><span class="sxs-lookup"><span data-stu-id="410d9-234">Select **Razor View**</span></span>

  * <span data-ttu-id="410d9-235">Zachowaj wartość pola **Nazwa** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="410d9-236">Wybierz pozycję **Dodaj**</span><span class="sxs-lookup"><span data-stu-id="410d9-236">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="410d9-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="410d9-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="410d9-239">Dodaj widok `Index` dla `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="410d9-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="410d9-240">Dodaj nowy folder o nazwie *viewss/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="410d9-241">Dodaj nowy plik do pliku *viewss/HelloWorld* Name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="410d9-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="410d9-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="410d9-243">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="410d9-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="410d9-244">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="410d9-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="410d9-245">W **nowy plik** okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="410d9-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="410d9-246">W lewym okienku wybierz pozycję **Sieć Web** .</span><span class="sxs-lookup"><span data-stu-id="410d9-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="410d9-247">W środkowym okienku wybierz pozycję **pusty plik HTML** .</span><span class="sxs-lookup"><span data-stu-id="410d9-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="410d9-248">Wpisz *index. cshtml* w polu **Nazwa** .</span><span class="sxs-lookup"><span data-stu-id="410d9-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="410d9-249">Wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="410d9-249">Select **New**.</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="410d9-251">Zastąp zawartość pliku widoku Razor *widoków/HelloWorld/index. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="410d9-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="410d9-252">Przejdź do adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="410d9-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="410d9-253">Metoda `Index` w `HelloWorldController` nie zrobiła wiele; uruchomiono instrukcję `return View();`, która określa, że metoda powinna używać pliku szablonu widoku, aby renderować odpowiedź do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="410d9-254">Ponieważ nazwa pliku szablonu widoku nie została określona, MVC domyślnie używa domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="410d9-255">Domyślny plik widoku ma taką samą nazwę jak Metoda (`Index`), więc jest używana */views/HelloWorld/index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="410d9-256">Na poniższej ilustracji przedstawiono ciąg "Hello z naszego szablonu widoku!"</span><span class="sxs-lookup"><span data-stu-id="410d9-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="410d9-257">zakodowane w widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-257">hard-coded in the view.</span></span>

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="410d9-259">Zmień widoki i strony układu</span><span class="sxs-lookup"><span data-stu-id="410d9-259">Change views and layout pages</span></span>

<span data-ttu-id="410d9-260">Wybierz linki menu (**MvcMovie**, **Home**i **privacy**).</span><span class="sxs-lookup"><span data-stu-id="410d9-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="410d9-261">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="410d9-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="410d9-262">Układ menu jest implementowany w pliku *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="410d9-263">Otwórz plik *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="410d9-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="410d9-264">Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="410d9-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="410d9-265">Znajdź wiersz `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="410d9-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="410d9-266">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone strony specyficzne dla widoku są *widoczne na stronie* układ.</span><span class="sxs-lookup"><span data-stu-id="410d9-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="410d9-267">Na przykład po wybraniu linku **prywatności** widok **widoki/główna/prywatność. cshtml** jest renderowany wewnątrz metody `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="410d9-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="410d9-268">Zmień tytuł, stopkę i łącze menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="410d9-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="410d9-269">W elementach tytuł i stopka Zmień `MvcMovie` na `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="410d9-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="410d9-270">Zmień `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` elementu zakotwiczenia, aby `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="410d9-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="410d9-271">Następujące znaczniki pokazują wyróżnione zmiany:</span><span class="sxs-lookup"><span data-stu-id="410d9-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="410d9-272">W poprzednim znaczniku [atrybut pomocnika `asp-area` tagu zakotwiczenia](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) został pominięty, ponieważ ta aplikacja nie korzysta z [obszarów](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="410d9-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="410d9-273">**Uwaga**: kontroler `Movies` nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="410d9-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="410d9-274">W tym momencie łącze `Movie App` nie działa.</span><span class="sxs-lookup"><span data-stu-id="410d9-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="410d9-275">Zapisz zmiany i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="410d9-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="410d9-276">Zwróć uwagę, jak tytuł na karcie Przeglądarka wyświetla **zasady zachowania poufności informacji — aplikacja dla filmów** zamiast **zasad ochrony prywatności — film MVC**:</span><span class="sxs-lookup"><span data-stu-id="410d9-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="410d9-278">Wybierz link **domowy** i zwróć uwagę na to, że tytuł i tekst zakotwiczenia również wyświetlają **aplikację Movie**.</span><span class="sxs-lookup"><span data-stu-id="410d9-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="410d9-279">Udało nam się wprowadzić zmiany w szablonie układu i wszystkie strony w witrynie będą odzwierciedlały nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="410d9-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="410d9-280">Przejrzyj plik *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="410d9-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="410d9-281">Plik *views/_ViewStart. cshtml* umieszcza w pliku *views/Shared/_Layout. cshtml* w każdym widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="410d9-282">Właściwość `Layout` może służyć do ustawiania innego widoku układu lub ustawiania jej na `null`, co spowoduje, że żaden plik układu nie zostanie użyty.</span><span class="sxs-lookup"><span data-stu-id="410d9-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="410d9-283">Zmień tytuł i element `<h2>` w pliku widoku */HelloWorld/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="410d9-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="410d9-284">Tytuł i element `<h2>` są nieco inne, więc można zobaczyć, który bit kodu zmienia ekran.</span><span class="sxs-lookup"><span data-stu-id="410d9-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="410d9-285">`ViewData["Title"] = "Movie List";` w powyższym kodzie ustawia właściwość `Title` słownika `ViewData` na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="410d9-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="410d9-286">Właściwość `Title` jest używana w elemencie `<title>` HTML na stronie układ:</span><span class="sxs-lookup"><span data-stu-id="410d9-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="410d9-287">Zapisz zmianę i przejdź do `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="410d9-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="410d9-288">Zwróć uwagę, że tytuł przeglądarki, nagłówek podstawowy i pomocnicze nagłówki zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="410d9-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="410d9-289">(Jeśli w przeglądarce nie są widoczne zmiany, może być wyświetlana zawartość z pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="410d9-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="410d9-290">Naciśnij kombinację klawiszy CTRL + F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera. Tytuł przeglądarki jest tworzony przy użyciu `ViewData["Title"]` ustawionych w szablonie *index. cshtml* , a dodatkowa "-Movie App" dodana w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="410d9-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="410d9-291">Zwróć uwagę na to, jak zawartość w szablonie widoku *index. cshtml* została scalona z szablonem *widoków/Shared/_Layout. cshtml* . do przeglądarki została WYSŁANA pojedyncza odpowiedź html.</span><span class="sxs-lookup"><span data-stu-id="410d9-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="410d9-292">Szablony układów ułatwiają wprowadzanie zmian, które są stosowane do wszystkich stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="410d9-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="410d9-293">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="410d9-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="410d9-295">Nasz mały bit "Data" (w tym przypadku "Hello z naszego szablonu widoku!")</span><span class="sxs-lookup"><span data-stu-id="410d9-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="410d9-296">komunikat) jest zakodowany na stałe.</span><span class="sxs-lookup"><span data-stu-id="410d9-296">message) is hard-coded, though.</span></span> <span data-ttu-id="410d9-297">Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie jest jeszcze "M" (model).</span><span class="sxs-lookup"><span data-stu-id="410d9-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="410d9-298">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="410d9-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="410d9-299">Akcje kontrolera są wywoływane w odpowiedzi na żądanie przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="410d9-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="410d9-300">Klasa kontrolera to miejsce, w którym zapisano kod obsługujący żądania przeglądarki przychodzącej.</span><span class="sxs-lookup"><span data-stu-id="410d9-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="410d9-301">Kontroler pobiera dane ze źródła danych i decyduje o typie odpowiedzi wysyłanej z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="410d9-302">Szablony widoków mogą być używane z poziomu kontrolera do generowania i formatowania odpowiedzi HTML w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="410d9-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="410d9-303">Kontrolery są odpowiedzialne za dostarczanie danych wymaganych w celu renderowania odpowiedzi przez szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="410d9-304">Najlepsze rozwiązanie: szablony widoków **nie** powinny wykonywać logiki biznesowej ani bezpośrednio korzystać z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="410d9-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="410d9-305">Zamiast tego szablon widoku powinien współpracować tylko z danymi, które są udostępniane przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="410d9-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="410d9-306">Utrzymywanie tego "separacji zagadnień" pomaga zachować kod, weryfikowalne i łatwość utrzymania.</span><span class="sxs-lookup"><span data-stu-id="410d9-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="410d9-307">Obecnie Metoda `Welcome` w klasie `HelloWorldController` przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="410d9-308">Zamiast przetworzyć tę odpowiedź jako ciąg, należy zmienić kontroler tak, aby używał szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="410d9-309">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą zostać przesłane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="410d9-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="410d9-310">W tym celu należy mieć kontroler umieszczający dane dynamiczne (parametry) wymagane przez szablon widoku w słowniku `ViewData`, do którego ma dostęp szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="410d9-311">W *HelloWorldController.cs*zmień metodę `Welcome`, aby dodać `Message` i `NumTimes` wartość do słownika `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="410d9-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="410d9-312">Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; Obiekt `ViewData` nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim elementu.</span><span class="sxs-lookup"><span data-stu-id="410d9-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="410d9-313">[System powiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje nazwane parametry (`name` i `numTimes`) z ciągu zapytania na pasku adresu do parametrów w metodzie.</span><span class="sxs-lookup"><span data-stu-id="410d9-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="410d9-314">Pełny plik *HelloWorldController.cs* wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="410d9-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="410d9-315">Obiekt słownika `ViewData` zawiera dane, które zostaną przesłane do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="410d9-316">Utwórz szablon widoku powitalnego o nazwie *przeglądający/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="410d9-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="410d9-317">Utworzysz pętlę w szablonie widoku *Welcome. cshtml* , który wyświetla "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="410d9-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="410d9-318">Zastąp zawartość *widoków/HelloWorld/Welcome. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="410d9-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="410d9-319">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="410d9-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="410d9-320">Dane są pobierane z adresu URL i przesyłane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="410d9-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="410d9-321">Kontroler pakuje dane do słownika `ViewData` i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="410d9-322">Widok następnie renderuje dane jako HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="410d9-322">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności pokazujący etykietę powitalną i frazę Hello Rick pokazywane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="410d9-324">W powyższym przykładzie słownik `ViewData` był używany do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="410d9-325">W dalszej części tego samouczka model widoku służy do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="410d9-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="410d9-326">Podejście model widoku do przekazywania danych jest ogólnie preferowane przez podejście `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="410d9-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="410d9-327">Aby uzyskać więcej informacji [, zobacz Kiedy używać ViewBag, ViewData lub TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="410d9-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="410d9-328">W następnym samouczku zostanie utworzona baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="410d9-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="410d9-329">[Poprzedni](adding-controller.md)
> [Następny](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="410d9-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
