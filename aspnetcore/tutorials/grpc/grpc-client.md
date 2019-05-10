---
title: 'Samouczek: Tworzenie klienta gRPC platformy .NET Core'
author: juntaoluo
description: Tej serii samouczków pokazano, jak utworzyć gRPC usługi programu ASP.NET Core. Dowiedz się, jak utworzyć projekt usługi gRPC, Edytuj plik proto i dodać dupleks, przesyłanie strumieniowe wywołania.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212637"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="1c80f-104">Samouczek: Tworzenie klienta gRPC platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c80f-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="1c80f-105">Przez [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="1c80f-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="1c80f-106">W tym samouczku przedstawiono sposób tworzenia platformy .NET Core [gRPC](https://grpc.io/docs/guides/) klienta, który może komunikować się z usługami gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c80f-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="1c80f-107">Po zakończeniu będziesz mieć klienta gRPC, który komunikuje się z gRPC Greeter usługi.</span><span class="sxs-lookup"><span data-stu-id="1c80f-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="1c80f-108">W ramach tego samouczka możesz:</span><span class="sxs-lookup"><span data-stu-id="1c80f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1c80f-109">Tworzenie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c80f-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="1c80f-110">Uruchom usługę względem gRPC usługi Greeter utworzony w poprzednim samouczku.</span><span class="sxs-lookup"><span data-stu-id="1c80f-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="1c80f-111">Sprawdź pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="1c80f-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="1c80f-112">Utwórz aplikację konsolową .NET</span><span class="sxs-lookup"><span data-stu-id="1c80f-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c80f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c80f-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c80f-114">Postępuj zgodnie z instrukcjami [tutaj](/dotnet/core/tutorials/with-visual-studio) do tworzenia aplikacji konsolowej o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="1c80f-114">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c80f-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c80f-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c80f-116">Postępuj zgodnie z instrukcjami [tutaj](/dotnet/core/tutorials/with-visual-studio-code) do tworzenia aplikacji konsolowej o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="1c80f-116">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c80f-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c80f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1c80f-118">Postępuj zgodnie z instrukcjami [tutaj](/dotnet/core/tutorials/using-on-mac-vs-full-solution) do tworzenia aplikacji konsolowej o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="1c80f-118">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="1c80f-119">Dodawanie wymaganych pakietów</span><span class="sxs-lookup"><span data-stu-id="1c80f-119">Add required packages</span></span>

<span data-ttu-id="1c80f-120">GRPC projekt klienta, należy dodać następujące pakiety:</span><span class="sxs-lookup"><span data-stu-id="1c80f-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="1c80f-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), który zawiera C# interfejsu API klienta C-core.</span><span class="sxs-lookup"><span data-stu-id="1c80f-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="1c80f-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera protobuf komunikatu interfejsów API na potrzeby C#.</span><span class="sxs-lookup"><span data-stu-id="1c80f-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="1c80f-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), który zawiera C# narzędzia do obsługi formatu protobuf plików.</span><span class="sxs-lookup"><span data-stu-id="1c80f-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="1c80f-124">Pakiet narzędzi nie jest wymagane w czasie wykonywania, więc zależność jest oznaczona za pomocą `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="1c80f-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="1c80f-125">Pakiety mogą być dodawane przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1c80f-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c80f-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c80f-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="1c80f-127">Konsolę zarządzania Pakietami opcję, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="1c80f-127">PMC option to install packages</span></span>

