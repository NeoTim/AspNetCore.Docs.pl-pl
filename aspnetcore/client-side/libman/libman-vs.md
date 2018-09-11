---
title: LibMan za pomocą platformy ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać LibMan w projektach programu ASP.NET Core z programem Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: a653b1a5c07feca8672ba38e0cda3ddc30482c5a
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312182"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="da61d-103">LibMan za pomocą platformy ASP.NET Core w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da61d-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="da61d-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="da61d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="da61d-105">Program Visual Studio ma wbudowaną obsługę [LibMan](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:</span><span class="sxs-lookup"><span data-stu-id="da61d-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="da61d-106">Obsługa konfigurowania i uruchamiania operacji przywracania LibMan podczas kompilacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="da61d-107">Elementy menu do wyzwalania przywracania LibMan i wyczyść operacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="da61d-108">Okno dialogowe Wyszukiwanie do znajdowania bibliotek i dodawanie plików do projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="da61d-109">Edycję *libman.json*&mdash;LibMan pliku manifestu.</span><span class="sxs-lookup"><span data-stu-id="da61d-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="da61d-110">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:tutorials/index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="da61d-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:tutorials/index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="da61d-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="da61d-111">Prerequisites</span></span>

* <span data-ttu-id="da61d-112">Visual Studio 2017 w wersji należy zachować 15,8 lub nowszy z **ASP.NET i tworzenie aplikacji internetowych** obciążenia</span><span class="sxs-lookup"><span data-stu-id="da61d-112">Visual Studio 2017 version 15.8 or later with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="da61d-113">Dodaj pliki bibliotek</span><span class="sxs-lookup"><span data-stu-id="da61d-113">Add library files</span></span>

<span data-ttu-id="da61d-114">Pliki biblioteki mogą być dodawane do projektu programu ASP.NET Core na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="da61d-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="da61d-115">Użyj okna dialogowego Dodawanie biblioteki po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="da61d-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="da61d-116">Ręczne konfigurowanie LibMan we wpisach w plikach manifestu</span><span class="sxs-lookup"><span data-stu-id="da61d-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="da61d-117">Użyj okna dialogowego Dodawanie biblioteki po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="da61d-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="da61d-118">Wykonaj następujące kroki, aby zainstalować bibliotekę klienta:</span><span class="sxs-lookup"><span data-stu-id="da61d-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="da61d-119">W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy folder projektu, w którym można dodać plików.</span><span class="sxs-lookup"><span data-stu-id="da61d-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="da61d-120">Wybierz **Dodaj** > **biblioteki po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="da61d-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="da61d-121">**Dodaj biblioteki po stronie klienta** zostanie wyświetlone okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="da61d-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Dodaj okno dialogowe biblioteki po stronie klienta](_static/add-library-dialog.png)

