---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak udostępniać i zabezpieczać pliki statyczne oraz skonfigurować zachowania oprogramowania pośredniczącego do obsługi plików statycznych w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/8/2019
uid: fundamentals/static-files
ms.openlocfilehash: 1c665d1206e984fe41e9f57bb5356839c354dde2
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308195"
---
# <a name="static-files-in-aspnet-core"></a>Pliki statyczne w ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Scott Addie](https://twitter.com/Scott_Addie)

Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które aplikacja ASP.NET Core może bezpośrednio obsługiwać klientów. Aby umożliwić obsługę tych plików, wymagana jest pewna konfiguracja.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsługuj pliki statyczne

Pliki statyczne są przechowywane w katalogu głównym sieci Web projektu. Domyślnym katalogiem jest  *\<content_root >/wwwroot*, ale można go zmienić za pomocą metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root) .

Host sieci Web aplikacji musi być świadomy katalogu głównego zawartości.

::: moniker range=">= aspnetcore-2.0"

`WebHost.CreateDefaultBuilder` Metoda ustawia katalog główny zawartości dla bieżącego katalogu:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Ustaw katalog główny zawartości w bieżącym katalogu, wywołując [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) wewnątrz `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla katalogu głównego sieci Web. Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w folderze *wwwroot* :

* **wwwroot**
  * **kaskad**
  * **images**
  * **js**

Format identyfikatora URI służący do uzyskiwania dostępu do  pliku w podfolderze images to *http://\<server_address >\</images/image_file_name >* . Na przykład *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu. W przypadku określania platformy .NET Core [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) zawiera ten pakiet.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu. W przypadku określania platformy .NET Core pakiet [Microsoft. AspNetCore. All](xref:fundamentals/metapackage) zawiera ten pakiet.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.

::: moniker-end

Skonfiguruj [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) , które umożliwia obsługę plików statycznych.

### <a name="serve-files-inside-of-web-root"></a>Obsługuj pliki wewnątrz katalogu głównego sieci Web

Wywołaj metodę [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) w `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Przeciążenie `UseStaticFiles` metody bez parametrów oznacza pliki w katalogu głównym sieci Web jako do zablokowania. Następujące znaczniki odwołują się do *wwwroot/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

