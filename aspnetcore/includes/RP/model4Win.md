<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a><span data-ttu-id="82040-101">Tworzenie szkieletu modelu Movie</span><span class="sxs-lookup"><span data-stu-id="82040-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="82040-102">Uruchom następujące polecenie w wierszu polecenia (w katalogu projektu, który zawiera *Program.cs*, *Startup.cs*, i *.csproj* plików):</span><span class="sxs-lookup"><span data-stu-id="82040-102">Run the following from the command line (in the project directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files):</span></span>

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="82040-103">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="82040-103">If you get the error:</span></span>

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="82040-104">Poprzedni błąd występuje, gdy jesteś w niewłaściwej katalogu.</span><span class="sxs-lookup"><span data-stu-id="82040-104">The preceding error happens when you are in the wrong directory.</span></span> <span data-ttu-id="82040-105">Otwórz powłokę wiersza polecenia do katalogu projektu (katalog, który zawiera *Program.cs*, *Startup.cs*, i *.csproj* plików), a następnie uruchom poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="82040-105">Open a command shell to the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files), and then run the preceding command.</span></span>

<span data-ttu-id="82040-106">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="82040-106">If you get the error:</span></span>

  ```
  The process cannot access the file
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll'
  because it is being used by another process.
  ```

<span data-ttu-id="82040-107">Zamknij program Visual Studio i ponownie uruchom polecenie.</span><span class="sxs-lookup"><span data-stu-id="82040-107">Exit Visual Studio and run the command again.</span></span>