* <span data-ttu-id="da61d-123">Wybierz dostawcę biblioteki z **dostawcy** listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="da61d-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="da61d-124">CDNJS jest dostawcą domyślnym.</span><span class="sxs-lookup"><span data-stu-id="da61d-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="da61d-125">Wpisz nazwę biblioteki, aby pobrać w **biblioteki** pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="da61d-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="da61d-126">Technologia IntelliSense zawiera listę bibliotek, począwszy od podany tekst.</span><span class="sxs-lookup"><span data-stu-id="da61d-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="da61d-127">Wybierz bibliotekę z listy technologii IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="da61d-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="da61d-128">Zwróć uwagę, jest sufiks nazwy biblioteki `@` symboli i najnowsza stabilna wersja znane z wybranym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="da61d-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="da61d-129">Zdecyduj, które pliki do uwzględnienia:</span><span class="sxs-lookup"><span data-stu-id="da61d-129">Decide which files to include:</span></span>
  * <span data-ttu-id="da61d-130">Wybierz **Dołącz wszystkie pliki biblioteki** przycisk radiowy, aby uwzględnić wszystkie pliki biblioteki.</span><span class="sxs-lookup"><span data-stu-id="da61d-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="da61d-131">Wybierz **wybierz konkretne pliki** przycisk radiowy, aby uwzględnić podzbiór plików biblioteki programu.</span><span class="sxs-lookup"><span data-stu-id="da61d-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="da61d-132">Po wybraniu przycisku radiowego drzewo selektora plików jest włączona.</span><span class="sxs-lookup"><span data-stu-id="da61d-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="da61d-133">Zaznacz pola wyboru po lewej stronie nazwy plików do pobrania.</span><span class="sxs-lookup"><span data-stu-id="da61d-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="da61d-134">Określ folder projektu, do przechowywania plików w **lokalizacji docelowej** pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="da61d-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="da61d-135">Jako zalecenie magazyn każdej biblioteki w oddzielnym folderze.</span><span class="sxs-lookup"><span data-stu-id="da61d-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="da61d-136">Sugerowany **lokalizacji docelowej** folderu opiera się na lokalizację, z którego uruchomiono okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="da61d-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="da61d-137">Jeśli uruchomionego z katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="da61d-137">If launched from the project root:</span></span>
    * <span data-ttu-id="da61d-138">*Wwwroot/lib* jest używany, gdy *wwwroot* istnieje.</span><span class="sxs-lookup"><span data-stu-id="da61d-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="da61d-139">*lib* jest używany, gdy *wwwroot* nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="da61d-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="da61d-140">Jeśli uruchomionego z folderu projektu, nazwę odpowiedniego folderu jest używany.</span><span class="sxs-lookup"><span data-stu-id="da61d-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="da61d-141">Sugestia folderu jest sufiks nazwy biblioteki.</span><span class="sxs-lookup"><span data-stu-id="da61d-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="da61d-142">W poniższej tabeli przedstawiono sugestie folderu podczas instalowania jQuery w projekcie stron Razor.</span><span class="sxs-lookup"><span data-stu-id="da61d-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="da61d-143">Uruchom lokalizacji</span><span class="sxs-lookup"><span data-stu-id="da61d-143">Launch location</span></span>                           |<span data-ttu-id="da61d-144">Sugerowane folderu</span><span class="sxs-lookup"><span data-stu-id="da61d-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="da61d-145">katalog główny projektu (Jeśli *wwwroot* istnieje)</span><span class="sxs-lookup"><span data-stu-id="da61d-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="da61d-146">*Wwwroot/lib/jquery /*</span><span class="sxs-lookup"><span data-stu-id="da61d-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="da61d-147">katalog główny projektu (Jeśli *wwwroot* nie istnieje)</span><span class="sxs-lookup"><span data-stu-id="da61d-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="da61d-148">*lib/jquery /*</span><span class="sxs-lookup"><span data-stu-id="da61d-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="da61d-149">*Strony* folderu w projekcie</span><span class="sxs-lookup"><span data-stu-id="da61d-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="da61d-150">*Strony/jquery /*</span><span class="sxs-lookup"><span data-stu-id="da61d-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="da61d-151">Kliknij przycisk **zainstalować** przycisk, aby pobrać pliki, na konfiguracji w *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="da61d-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="da61d-152">Przegląd **Library Manager** kanału **dane wyjściowe** okna, aby uzyskać szczegółowe informacje dotyczące instalacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="da61d-153">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="da61d-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="da61d-154">Ręczne konfigurowanie LibMan we wpisach w plikach manifestu</span><span class="sxs-lookup"><span data-stu-id="da61d-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="da61d-155">Wszystkie operacje LibMan w programie Visual Studio są oparte na zawartości manifestu LibMan katalog główny projektu (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="da61d-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="da61d-156">Możesz ręcznie edytować *libman.json* konfigurowania plików biblioteki dla projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="da61d-157">Visual Studio, przywraca raz wszystkie pliki biblioteki *libman.json* jest zapisywany.</span><span class="sxs-lookup"><span data-stu-id="da61d-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="da61d-158">Aby otworzyć *libman.json* do edycji, istnieją następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="da61d-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="da61d-159">Kliknij dwukrotnie *libman.json* w pliku **Eksploratora rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="da61d-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="da61d-160">Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz **zarządzanie bibliotekami po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="da61d-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="da61d-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="da61d-161">**&#8224;**</span></span>
* <span data-ttu-id="da61d-162">Wybierz **zarządzanie bibliotekami po stronie klienta** z programu Visual Studio **projektu** menu.</span><span class="sxs-lookup"><span data-stu-id="da61d-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="da61d-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="da61d-163">**&#8224;**</span></span>

<span data-ttu-id="da61d-164">**&#8224;** Jeśli *libman.json* plik jeszcze nie istnieje w katalogu głównym projektu, zostanie on utworzony za pomocą szablonu elementu domyślnej zawartości.</span><span class="sxs-lookup"><span data-stu-id="da61d-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="da61d-165">Visual Studio oferuje bogate JSON edycję pomocy technicznej, takie jak kolorowanie, formatowanie, IntelliSense i sprawdzanie poprawności schematu.</span><span class="sxs-lookup"><span data-stu-id="da61d-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="da61d-166">Schemat JSON manifestu LibMan znajduje się na [ http://json.schemastore.org/libman ](http://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="da61d-166">The LibMan manifest's JSON schema is found at [http://json.schemastore.org/libman](http://json.schemastore.org/libman).</span></span>