W poprzednim kodzie znak `~/` tyldy wskazuje na Webroot. Aby uzyskać więcej informacji, zobacz [katalog główny sieci Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsługiwanie plików poza katalogiem głównym sieci Web

Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza katalogiem głównym sieci Web:

* **wwwroot**
  * **kaskad**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

Żądanie może uzyskać dostęp do pliku *banner1. SVG* przez skonfigurowanie pliku statycznego pośredniczącego w następujący sposób:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* . Żądanie *http://\<server_address >/StaticFiles/images/banner1.SVG* obsługuje plik *banner1. SVG* .

Następujące znaczniki odwołują się do *MyStaticFiles/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

Obiekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego z poziomu katalogu głównego sieci Web, poniższy kod ustawia `Cache-Control` nagłówek:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

Metoda [HeaderDictionaryExtensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) istnieje w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

Pliki zostały publicznie przetworzone w pamięci podręcznej przez 10 minut (600 sekund) w środowisku deweloperskim:

![Nagłówki odpowiedzi pokazujące nagłówek Cache-Control został dodany](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja pliku statycznego

Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji. Wszystkie pliki obsługiwane przez ten program, w tym w katalogu *wwwroot*, są publicznie dostępne. Aby obpracować pliki na podstawie autoryzacji:

* Przechowaj je poza katalogiem *wwwroot* i dowolnym katalogu dostępnym dla oprogramowania pośredniczącego plików statycznych.
* Obsłużyć je za pomocą metody akcji, do której zastosowano autoryzację. Zwróć obiekt [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Włącz przeglądanie katalogów

Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci Web Wyświetlanie listy katalogów i plików w określonym katalogu. Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (zobacz [uwagi](#considerations)). Włącz przeglądanie katalogów, wywołując metodę [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) w `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Dodaj wymagane usługi, wywołując metodę [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address >/myimages*, z linkami do każdego pliku i folderu:

![Przeglądanie katalogów](static-files/_static/dir-browse.png)

Zapoznaj się z [uwagami](#considerations) dotyczącymi zagrożeń bezpieczeństwa podczas włączania przeglądania.

Zwróć uwagę na `UseStaticFiles` dwa wywołania w poniższym przykładzie. Pierwsze wywołanie umożliwia obsługę plików statycznych w folderze *wwwroot* . Drugie wywołanie umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address >/myimages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Obsługuj dokument domyślny

Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny. Aby obtworzyć stronę domyślną bez zakwalifikowania identyfikatora URI użytkownika, wywołaj metodę [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`musi być wywoływana przed `UseStaticFiles` zapisaniem pliku domyślnego. `UseDefaultFiles`to adres URL, który faktycznie nie obsługuje pliku. Włącz oprogramowanie pośredniczące plików statycznych `UseStaticFiles` za pośrednictwem programu w celu obsługi pliku.

W `UseDefaultFiles`programie żądania do folderu wyszukują:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Poniższy kod zmienia domyślną nazwę pliku na *default. html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) łączy funkcje `UseStaticFiles`, `UseDefaultFiles`i. `UseDirectoryBrowser`

Poniższy kod umożliwia obsługę plików statycznych i pliku domyślnego. Przeglądanie katalogów nie jest włączone.

```csharp
app.UseFileServer();
```

Poniższy kod kompiluje na Przeciążenie bez parametrów przez włączenie przeglądania katalogów:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Weź pod uwagę następującą hierarchię katalogów:

* **wwwroot**
  * **kaskad**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Poniższy kod umożliwia włączenie plików statycznych, plików domyślnych i przeglądanie `MyStaticFiles`katalogów:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`musi być wywoływana, `EnableDirectoryBrowsing` gdy wartość właściwości to: `true`

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:

| Identyfikator URI            |                             Odpowiedź  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Jeśli w katalogu *MyStaticFiles* nie istnieje plik o nazwie Default, *http://\<server_address >/StaticFiles* zwraca listę katalogów z linkami, które można kliknąć:

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> przekierować po stronie klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z końcowym ukośnikiem). Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowej kreski ułamkowej.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Klasa [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) zawiera `Mappings` Właściwość służącą jako mapowanie rozszerzeń plików na typy zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są zarejestrowane na znanych typach MIME. Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* jest usuwany.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików static rozpoznaje niemal 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* . Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.

Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stanowi zagrożenie bezpieczeństwa. Jest on domyślnie wyłączony i nie jest zalecane jego użycie. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.

### <a name="considerations"></a>Uwagi

> [!WARNING]
> `UseDirectoryBrowser`i `UseStaticFiles` mogą wyciekać klucze tajne. Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane. Uważnie Przejrzyj katalogi, które są włączone `UseStaticFiles` za `UseDirectoryBrowser`pośrednictwem lub. Cały katalog i jego katalogi podrzędne stają się publicznie dostępne. Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, takie jak  *\<content_root >/wwwroot*. Oddziel te pliki od widoków MVC, Razor Pages (tylko 2. x), plików konfiguracji itp.

* Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików. Na przykład w systemie Windows nie jest rozróżniana wielkość liter&mdash;macOS i Linux.

* ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych. Procedura obsługi pliku statycznego usług IIS nie jest używana. Nie ma możliwości obsługi żądań, zanim są one obsługiwane przez moduł.

* Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:
    1. Przejdź do funkcji **moduły** .
    1. Na liście wybierz pozycję **StaticFileModule** .
    1. Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .

> [!WARNING]
> Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne. Dzieje się tak na przykład wtedy, gdy plik *Web. config* nie jest wdrożony.

* Umieść pliki kodu (w tym *. cs* i *. cshtml*) poza katalogiem głównym sieci Web projektu aplikacji. W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze. Zapobiega to wyciekowi kodu po stronie serwera.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Wprowadzenie do platformy ASP.NET Core](xref:index)
