---
title: Tworzenie interfejsów API sieci web za pomocą platformy ASP.NET Core i bazy danych MongoDB
author: prkhandelwal
description: W tym samouczku przedstawiono sposób tworzenia sieci web platformy ASP.NET Core interfejsu API przy użyciu bazy danych NoSQL bazy danych MongoDB.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 95a5f8bdb4b302d6bdae7b5809b54f1b263e6ee4
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012867"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="e8ad7-103">Tworzenie internetowego interfejsu API za pomocą platformy ASP.NET Core i usługi MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="e8ad7-104">Przez [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e8ad7-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e8ad7-105">Ten samouczek tworzy internetowego interfejsu API, która wykonuje operacje tworzenia, odczytu, aktualizowania lub usuwania (CRUD) [bazy danych MongoDB](https://www.mongodb.com/what-is-mongodb) bazy danych NoSQL.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="e8ad7-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e8ad7-107">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-107">Configure MongoDB</span></span>
> * <span data-ttu-id="e8ad7-108">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="e8ad7-109">Definiowanie kolekcji usługi MongoDB i schematu</span><span class="sxs-lookup"><span data-stu-id="e8ad7-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="e8ad7-110">Wykonywanie operacji CRUD bazy danych MongoDB z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="e8ad7-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="e8ad7-111">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([sposobu pobierania](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8ad7-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e8ad7-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="e8ad7-112">Prerequisites</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="e8ad7-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8ad7-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="e8ad7-114">.NET core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="e8ad7-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="e8ad7-115">[Visual Studio 2017 w wersji 15.9 lub nowszej](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) z **ASP.NET i tworzenie aplikacji internetowych** obciążenia</span><span class="sxs-lookup"><span data-stu-id="e8ad7-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="e8ad7-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# [<a name="visual-studio-code"></a><span data-ttu-id="e8ad7-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8ad7-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="e8ad7-118">.NET core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="e8ad7-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="e8ad7-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8ad7-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="e8ad7-120">C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8ad7-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="e8ad7-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="e8ad7-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e8ad7-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="e8ad7-123">.NET core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="e8ad7-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="e8ad7-124">Program Visual Studio dla komputerów Mac w wersji 7,7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="e8ad7-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="e8ad7-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="e8ad7-126">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="e8ad7-126">Configure MongoDB</span></span>

<span data-ttu-id="e8ad7-127">Jeśli przy użyciu Windows, bazy danych MongoDB jest zainstalowana w *C:\\Program Files\\bazy danych MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="e8ad7-128">Dodaj *C:\\Program Files\\bazy danych MongoDB\\serwera\\\<numer_wersji >\\bin* do `Path` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="e8ad7-129">Dzięki tej zmianie bazy danych MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="e8ad7-130">Użyj powłoki mongo w poniższych krokach umożliwia tworzenie bazy danych, wprowadzanie kolekcje i przechowywanie dokumentów.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="e8ad7-131">Aby uzyskać więcej informacji na temat poleceń powłoki mongo, zobacz [Praca z powłoki mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="e8ad7-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="e8ad7-132">Wybierz katalog na komputerze deweloperskim do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="e8ad7-133">Na przykład *C:\\BooksData* na Windows.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="e8ad7-134">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="e8ad7-135">Powłoki mongo nie tworzyć nowe katalogi.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="e8ad7-136">Otwórz powłokę wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-136">Open a command shell.</span></span> <span data-ttu-id="e8ad7-137">Uruchom następujące polecenie, aby nawiązać połączenie z bazą danych MongoDB na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="e8ad7-138">Pamiętaj, aby zastąpić `<data_directory_path>` z katalogu, który został wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="e8ad7-139">Otwórz inne wystąpienie powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-139">Open another command shell instance.</span></span> <span data-ttu-id="e8ad7-140">Połączenia z bazą danych testu domyślną, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="e8ad7-141">Uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="e8ad7-142">Jeśli jeszcze nie istnieje, bazę danych o nazwie *BookstoreDb* zostanie utworzony.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="e8ad7-143">Jeśli baza danych istnieje, jego połączenie jest otwarte dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="e8ad7-144">Utwórz `Books` kolekcji za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="e8ad7-145">Wyświetlane są następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="e8ad7-146">Zdefiniować schemat `Books` kolekcji i Wstaw dwa dokumenty, używając następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="e8ad7-147">Wyświetlane są następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="e8ad7-148">Wyświetl dokumenty w bazie danych, używając następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="e8ad7-149">Wyświetlane są następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-149">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="e8ad7-150">Schemat dodaje wygenerowany automatycznie `_id` właściwości typu `ObjectId` dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="e8ad7-151">Baza danych jest gotowy.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-151">The database is ready.</span></span> <span data-ttu-id="e8ad7-152">Możesz rozpocząć tworzenie interfejsu API sieci web platformy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="e8ad7-153">Utwórz projekt interfejsu API sieci web platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8ad7-153">Create the ASP.NET Core web API project</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="e8ad7-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8ad7-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e8ad7-155">Przejdź do **pliku** > **nowe** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="e8ad7-156">Wybierz **aplikacji sieci Web programu ASP.NET Core**, nadaj projektowi nazwę *BooksApi*i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="e8ad7-157">Wybierz **platformy .NET Core** platformy docelowej i **platformy ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-157">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="e8ad7-158">Wybierz **API** projektu szablonu, a następnie kliknij przycisk **OK**:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="e8ad7-159">Odwiedź stronę [galerii pakietów NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) do określenia najnowszej stabilnej wersji sterownika platformy .NET dla bazy danych MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-159">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="e8ad7-160">W **Konsola Menedżera pakietów** okna, przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-160">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="e8ad7-161">Uruchom następujące polecenie, aby zainstalować sterownik platformy .NET dla bazy danych MongoDB:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-161">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# [<a name="visual-studio-code"></a><span data-ttu-id="e8ad7-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8ad7-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e8ad7-163">Uruchom następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-163">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="e8ad7-164">Nowy projekt interfejsu API sieci web platformy ASP.NET Core, na przeznaczonych dla platformy .NET Core jest wygenerowany i otworzyć w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-164">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="e8ad7-165">Kliknij przycisk **tak** podczas *"BooksApi" brakuje wymagane zasoby do tworzenia i debugowania. Dodaj je?*  zostanie wyświetlone powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-165">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="e8ad7-166">Odwiedź stronę [galerii pakietów NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) do określenia najnowszej stabilnej wersji sterownika platformy .NET dla bazy danych MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-166">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="e8ad7-167">Otwórz **zintegrowany Terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-167">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="e8ad7-168">Uruchom następujące polecenie, aby zainstalować sterownik platformy .NET dla bazy danych MongoDB:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-168">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="e8ad7-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e8ad7-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e8ad7-170">Przejdź do **pliku** > **nowe rozwiązanie** > **platformy .NET Core** > **aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-170">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="e8ad7-171">Wybierz **internetowego interfejsu API platformy ASP.NET Core** C# projektu szablonu, a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-171">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="e8ad7-172">Wybierz **platformy .NET Core 2.2** z **platformę docelową** listy rozwijanej, a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-172">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="e8ad7-173">Wprowadź *BooksApi* dla **Nazwa projektu**i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-173">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="e8ad7-174">W **rozwiązania** konsoli kliknij prawym przyciskiem myszy projekt **zależności** a następnie wybierz węzeł **Dodawanie pakietów**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-174">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="e8ad7-175">Wprowadź *MongoDB.Driver* w polu wyszukiwania, wybierz *MongoDB.Driver* pakietu, a następnie kliknij przycisk **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-175">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="e8ad7-176">Kliknij przycisk **Akceptuj** znajdujący się w **akceptacja licencji** okna dialogowego.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-176">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="e8ad7-177">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="e8ad7-177">Add a model</span></span>

1. <span data-ttu-id="e8ad7-178">Dodaj *modeli* katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-178">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="e8ad7-179">Dodaj `Book` klasy *modeli* katalogu z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-179">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="e8ad7-180">W klasie poprzedniego `Id` właściwości:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-180">In the preceding class, the `Id` property:</span></span>

* <span data-ttu-id="e8ad7-181">Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-181">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
* <span data-ttu-id="e8ad7-182">Jest oznaczony za pomocą `[BsonId]` do wyznaczenia tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-182">Is annotated with `[BsonId]` to designate this property as the document's primary key.</span></span>
* <span data-ttu-id="e8ad7-183">Jest oznaczony za pomocą `[BsonRepresentation(BsonType.ObjectId)]` umożliwia przekazanie parametru jako typu `string` zamiast `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-183">Is annotated with `[BsonRepresentation(BsonType.ObjectId)]` to allow passing the parameter as type `string` instead of `ObjectId`.</span></span> <span data-ttu-id="e8ad7-184">MONGO obsługuje konwersję z `string` do `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-184">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

<span data-ttu-id="e8ad7-185">Inne właściwości w klasie jest oznaczony za pomocą `[BsonElement]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-185">Other properties in the class are annotated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="e8ad7-186">Wartość ten atrybut reprezentuje nazwę właściwości w kolekcji usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-186">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="e8ad7-187">Dodaj klasę operacje CRUD</span><span class="sxs-lookup"><span data-stu-id="e8ad7-187">Add a CRUD operations class</span></span>

1. <span data-ttu-id="e8ad7-188">Dodaj *usług* katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-188">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="e8ad7-189">Dodaj `BookService` klasy *usług* katalogu z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-189">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="e8ad7-190">Dodaj parametry połączenia bazy danych MongoDB do *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-190">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="e8ad7-191">Poprzedni `BookstoreDb` dostęp do właściwości w `BookService` konstruktora klasy.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-191">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="e8ad7-192">W `Startup.ConfigureServices`, zarejestruj `BookService` klasy przy użyciu systemu wstrzykiwanie zależności:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-192">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="e8ad7-193">Poprzedni rejestracji usługi jest niezbędne do obsługi iniekcji konstruktora w korzystających z tych klas.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-193">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="e8ad7-194">`BookService` Klasa używa następujących `MongoDB.Driver` elementy członkowskie do wykonywania operacji CRUD w odniesieniu do bazy danych:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-194">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* `MongoClient` <span data-ttu-id="e8ad7-195">&ndash; Odczytuje wystąpienie serwera do wykonywania operacji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-195">&ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="e8ad7-196">Konstruktor obiektu tej klasy znajduje się ciąg połączenia bazy danych MongoDB:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-196">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` <span data-ttu-id="e8ad7-197">&ndash; Reprezentuje bazą danych Mongo do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-197">&ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="e8ad7-198">W tym samouczku korzysta z ogólnego `GetCollection<T>(collection)` metoda w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-198">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="e8ad7-199">Po ta metoda jest wywoływana, można wykonać operacji CRUD względem kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-199">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="e8ad7-200">W `GetCollection<T>(collection)` wywołanie metody:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-200">In the `GetCollection<T>(collection)` method call:</span></span>
  * `collection` <span data-ttu-id="e8ad7-201">reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-201">represents the collection name.</span></span>
  * `T` <span data-ttu-id="e8ad7-202">reprezentuje typ obiektu CLR, które są przechowywane w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-202">represents the CLR object type stored in the collection.</span></span>

`GetCollection<T>(collection)` <span data-ttu-id="e8ad7-203">Zwraca `MongoCollection` obiekt reprezentujący kolekcję.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-203">returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="e8ad7-204">W tym samouczku następujące metody są wywoływane w kolekcji:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-204">In this tutorial, the following methods are invoked on the collection:</span></span>

* `Find<T>` <span data-ttu-id="e8ad7-205">&ndash; Zwraca wszystkie dokumenty w kolekcji, zgodne z kryteriami wyszukiwania podana.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-205">&ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* `InsertOne` <span data-ttu-id="e8ad7-206">&ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-206">&ndash; Inserts the provided object as a new document in the collection.</span></span>
* `ReplaceOne` <span data-ttu-id="e8ad7-207">&ndash; Zastępuje jednolitego dokumentu, które są zgodne z kryteriami wyszukiwania podanej za pomocą udostępnionego obiektu.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-207">&ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* `DeleteOne` <span data-ttu-id="e8ad7-208">&ndash; Usuwa pojedynczy dokument zgodne z kryteriami wyszukiwania podana.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-208">&ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e8ad7-209">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="e8ad7-209">Add a controller</span></span>

1. <span data-ttu-id="e8ad7-210">Dodaj `BooksController` klasy *kontrolerów* katalogu z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-210">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="e8ad7-211">Poprzedni kontroler internetowego interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-211">The preceding web API controller:</span></span>

    * <span data-ttu-id="e8ad7-212">Używa `BookService` klasy w celu wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-212">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="e8ad7-213">Zawiera metody akcji w celu obsługi żądań GET, POST, PUT i DELETE protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-213">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
    * <span data-ttu-id="e8ad7-214"><xref:System.Web.Http.ApiController.CreatedAtRoute*> Metoda zwraca odpowiedź 201, która jest standardowa odpowiedź na metodę POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-214">The <xref:System.Web.Http.ApiController.CreatedAtRoute*> method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> `CreatedAtRoute` <span data-ttu-id="e8ad7-215">również dodaje do odpowiedzi nagłówek lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-215">also adds a Location header to the response.</span></span> <span data-ttu-id="e8ad7-216">Nagłówek Location określa identyfikator URI nowo utworzonego zadania do wykonania.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-216">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e8ad7-217">Zobacz [10.2.2 201 utworzone](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e8ad7-217">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
1. <span data-ttu-id="e8ad7-218">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-218">Build and run the app.</span></span>
1. <span data-ttu-id="e8ad7-219">Przejdź do `http://localhost:<port>/api/books` w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="e8ad7-219">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="e8ad7-220">Wyświetlane są następujące odpowiedź w formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-220">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

## <a name="next-steps"></a><span data-ttu-id="e8ad7-221">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="e8ad7-221">Next steps</span></span>

<span data-ttu-id="e8ad7-222">Aby uzyskać więcej informacji dotyczących tworzenia interfejsów API sieci web platformy ASP.NET Core zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="e8ad7-222">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="e8ad7-223">Youtube wersję tego artykułu</span><span class="sxs-lookup"><span data-stu-id="e8ad7-223">Youtube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
