---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Odkryj, jak obsługiwać błędy w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: c20d8757eef80fdbb73b1b7a9933a3c0be9bb8ed
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358980"
---
# <a name="handle-errors-in-aspnet-core"></a>Obsługa błędów w ASP.NET Core

Autorzy [Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex)i [Steve Smith](https://ardalis.com/)

W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core. Zobacz <xref:web-api/handle-errors> interfejsów API sieci Web.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Jak pobrać](xref:index#how-to-download-a-sample)) Artykuł zawiera instrukcje dotyczące sposobu ustawiania dyrektyw preprocesora (`#if`, `#endif`, `#define`) w przykładowej aplikacji, aby umożliwić korzystanie z różnych scenariuszy.

## <a name="developer-exception-page"></a>Strona wyjątków dla deweloperów

Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania. Strona jest udostępniana przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , który znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Dodaj kod do metody `Startup.Configure`, aby włączyć stronę, gdy aplikacja jest uruchomiona w [środowisku](xref:fundamentals/environments)deweloperskim:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Należy umieścić wywołanie <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> przed dowolnym oprogramowanie pośredniczące, które ma przechwytywać wyjątki.

> [!WARNING]
> Stronę wyjątku dla deweloperów należy włączyć tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**. Nie chcesz udostępniać szczegółowych informacji o wyjątku publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments>.

Na stronie znajdują się następujące informacje o wyjątku i żądaniu:

* Ślad stosu
* Parametry ciągu zapytania (jeśli istnieją)
* Pliki cookie (jeśli istnieją)
* Nagłówki

Aby wyświetlić stronę wyjątku dla deweloperów w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj dyrektywy preprocesora `DevEnvironment` i wybierz pozycję **Wyzwól wyjątek** na stronie głównej.

## <a name="exception-handler-page"></a>Strona obsługi wyjątków

Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego. Oprogramowanie pośredniczące:

* Przechwytuje i rejestruje wyjątki.
* Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera. Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.

Poniższy przykład <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Szablon aplikacji Razor Pages zawiera stronę błędów ( *. cshtml*) i klasę <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) w folderze *Pages* . W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędu. Oto Metoda działania:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet`. Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody. Zezwalaj na anonimowy dostęp do metody, aby nieuwierzytelnionym użytkownikom mogli odebrać widok błędów.

### <a name="access-the-exception"></a>Uzyskaj dostęp do wyjątku

Użyj <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach do klientów. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby wyświetlić stronę obsługa wyjątków w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj dyrektyw preprocesora `ProdEnvironment` i `ErrorHandlerPage`, a następnie wybierz pozycję **Wyzwól wyjątek** na stronie głównej.

## <a name="exception-handler-lambda"></a>Procedura obsługi wyjątków lambda

Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest podanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.

Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

W poprzednim kodzie `await context.Response.WriteAsync(new string(' ', 512));` został dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE. Aby uzyskać więcej informacji, zobacz [problem w usłudze GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> lub <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> do klientów. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj dyrektyw preprocesora `ProdEnvironment` i `ErrorHandlerLambda`, a następnie wybierz pozycję **Wyzwól wyjątek** na stronie głównej.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono*. Aplikacja zwraca kod stanu i pustą treść odpowiedzi. Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.

Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , który znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> w metodzie `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).

Oto przykład tekstu wyświetlanego przez domyślne programy obsługi:

```
Status Code: 404; Not Found
```

Aby wyświetlić jeden z różnych formatów stron kodu stanu w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), należy użyć jednej z dyrektyw preprocesora, która rozpoczyna się od `StatusCodePages`, a na stronie głównej wybierz pozycję **Wyzwól a 404** .

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages z ciągiem formatu

Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, które pobiera typ zawartości i ciąg formatu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages z wyrażeniem lambda

Aby określić niestandardową obsługę błędów i kod do pisania, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, które przyjmuje wyrażenie lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Metoda rozszerzenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Wysyła do klienta kod stanu *znaleziony przez 302* .
* Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Szablon adresu URL może zawierać `{0}` symbol zastępczy dla kodu stanu, jak pokazano w przykładzie. Jeśli szablon adresu URL zaczyna się od tyldy (~), tylda jest zastępowana `PathBase`aplikacji. Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub stronę Razor dla punktu końcowego. Aby uzyskać Razor Pages przykład, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana w przypadku aplikacji:

* Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.
* Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Metoda rozszerzenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Zwraca oryginalny kod stanu dla klienta.
* Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub stronę Razor dla punktu końcowego. Aby uzyskać Razor Pages przykład, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana, gdy aplikacja powinna:

* Przetwórz żądanie bez przekierowania do innego punktu końcowego. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.
* Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.

Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy (`{0}`) dla kodu stanu. Szablon adresu URL musi rozpoczynać się od ukośnika (`/`). W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki. Na przykład strona Razor dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu dyrektywy `@page`:

```cshtml
@page "{code?}"
```

Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Wyłącz strony kodu stanu

Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, Użyj atrybutu [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .

Aby wyłączyć strony kodowe stanu dla określonych żądań w metodzie obsługi Razor Pages lub w kontrolerze MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kod obsługi wyjątków

Kod na stronach obsługi wyjątków może generować wyjątki. Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.

### <a name="response-headers"></a>Nagłówki odpowiedzi

Po wysłaniu nagłówków odpowiedzi:

* Aplikacja nie może zmienić kodu stanu odpowiedzi.
* Nie można uruchomić żadnych stron ani programów wyjątków. Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.

## <a name="server-exception-handling"></a>Obsługa wyjątków serwera

Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki. Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi. Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie. Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer. Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera. Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.

## <a name="startup-exception-handling"></a>Obsługa wyjątków uruchamiania

Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji. Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).

W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu. Jeśli powiązanie nie powiedzie się:

* Warstwa hostingu rejestruje wyjątek krytyczny.
* Proces dotnet ulega awarii.
* Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).

W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu. Aby uzyskać więcej informacji, zobacz temat <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Strona błędu bazy danych

Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework. Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu. Ta strona powinna być włączona tylko w środowisku deweloperskim. Włącz stronę, dodając kod do `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtry wyjątków

W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji. W aplikacjach Razor Pages można je skonfigurować globalnie lub na model strony. Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru. Aby uzyskać więcej informacji, zobacz temat <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące. Zalecamy używanie oprogramowania pośredniczącego. Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.

## <a name="model-state-errors"></a>Błędy stanu modelu

Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