* <span data-ttu-id="1c80f-128">W programie Visual Studio, wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="1c80f-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="1c80f-129">Z **Konsola Menedżera pakietów** okna, przejdź do katalogu, w którym *GrpcGreeterClient.csproj* plik istnieje.</span><span class="sxs-lookup"><span data-stu-id="1c80f-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="1c80f-130">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1c80f-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="1c80f-131">Powtórz `Install-Package` Google.Protobuf i Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="1c80f-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="1c80f-132">Zarządzanie pakietami NuGet opcję, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="1c80f-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="1c80f-133">Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** > **Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="1c80f-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="1c80f-134">Ustaw **źródła pakietu** na stronie "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1c80f-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="1c80f-135">W polu wyszukiwania wprowadź "Grpc.Core"</span><span class="sxs-lookup"><span data-stu-id="1c80f-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="1c80f-136">Wybierz pakiet "Grpc.Core" z **Przeglądaj** kartę, a następnie kliknij przycisk **instalacji**</span><span class="sxs-lookup"><span data-stu-id="1c80f-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="1c80f-137">Powtórz te czynności dla Google.Protobuf i Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="1c80f-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c80f-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c80f-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c80f-139">Uruchom następujące polecenie z **zintegrowany Terminal**:</span><span class="sxs-lookup"><span data-stu-id="1c80f-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

<span data-ttu-id="1c80f-140">Powtórz te czynności dla Google.Protobuf i Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="1c80f-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c80f-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c80f-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c80f-142">Kliknij prawym przyciskiem myszy *pakietów* folderu w **konsoli rozwiązania** > **Dodawanie pakietów...**</span><span class="sxs-lookup"><span data-stu-id="1c80f-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="1c80f-143">Ustaw **Dodawanie pakietów** okna **źródła** menu rozwijane "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1c80f-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="1c80f-144">W polu wyszukiwania wprowadź "Grpc.Core"</span><span class="sxs-lookup"><span data-stu-id="1c80f-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="1c80f-145">Wybierz pakiet "Grpc.Core" w okienku wyników, a następnie kliknij przycisk **Dodaj pakiet**</span><span class="sxs-lookup"><span data-stu-id="1c80f-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="1c80f-146">Powtórz te czynności dla Google.Protobuf i Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="1c80f-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="1c80f-147">Dodaj plik greet.proto</span><span class="sxs-lookup"><span data-stu-id="1c80f-147">Add the greet.proto file</span></span>

