---
title: Zarządzanie stanem Blazor ASP.NET Core
author: guardrex
description: Dowiedz się, jak utrwalać stan w aplikacjach Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 990d392b0e1658774256626eb277701e40287b79
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657711"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="4dcff-103">Zarządzanie stanem Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dcff-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="4dcff-104">[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="4dcff-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4dcff-105">Serwer Blazor jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="4dcff-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="4dcff-106">W większości przypadków aplikacja utrzymuje ciągłe połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="4dcff-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="4dcff-107">Stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="4dcff-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="4dcff-108">Przykłady stanu przechowywanego dla obwodu użytkownika obejmują:</span><span class="sxs-lookup"><span data-stu-id="4dcff-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="4dcff-109">Renderowany interfejs użytkownika&mdash;hierarchii wystąpień składników i ich najnowszych danych wyjściowych renderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="4dcff-110">Wartości wszystkich pól i właściwości w wystąpieniach składników.</span><span class="sxs-lookup"><span data-stu-id="4dcff-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="4dcff-111">Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="4dcff-112">Ten artykuł rozwiązuje trwałość stanu w aplikacjach Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4dcff-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="4dcff-113"> aplikacje webassembly mogą korzystać z [trwałości stanu po stronie klienta w przeglądarce,](#client-side-in-the-browser) ale wymagają niestandardowych rozwiązań lub pakietów innych firm wykraczających poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a><span data-ttu-id="4dcff-114">obwody Blazor</span><span class="sxs-lookup"><span data-stu-id="4dcff-114">Blazor circuits</span></span>

<span data-ttu-id="4dcff-115">Jeśli użytkownik napotyka chwilową utratę połączenia sieciowego, Blazor próbuje ponownie połączyć użytkownika z oryginalnym obwodem, aby mogli nadal korzystać z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4dcff-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="4dcff-116">Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:</span><span class="sxs-lookup"><span data-stu-id="4dcff-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="4dcff-117">Serwer nie może całkowicie zachować rozłączonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="4dcff-118">Serwer musi zwolnić obwód rozłączony po upływie limitu czasu lub w przypadku, gdy na serwerze znajduje się pamięć.</span><span class="sxs-lookup"><span data-stu-id="4dcff-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="4dcff-119">W środowiskach wieloserwerowych ze zrównoważonym obciążeniem wszystkie żądania przetwarzania serwera mogą stać się niedostępne w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="4dcff-120">Poszczególne serwery mogą kończyć się niepowodzeniem lub być automatycznie usuwane, gdy nie są już wymagane do obsługi ogólnej liczby żądań.</span><span class="sxs-lookup"><span data-stu-id="4dcff-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="4dcff-121">Oryginalny serwer może nie być dostępny, gdy użytkownik próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="4dcff-122">Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co spowoduje usunięcie wszystkich stanów przechowywanych w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4dcff-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="4dcff-123">Na przykład wartości ustawione za poorednictwem wywołań międzyoperacyjnych języka JavaScript są tracone.</span><span class="sxs-lookup"><span data-stu-id="4dcff-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="4dcff-124">Gdy nie można ponownie nawiązać połączenia użytkownika z oryginalnym obwodem, użytkownik otrzymuje nowy obwód z pustym stanem.</span><span class="sxs-lookup"><span data-stu-id="4dcff-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="4dcff-125">Jest to równoznaczne z zamknięciem i ponownym otwarciem aplikacji klasycznej.</span><span class="sxs-lookup"><span data-stu-id="4dcff-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="4dcff-126">Zachowaj stan między obwodymi</span><span class="sxs-lookup"><span data-stu-id="4dcff-126">Preserve state across circuits</span></span>

<span data-ttu-id="4dcff-127">W niektórych scenariuszach jest wymagane zachowanie stanu między obwodami.</span><span class="sxs-lookup"><span data-stu-id="4dcff-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="4dcff-128">Aplikacja może zachować ważne dane dla użytkownika, jeśli:</span><span class="sxs-lookup"><span data-stu-id="4dcff-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="4dcff-129">Serwer sieci Web stał się niedostępny.</span><span class="sxs-lookup"><span data-stu-id="4dcff-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="4dcff-130">W przeglądarce użytkownika wymuszone jest rozpoczęcie nowego obwodu przy użyciu nowego serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4dcff-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="4dcff-131">Ogólnie rzecz biorąc, utrzymanie stanu między obwodami ma zastosowanie do scenariuszy, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="4dcff-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="4dcff-132">Aby zachować stan poza pojedynczym obwodem, *nie należy przechowywać danych w pamięci serwera*.</span><span class="sxs-lookup"><span data-stu-id="4dcff-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="4dcff-133">Aplikacja musi przechowywać dane w innej lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="4dcff-134">Trwałość stanu nie jest automatyczna&mdash;należy wykonać kroki podczas tworzenia aplikacji w celu zaimplementowania trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="4dcff-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="4dcff-135">Trwałość danych jest zwykle wymagana tylko w przypadku stanu wysokiego poziomu, który użytkownicy wystawią nakłady na tworzenie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="4dcff-136">W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:</span><span class="sxs-lookup"><span data-stu-id="4dcff-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="4dcff-137">Wieloetapowy formularz WebForm &ndash; czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków procesu wieloetapowego, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="4dcff-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="4dcff-138">Użytkownik utraci stan w tym scenariuszu, jeśli przejdą do formularza wieloetapowego i wrócisz do formularza później.</span><span class="sxs-lookup"><span data-stu-id="4dcff-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="4dcff-139">Koszyk &ndash; każdy istotny dla nich składnik aplikacji, który reprezentuje potencjalne przychody.</span><span class="sxs-lookup"><span data-stu-id="4dcff-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="4dcff-140">Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="4dcff-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="4dcff-141">Zwykle nie jest konieczne zachowywanie stanu łatwego ponownego tworzenia, takiego jak wprowadzona nazwa użytkownika do okna dialogowego logowania, które nie zostało przesłane.</span><span class="sxs-lookup"><span data-stu-id="4dcff-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4dcff-142">Aplikacja może utrzymywać tylko *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="4dcff-142">An app can only persist *app state*.</span></span> <span data-ttu-id="4dcff-143">Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="4dcff-144">Składniki i drzewa renderowania nie są generalnie serializowane.</span><span class="sxs-lookup"><span data-stu-id="4dcff-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="4dcff-145">Aby zachować coś podobnego do stanu interfejsu użytkownika, na przykład rozwinięte węzły drzewa TreeView, aplikacja musi mieć kod niestandardowy, aby modelować zachowanie jako możliwy do serializacji stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4dcff-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="4dcff-146">Gdzie będzie trwały stan</span><span class="sxs-lookup"><span data-stu-id="4dcff-146">Where to persist state</span></span>

<span data-ttu-id="4dcff-147">Trzy Popularne lokalizacje istnieją dla stanu utrwalania w aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4dcff-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="4dcff-148">Każde podejście jest najlepiej dostosowane do różnych scenariuszy i ma inne zastrzeżenia:</span><span class="sxs-lookup"><span data-stu-id="4dcff-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="4dcff-149">Po stronie serwera w bazie danych</span><span class="sxs-lookup"><span data-stu-id="4dcff-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="4dcff-150">Adres URL</span><span class="sxs-lookup"><span data-stu-id="4dcff-150">URL</span></span>](#url)
* [<span data-ttu-id="4dcff-151">Po stronie klienta w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="4dcff-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="4dcff-152">Po stronie serwera w bazie danych</span><span class="sxs-lookup"><span data-stu-id="4dcff-152">Server-side in a database</span></span>

<span data-ttu-id="4dcff-153">W przypadku trwałych trwałości danych lub wszelkich danych, które muszą obejmować wiele użytkowników lub urządzeń, niezależna baza danych po stronie serwera prawie najlepiej sprawdza się.</span><span class="sxs-lookup"><span data-stu-id="4dcff-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="4dcff-154">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="4dcff-154">Options include:</span></span>

* <span data-ttu-id="4dcff-155">Relacyjna baza danych SQL</span><span class="sxs-lookup"><span data-stu-id="4dcff-155">Relational SQL database</span></span>
* <span data-ttu-id="4dcff-156">Magazyn wartości klucza</span><span class="sxs-lookup"><span data-stu-id="4dcff-156">Key-value store</span></span>
* <span data-ttu-id="4dcff-157">Magazyn obiektów BLOB</span><span class="sxs-lookup"><span data-stu-id="4dcff-157">Blob store</span></span>
* <span data-ttu-id="4dcff-158">Magazyn tabel</span><span class="sxs-lookup"><span data-stu-id="4dcff-158">Table store</span></span>

<span data-ttu-id="4dcff-159">Po zapisaniu danych w bazie danych nowy obwód może być uruchamiany przez użytkownika w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="4dcff-160">Dane użytkownika są przechowywane i dostępne w dowolnym nowym obwodzie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="4dcff-161">Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz [dokumentację usługi Azure Storage](/azure/storage/) i [bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="4dcff-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="4dcff-162">Adres URL</span><span class="sxs-lookup"><span data-stu-id="4dcff-162">URL</span></span>

<span data-ttu-id="4dcff-163">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="4dcff-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="4dcff-164">Przykłady stanu modelu w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="4dcff-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="4dcff-165">Identyfikator wyświetlonej jednostki.</span><span class="sxs-lookup"><span data-stu-id="4dcff-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="4dcff-166">Numer bieżącej strony w siatce stronicowanej.</span><span class="sxs-lookup"><span data-stu-id="4dcff-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="4dcff-167">Zawartość paska adresu przeglądarki jest zachowywana:</span><span class="sxs-lookup"><span data-stu-id="4dcff-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="4dcff-168">Jeśli użytkownik ręcznie ponownie załaduje stronę.</span><span class="sxs-lookup"><span data-stu-id="4dcff-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="4dcff-169">Jeśli serwer sieci Web będzie niedostępny,&mdash;użytkownik musi ponownie załadować stronę, aby można było połączyć się z innym serwerem.</span><span class="sxs-lookup"><span data-stu-id="4dcff-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="4dcff-170">Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą dyrektywy `@page`, zobacz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="4dcff-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="4dcff-171">Po stronie klienta w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="4dcff-171">Client-side in the browser</span></span>

<span data-ttu-id="4dcff-172">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, wspólny magazyn zapasowy to `localStorage` i kolekcje `sessionStorage` przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4dcff-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="4dcff-173">Aplikacja nie jest wymagana do zarządzania lub czyszczenia stanu przechowywanego, jeśli obwód został porzucony, co jest korzystne w porównaniu z magazynem po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="4dcff-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="4dcff-174">"Po stronie klienta" w tej sekcji odwołuje się do scenariuszy po stronie klienta w przeglądarce, a nie [modelu hostinguBlazor webassembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="4dcff-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="4dcff-175">`localStorage` i `sessionStorage` mogą być używane w aplikacjach Blazor webassembly, ale tylko przez napisanie kodu niestandardowego lub użycie pakietu innej firmy.</span><span class="sxs-lookup"><span data-stu-id="4dcff-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="4dcff-176">`localStorage` i `sessionStorage` różnią się w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="4dcff-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="4dcff-177">`localStorage` jest objęty zakresem przeglądarki użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4dcff-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="4dcff-178">Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="4dcff-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="4dcff-179">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="4dcff-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="4dcff-180">Dane są zachowywane w `localStorage` do momentu jawnego wyczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="4dcff-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="4dcff-181">`sessionStorage` należy do zakresu karty przeglądarki użytkownika. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="4dcff-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="4dcff-182">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="4dcff-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="4dcff-183">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="4dcff-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="4dcff-184">Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie do użycia.</span><span class="sxs-lookup"><span data-stu-id="4dcff-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="4dcff-185">`sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="4dcff-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="4dcff-186">Błędy w magazynie Stanów na kartach.</span><span class="sxs-lookup"><span data-stu-id="4dcff-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="4dcff-187">Zachowanie mylące, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="4dcff-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="4dcff-188">`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4dcff-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="4dcff-189">Ostrzeżenia dotyczące korzystania z magazynu przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="4dcff-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="4dcff-190">Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="4dcff-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="4dcff-191">W przeciwieństwie do bazy danych po stronie serwera, magazyn nie jest dostępny podczas renderowania wstępnego, ponieważ żądana strona nie istnieje w przeglądarce na etapie renderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="4dcff-192">Przechowywanie kilku kilobajtów danych jest rozsądne dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="4dcff-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="4dcff-193">Po kilku kilobajtach należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.</span><span class="sxs-lookup"><span data-stu-id="4dcff-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="4dcff-194">Użytkownicy mogą wyświetlać i modyfikować dane.</span><span class="sxs-lookup"><span data-stu-id="4dcff-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="4dcff-195">[Ochrona danych](xref:security/data-protection/introduction) ASP.NET Core może ograniczyć ryzyko.</span><span class="sxs-lookup"><span data-stu-id="4dcff-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="4dcff-196">Rozwiązania do magazynowania przeglądarki innych firm</span><span class="sxs-lookup"><span data-stu-id="4dcff-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="4dcff-197">Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="4dcff-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="4dcff-198">Warto rozważać wybór pakietu, który w sposób przezroczysty używa ASP.NET Core [ochrony danych](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="4dcff-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4dcff-199">Ochrona danych ASP.NET Core szyfruje przechowywane dane i zmniejsza potencjalne ryzyko naruszenia przechowywanych danych.</span><span class="sxs-lookup"><span data-stu-id="4dcff-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="4dcff-200">Jeśli dane serializowane w formacie JSON są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane przy użyciu narzędzi deweloperskich przeglądarki, a także zmodyfikować przechowywane dane.</span><span class="sxs-lookup"><span data-stu-id="4dcff-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="4dcff-201">Zabezpieczanie danych nie zawsze jest problemem, ponieważ dane mogą być proste.</span><span class="sxs-lookup"><span data-stu-id="4dcff-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="4dcff-202">Na przykład odczytywanie lub modyfikowanie zapisanego koloru elementu interfejsu użytkownika nie jest istotnym zagrożeniem bezpieczeństwa użytkownika lub organizacji.</span><span class="sxs-lookup"><span data-stu-id="4dcff-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="4dcff-203">Unikaj zezwalania użytkownikom na inspekcję i manipulowanie *danymi poufnymi*.</span><span class="sxs-lookup"><span data-stu-id="4dcff-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="4dcff-204">Pakiet Eksperymentalny magazynu chronionej przeglądarki</span><span class="sxs-lookup"><span data-stu-id="4dcff-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="4dcff-205">Przykładem pakietu NuGet, który zapewnia [ochronę danych](xref:security/data-protection/introduction) dla `localStorage` i `sessionStorage` jest [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="4dcff-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="4dcff-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` jest nieobsługiwanym pakietem eksperymentalnym nieodpowiedninym do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="4dcff-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="4dcff-207">Instalacja</span><span class="sxs-lookup"><span data-stu-id="4dcff-207">Installation</span></span>

<span data-ttu-id="4dcff-208">Aby zainstalować pakiet `Microsoft.AspNetCore.ProtectedBrowserStorage`:</span><span class="sxs-lookup"><span data-stu-id="4dcff-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="4dcff-209">W projekcie aplikacji Blazor Server Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="4dcff-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="4dcff-210">W kodzie HTML najwyższego poziomu (na przykład w pliku *Pages/_Host. cshtml* w domyślnym szablonie projektu) Dodaj następujący tag `<script>`:</span><span class="sxs-lookup"><span data-stu-id="4dcff-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="4dcff-211">W metodzie `Startup.ConfigureServices` Wywołaj `AddProtectedBrowserStorage`, aby dodać usługi `localStorage` i `sessionStorage` do kolekcji usług:</span><span class="sxs-lookup"><span data-stu-id="4dcff-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="4dcff-212">Zapisz i Załaduj dane w składniku</span><span class="sxs-lookup"><span data-stu-id="4dcff-212">Save and load data within a component</span></span>

<span data-ttu-id="4dcff-213">W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) , aby wstrzyknąć wystąpienie jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="4dcff-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="4dcff-214">Wybór zależy od tego, który magazyn zapasowy ma być używany.</span><span class="sxs-lookup"><span data-stu-id="4dcff-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="4dcff-215">W poniższym przykładzie użyto `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="4dcff-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="4dcff-216">Instrukcję `@using` można umieścić w pliku *_Imports. Razor* zamiast w składniku.</span><span class="sxs-lookup"><span data-stu-id="4dcff-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="4dcff-217">Użycie pliku *_Imports. Razor* sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4dcff-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="4dcff-218">Aby zachować wartość `_currentCount` w składniku `Counter` szablonu projektu, należy zmodyfikować metodę `IncrementCount`, aby użyć `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="4dcff-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="4dcff-219">W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne.</span><span class="sxs-lookup"><span data-stu-id="4dcff-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="4dcff-220">Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany.</span><span class="sxs-lookup"><span data-stu-id="4dcff-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="4dcff-221">`ProtectedSessionStore` automatycznie serializować i deserializacji danych JSON.</span><span class="sxs-lookup"><span data-stu-id="4dcff-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="4dcff-222">W poprzednim przykładzie kodu `_currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4dcff-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="4dcff-223">Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu [ochrony danych](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4dcff-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4dcff-224">Zaszyfrowane dane można zobaczyć, jeśli `sessionStorage['count']` jest oceniane w konsoli dewelopera w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4dcff-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="4dcff-225">Aby odzyskać dane `_currentCount`, jeśli użytkownik powróci do składnika `Counter` później (w tym, jeśli znajdują się w całkowicie nowym obwodzie), użyj `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="4dcff-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="4dcff-226">Jeśli parametry składnika obejmują stan nawigacji, wywołaj `ProtectedSessionStore.GetAsync` i przypisz wynik do `OnParametersSetAsync`, a nie `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="4dcff-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="4dcff-227">`OnInitializedAsync` jest wywoływana tylko raz podczas pierwszego wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="4dcff-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="4dcff-228">`OnInitializedAsync` nie zostanie wywołane ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="4dcff-229">Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="4dcff-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="4dcff-230">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="4dcff-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="4dcff-231">Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd podobny do:</span><span class="sxs-lookup"><span data-stu-id="4dcff-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="4dcff-232">W tej chwili nie można wystawić wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4dcff-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="4dcff-233">Dzieje się tak, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="4dcff-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="4dcff-234">Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="4dcff-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="4dcff-235">Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4dcff-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="4dcff-236">Obsłuż stan ładowania</span><span class="sxs-lookup"><span data-stu-id="4dcff-236">Handle the loading state</span></span>

<span data-ttu-id="4dcff-237">Ponieważ magazyn przeglądarki jest asynchroniczny (dostęp za pośrednictwem połączenia sieciowego), zawsze jest okres, po upływie którego dane będą ładowane i dostępne do użycia przez składnik.</span><span class="sxs-lookup"><span data-stu-id="4dcff-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="4dcff-238">Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.</span><span class="sxs-lookup"><span data-stu-id="4dcff-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="4dcff-239">Jednym z metod jest śledzenie, czy dane są `null` (nadal ładowane) czy nie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="4dcff-240">W domyślnym składniku `Counter` liczba jest utrzymywana w `int`.</span><span class="sxs-lookup"><span data-stu-id="4dcff-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="4dcff-241">Wprowadź `_currentCount` wartość null, dodając znak zapytania (`?`) do typu (`int`):</span><span class="sxs-lookup"><span data-stu-id="4dcff-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="4dcff-242">Zamiast bezwarunkowo wyświetlać przycisk Count i **Increment** , wybierz, aby wyświetlić te elementy tylko wtedy, gdy dane są ładowane:</span><span class="sxs-lookup"><span data-stu-id="4dcff-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="4dcff-243">Obsługa przed renderowaniem</span><span class="sxs-lookup"><span data-stu-id="4dcff-243">Handle prerendering</span></span>

<span data-ttu-id="4dcff-244">Podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="4dcff-244">During prerendering:</span></span>

* <span data-ttu-id="4dcff-245">Połączenie interaktywne z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="4dcff-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="4dcff-246">Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4dcff-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="4dcff-247">`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="4dcff-248">Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd podobny do:</span><span class="sxs-lookup"><span data-stu-id="4dcff-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="4dcff-249">W tej chwili nie można wystawić wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4dcff-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="4dcff-250">Dzieje się tak, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="4dcff-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="4dcff-251">Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="4dcff-252">Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4dcff-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="4dcff-253">Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości, dopóki `localStorage` lub `sessionStorage` nie są dostępne.</span><span class="sxs-lookup"><span data-stu-id="4dcff-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="4dcff-254">Aby wyłączyć renderowanie, Otwórz plik *Pages/_Host. cshtml* i zmień wywołanie `render-mode` pomocnika tagu `Component` na `Server`.</span><span class="sxs-lookup"><span data-stu-id="4dcff-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `render-mode` of the `Component` Tag Helper to `Server`.</span></span>

<span data-ttu-id="4dcff-255">Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` lub `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="4dcff-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="4dcff-256">Aby włączyć renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="4dcff-257">Poniżej przedstawiono przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="4dcff-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="4dcff-258">Wyrównać zachowywanie stanu do wspólnej lokalizacji</span><span class="sxs-lookup"><span data-stu-id="4dcff-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="4dcff-259">Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="4dcff-260">Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="4dcff-261">Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="4dcff-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="4dcff-262">W poniższym przykładzie składnika `CounterStateProvider` dane licznika są utrwalane:</span><span class="sxs-lookup"><span data-stu-id="4dcff-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="4dcff-263">Składnik `CounterStateProvider` obsługuje fazę ładowania, ponieważ nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="4dcff-264">Aby użyć składnika `CounterStateProvider`, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="4dcff-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="4dcff-265">Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń składnik `CounterStateProvider` wokół `Router` w składniku `App` (*App. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4dcff-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="4dcff-266">Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników.</span><span class="sxs-lookup"><span data-stu-id="4dcff-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="4dcff-267">Poniższy składnik `Counter` implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="4dcff-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="4dcff-268">Poprzedni składnik nie jest wymagany do współdziałania z `ProtectedBrowserStorage`ani nie zajmuje się fazą "Ładowanie".</span><span class="sxs-lookup"><span data-stu-id="4dcff-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="4dcff-269">Aby przeprowadzić postępowanie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` można zmienić tak, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania.</span><span class="sxs-lookup"><span data-stu-id="4dcff-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="4dcff-270">Szczegółowe informacje znajdują się w sekcji [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4dcff-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="4dcff-271">Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :</span><span class="sxs-lookup"><span data-stu-id="4dcff-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="4dcff-272">Aby korzystać z stanu w wielu innych składnikach.</span><span class="sxs-lookup"><span data-stu-id="4dcff-272">To consume state in many other components.</span></span>
* <span data-ttu-id="4dcff-273">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.</span><span class="sxs-lookup"><span data-stu-id="4dcff-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="4dcff-274">Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać obsługi ładowania i zapisywania stanu globalnie.</span><span class="sxs-lookup"><span data-stu-id="4dcff-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