<span data-ttu-id="da61d-167">Za pomocą następującego pliku manifestu, LibMan pobiera pliki na konfiguracją zdefiniowaną w `libraries` właściwości.</span><span class="sxs-lookup"><span data-stu-id="da61d-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="da61d-168">Wyjaśnienie literałów obiektu zdefiniowany w ramach `libraries` poniżej:</span><span class="sxs-lookup"><span data-stu-id="da61d-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="da61d-169">Podzbiór [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierana z CDNJS dostawcy.</span><span class="sxs-lookup"><span data-stu-id="da61d-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="da61d-170">Podzbiór jest zdefiniowany w `files` właściwość&mdash;*jquery.min.js*, *jquery.js*, i *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="da61d-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="da61d-171">Pliki są umieszczane w projekcie *wwwroot/lib/jquery* folderu.</span><span class="sxs-lookup"><span data-stu-id="da61d-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="da61d-172">Całość [Bootstrap](https://getbootstrap.com/) wersji 4.1.3 jest pobierana i umieszczana w *wwwroot/lib/bootstrap* folderu.</span><span class="sxs-lookup"><span data-stu-id="da61d-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="da61d-173">Literał obiektu `provider` zastąpienia właściwości `defaultProvider` wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="da61d-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="da61d-174">LibMan pobiera ładowania plików od dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="da61d-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="da61d-175">Podzbiór [Lodash](https://lodash.com/) została zatwierdzona przez jednostkę zarządzającej w organizacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="da61d-176">*Lodash.js* i *lodash.min.js* pliki są pobierane z lokalnego systemu plików w *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="da61d-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="da61d-177">Pliki są kopiowane w projekcie *wwwroot/lib/lodash* folderu.</span><span class="sxs-lookup"><span data-stu-id="da61d-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="da61d-178">LibMan obsługuje tylko jedną wersję każdego biblioteki z każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="da61d-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="da61d-179">*Libman.json* pliku zakończy się niepowodzeniem podczas sprawdzania poprawności schematu zawiera dwie biblioteki o takiej samej nazwie biblioteki dla danego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="da61d-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="da61d-180">Przywróć pliki bibliotek</span><span class="sxs-lookup"><span data-stu-id="da61d-180">Restore library files</span></span>

<span data-ttu-id="da61d-181">Aby przywrócić pliki biblioteki z poziomu programu Visual Studio, musi być prawidłowym *libman.json* plik w folderze głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="da61d-182">Przywróconych plików są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="da61d-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="da61d-183">Pliki biblioteki mogą zostać przywrócone w projektach programu ASP.NET Core na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="da61d-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="da61d-184">Przywracanie plików podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="da61d-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="da61d-185">Przywróć pliki ręcznie</span><span class="sxs-lookup"><span data-stu-id="da61d-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="da61d-186">Przywracanie plików podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="da61d-186">Restore files during build</span></span>

<span data-ttu-id="da61d-187">LibMan można przywrócić pliki biblioteki zdefiniowane jako część procesu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="da61d-188">Domyślnie *przywracania na kompilacji* zachowania jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="da61d-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="da61d-189">Aby włączyć i testowanie zachowania przywracania w kompilacji:</span><span class="sxs-lookup"><span data-stu-id="da61d-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="da61d-190">Kliknij prawym przyciskiem myszy *libman.json* w **Eksploratora rozwiązań** i wybierz **włączyć Przywracanie biblioteki po stronie klienta, podczas kompilacji** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="da61d-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="da61d-191">Kliknij przycisk **tak** przycisku po wyświetleniu monitu można zainstalować pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="da61d-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="da61d-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) pakiet NuGet zostanie dodany do projektu:</span><span class="sxs-lookup"><span data-stu-id="da61d-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="da61d-193">Skompiluj projekt, aby potwierdzić, że występuje LibMan Przywracanie plików.</span><span class="sxs-lookup"><span data-stu-id="da61d-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="da61d-194">`Microsoft.Web.LibraryManager.Build` Pakietu wprowadza obiekt docelowy programu MSBuild, działającego LibMan podczas operacji tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="da61d-195">Przegląd **kompilacji** kanału **dane wyjściowe** okno Dziennik aktywności LibMan:</span><span class="sxs-lookup"><span data-stu-id="da61d-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="da61d-196">Po włączeniu zachowanie przywracania na kompilacji *libman.json* menu kontekstowe wyświetla **Wyłącz Przywracanie biblioteki po stronie klienta, podczas kompilacji** opcji.</span><span class="sxs-lookup"><span data-stu-id="da61d-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="da61d-197">Wybranie tej opcji spowoduje usunięcie `Microsoft.Web.LibraryManager.Build` pakietu odwołania z pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="da61d-198">W związku z tym biblioteki po stronie klienta nie są już przywracane na każdej kompilacji.</span><span class="sxs-lookup"><span data-stu-id="da61d-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="da61d-199">Bez względu na ustawienie przywracania w kompilacji, można przywrócić ręcznie w dowolnym momencie z *libman.json* menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="da61d-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="da61d-200">Aby uzyskać więcej informacji, zobacz [ręcznie przywrócić pliki](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="da61d-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="da61d-201">Przywróć pliki ręcznie</span><span class="sxs-lookup"><span data-stu-id="da61d-201">Restore files manually</span></span>

<span data-ttu-id="da61d-202">Aby ręcznie przywrócić pliki biblioteki:</span><span class="sxs-lookup"><span data-stu-id="da61d-202">To manually restore library files:</span></span>

* <span data-ttu-id="da61d-203">Dla wszystkich projektów w rozwiązaniu:</span><span class="sxs-lookup"><span data-stu-id="da61d-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="da61d-204">Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksploratora rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="da61d-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="da61d-205">Wybierz **przywrócenia biblioteki po stronie klienta** opcji.</span><span class="sxs-lookup"><span data-stu-id="da61d-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="da61d-206">Dla określonego projektu:</span><span class="sxs-lookup"><span data-stu-id="da61d-206">For a specific project:</span></span>
  * <span data-ttu-id="da61d-207">Kliknij prawym przyciskiem myszy *libman.json* w pliku **Eksploratora rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="da61d-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="da61d-208">Wybierz **przywrócenia biblioteki po stronie klienta** opcji.</span><span class="sxs-lookup"><span data-stu-id="da61d-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="da61d-209">Operacja przywracania jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="da61d-209">While the restore operation is running:</span></span>

* <span data-ttu-id="da61d-210">Ikona Centrum stanu zadań (TSC) na pasku stanu programu Visual Studio będzie animowana i odczyta *uruchomiona operacja przywracania*.</span><span class="sxs-lookup"><span data-stu-id="da61d-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="da61d-211">Klikając ikonę Otwiera listę zadań w tle znanych etykietka narzędzia.</span><span class="sxs-lookup"><span data-stu-id="da61d-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="da61d-212">Wiadomości zostaną wysłane do paska stanu i **Library Manager** kanału **dane wyjściowe** okna.</span><span class="sxs-lookup"><span data-stu-id="da61d-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="da61d-213">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="da61d-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="da61d-214">Usuń pliki bibliotek</span><span class="sxs-lookup"><span data-stu-id="da61d-214">Delete library files</span></span>

<span data-ttu-id="da61d-215">Aby wykonać *czyste* operacji, który usuwa pliki biblioteki wcześniej przywrócona w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="da61d-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="da61d-216">Kliknij prawym przyciskiem myszy *libman.json* w pliku **Eksploratora rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="da61d-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="da61d-217">Wybierz **czystej biblioteki po stronie klienta** opcji.</span><span class="sxs-lookup"><span data-stu-id="da61d-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="da61d-218">Aby zapobiec przypadkowemu usunięciu plików nienależącym do biblioteki, operacji czyszczenia nie powoduje usunięcia całej katalogów.</span><span class="sxs-lookup"><span data-stu-id="da61d-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="da61d-219">Powoduje usunięcie tylko pliki, które występowały w poprzednich przywracania.</span><span class="sxs-lookup"><span data-stu-id="da61d-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="da61d-220">Po uruchomieniu operacji czyszczenia:</span><span class="sxs-lookup"><span data-stu-id="da61d-220">While the clean operation is running:</span></span>

* <span data-ttu-id="da61d-221">Ikona TSC na pasku stanu programu Visual Studio będzie animowana i odczyta *rozpoczęcia operacji biblioteki klienta*.</span><span class="sxs-lookup"><span data-stu-id="da61d-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="da61d-222">Klikając ikonę Otwiera listę zadań w tle znanych etykietka narzędzia.</span><span class="sxs-lookup"><span data-stu-id="da61d-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="da61d-223">Komunikaty są wysyłane do paska stanu i **Library Manager** kanału **dane wyjściowe** okna.</span><span class="sxs-lookup"><span data-stu-id="da61d-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="da61d-224">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="da61d-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="da61d-225">Operacji czyszczenia usuwa tylko pliki z projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="da61d-226">Pliki biblioteki pozostać w pamięci podręcznej szybsze pobieranie na operacje przywracania w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="da61d-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="da61d-227">Aby zarządzać plikami biblioteki przechowywanych w pamięci podręcznej na komputerze lokalnym, użyj [LibMan interfejsu wiersza polecenia](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="da61d-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="da61d-228">Odinstaluj pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="da61d-228">Uninstall library files</span></span>

<span data-ttu-id="da61d-229">Aby odinstalować plików biblioteki:</span><span class="sxs-lookup"><span data-stu-id="da61d-229">To uninstall library files:</span></span>

* <span data-ttu-id="da61d-230">Otwórz *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="da61d-230">Open *libman.json*.</span></span>
* <span data-ttu-id="da61d-231">Położenie karetki wewnątrz odpowiadającego `libraries` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="da61d-232">Kliknij ikonę żarówki, która pojawia się na lewym marginesie, a następnie wybierz pozycję **Odinstaluj \<library_name > @\<library_version >**:</span><span class="sxs-lookup"><span data-stu-id="da61d-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Odinstaluj menu kontekstowego biblioteki](_static/uninstall-menu-option.png)

<span data-ttu-id="da61d-234">Można też ręcznie edytować i zapisać manifestu LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="da61d-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="da61d-235">[Operacja przywracania](#restore-library-files) jest uruchamiany, gdy plik jest zapisywany.</span><span class="sxs-lookup"><span data-stu-id="da61d-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="da61d-236">Pliki bibliotek, które są już zdefiniowane w *libman.json* zostaną usunięte z projektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="da61d-237">Zaktualizuj wersję biblioteki</span><span class="sxs-lookup"><span data-stu-id="da61d-237">Update library version</span></span>

<span data-ttu-id="da61d-238">Aby sprawdzić wersję zaktualizowanej biblioteki:</span><span class="sxs-lookup"><span data-stu-id="da61d-238">To check for an updated library version:</span></span>

* <span data-ttu-id="da61d-239">Otwórz *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="da61d-239">Open *libman.json*.</span></span>
* <span data-ttu-id="da61d-240">Położenie karetki wewnątrz odpowiadającego `libraries` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="da61d-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="da61d-241">Kliknij ikonę żarówki, która pojawia się na lewym marginesie.</span><span class="sxs-lookup"><span data-stu-id="da61d-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="da61d-242">Umieść kursor nad **sprawdzać dostępność aktualizacji**.</span><span class="sxs-lookup"><span data-stu-id="da61d-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="da61d-243">LibMan sprawdza, czy biblioteka nowsza wersja niż zainstalowana wersja.</span><span class="sxs-lookup"><span data-stu-id="da61d-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="da61d-244">Mogą wystąpić następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="da61d-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="da61d-245">A **nie odnaleziono żadnych aktualizacji** zostanie wyświetlony komunikat, jeśli jest już zainstalowana najnowsza wersja.</span><span class="sxs-lookup"><span data-stu-id="da61d-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="da61d-246">Najnowsza stabilna wersja jest wyświetlana, jeśli nie jeszcze zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="da61d-246">The latest stable version is displayed if not already installed.</span></span>

  ![Sprawdź, czy są aktualizacje menu kontekstowego](_static/update-menu-option.png)

* <span data-ttu-id="da61d-248">Jeśli wstępna jest nowsza niż zainstalowana wersja jest dostępna, jest wyświetlany w wersji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="da61d-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="da61d-249">Zmiany na starszą starszej wersji biblioteki, należy ręcznie zmodyfikować *libman.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="da61d-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="da61d-250">Gdy plik jest zapisywany, LibMan [operacja przywracania](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="da61d-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="da61d-251">Usuwa zbędne pliki z poprzedniej wersji.</span><span class="sxs-lookup"><span data-stu-id="da61d-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="da61d-252">Dodaje nowe i zaktualizowane pliki z nowej wersji.</span><span class="sxs-lookup"><span data-stu-id="da61d-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da61d-253">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="da61d-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="da61d-254">Repozytorium LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="da61d-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)