<span data-ttu-id="1c80f-148">Kopiuj **Protos\greet.proto** pliku z gRPC Greeter usługi do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c80f-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="1c80f-149">Dodaj **greet.proto** plik `<Protobuf>` grupy elementów GrpcGreeterClient pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="1c80f-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="1c80f-150">Plik projektu GrpcGreeterClient można otworzyć, klikając prawym przyciskiem myszy projekt i wybierając polecenie **Edytuj GrpcGreeterClient.csproj** opcję z menu rozwijanego.</span><span class="sxs-lookup"><span data-stu-id="1c80f-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![Nowa aplikacja internetowa ASP.NET Core](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="1c80f-152">Alternatywnie, przejdź do katalogu GrpcGreeterClient i edytować `GrpcGreeterClient.csproj` za pomocą ulubionego edytora.</span><span class="sxs-lookup"><span data-stu-id="1c80f-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="1c80f-153">`GrpcServices="Client"` Jest dodawany, aby tylko atrybut C# zasobów klienta są generowane dla pliku protobuf uwzględnione.</span><span class="sxs-lookup"><span data-stu-id="1c80f-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="1c80f-154">Skompiluj projekt klienta, aby wyzwolić Generowanie C# zasobów klienta.</span><span class="sxs-lookup"><span data-stu-id="1c80f-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="1c80f-155">Tworzenie GreeterClient i wywoływanie SayHello wywołanie jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="1c80f-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="1c80f-156">Dodaj następujący kod do `Main` metody `Program.cs` pliku projektu klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="1c80f-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="1c80f-157">Dostęp do wymaganych typów używając następujących instrukcji są wymagane do:</span><span class="sxs-lookup"><span data-stu-id="1c80f-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="1c80f-158">GreeterClient jest tworzony przez utworzenie wystąpienia `Channel` zawierających informacje dotyczące tworzenia połączenia z usługą gRPC i używania go do konstruowania `GreeterClient`:</span><span class="sxs-lookup"><span data-stu-id="1c80f-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="1c80f-159">GreeterClient zawiera wywołanie jednoargumentowe `SayHello` które mogą być wywoływane asynchronicznie:</span><span class="sxs-lookup"><span data-stu-id="1c80f-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="1c80f-160">Wyniki `SayHello` wywołania są przechowywane w `reply` które następnie mogą być wyświetlane:</span><span class="sxs-lookup"><span data-stu-id="1c80f-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="1c80f-161">`Channel` Używany przez klienta należy zamknąć po zakończeniu operacji zwolnić wszystkie zasoby:</span><span class="sxs-lookup"><span data-stu-id="1c80f-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="1c80f-162">Będą potrzebne do tworzenia projektu przed typów w **Greeter** przestrzeni nazw mogą być rozwiązane.</span><span class="sxs-lookup"><span data-stu-id="1c80f-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="1c80f-163">Te typy są generowane automatycznie podczas kompilacji i nie być dostępne przed uruchomieniem kompilacji.</span><span class="sxs-lookup"><span data-stu-id="1c80f-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="1c80f-164">Testowanie klienta gRPC gRPC Greeter usługi</span><span class="sxs-lookup"><span data-stu-id="1c80f-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c80f-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c80f-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c80f-166">Upewnij się, Greeter utworzony w poprzednim samouczku jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="1c80f-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="1c80f-167">Gdy usługa jest uruchomiona, wróć do **GrpcGreeterClient** projektu jest ustawiony jako projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="1c80f-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="1c80f-168">Naciśnij klawisze Ctrl + F5, aby uruchomić klienta bez debugera.</span><span class="sxs-lookup"><span data-stu-id="1c80f-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="1c80f-169">Klient wysyła pozdrowienia z usługą za pomocą komunikatu zawierającego jego nazwę "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="1c80f-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="1c80f-170">Usługa wyśle komunikat "Hello GreeterClient" jako odpowiedzi, który jest wyświetlany w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c80f-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![Nowa aplikacja internetowa ASP.NET Core](grpc-start/_static/client.png)

  <span data-ttu-id="1c80f-172">Usługa rejestruje szczegółowe informacje o pomyślnym wywołaniem w dziennikach, zapisywany wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c80f-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![Nowa aplikacja internetowa ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1c80f-174">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c80f-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1c80f-175">Upewnij się, Greeter utworzony w poprzednim samouczku jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="1c80f-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="1c80f-176">Uruchom projekt klienta, GrpcGreeter.Client w osobnym wierszu polecenia przy użyciu `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1c80f-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="1c80f-177">Klient wysyła pozdrowienia z usługą za pomocą komunikatu zawierającego jego nazwę "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="1c80f-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="1c80f-178">Usługa wyśle komunikat "Hello GreeterClient" jako odpowiedzi, który jest wyświetlany w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c80f-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="1c80f-179">Usługa rejestruje szczegółowe informacje o pomyślnym wywołaniem w dziennikach, zapisywany wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c80f-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="1c80f-180">Przejrzyj pliki projektu gRPC projektu</span><span class="sxs-lookup"><span data-stu-id="1c80f-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="1c80f-181">gRPC client GrpcGreeterClient file:</span><span class="sxs-lookup"><span data-stu-id="1c80f-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="1c80f-182">*Plik program.cs* zawiera punktu wejścia, a logika gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="1c80f-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c80f-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1c80f-183">Additional resources</span></span>

<span data-ttu-id="1c80f-184">W ramach tego samouczka możesz:</span><span class="sxs-lookup"><span data-stu-id="1c80f-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1c80f-185">Tworzenie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c80f-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="1c80f-186">Uruchom usługę względem gRPC usługi Greeter utworzony w poprzednim samouczku.</span><span class="sxs-lookup"><span data-stu-id="1c80f-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="1c80f-187">Sprawdź pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="1c80f-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1c80f-188">Poprzednie: Utwórz gRPC Greeter usługi</span><span class="sxs-lookup"><span data-stu-id="1c80f-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
