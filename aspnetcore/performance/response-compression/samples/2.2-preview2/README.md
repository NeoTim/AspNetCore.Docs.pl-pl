# <a name="response-compression-sample-application-aspnet-core-2x"></a><span data-ttu-id="a097f-101">Aplikacja przykładowa kompresji odpowiedzi (Platforma ASP.NET Core 2.x)</span><span class="sxs-lookup"><span data-stu-id="a097f-101">Response compression sample application (ASP.NET Core 2.x)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a097f-102">W przykładzie użyto zestawu SDK platformy ASP.NET Core 2.2 w wersji zapoznawczej 2 i środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="a097f-102">This sample uses the ASP.NET Core 2.2 Preview 2 SDK and runtime.</span></span> <span data-ttu-id="a097f-103">Uzyskiwanie wersji zapoznawczej z [pliki do pobrania platformy .NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2).</span><span class="sxs-lookup"><span data-stu-id="a097f-103">Obtain the preview release from [.NET Core 2.2 downloads](https://www.microsoft.com/net/download/dotnet-core/2.2).</span></span> <span data-ttu-id="a097f-104">Upewnij się, wersja zestawu SDK w *global.json* pliku i [meta Microsoft.aspnetcore.all Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) wersji są poprawne w pliku projektu dla zestawu SDK i wersję udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="a097f-104">Confirm the SDK version in the *global.json* file and the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) version are correct in the project file for your SDK and shared framework version.</span></span>

<span data-ttu-id="a097f-105">Ten przykład ilustruje sposób używania programu ASP.NET Core 2.x, aby kompresowały odpowiedzi HTTP dla, oprogramowanie pośredniczące kompresji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-105">This sample illustrates the use of ASP.NET Core 2.x Response Compression Middleware to compress HTTP responses for.</span></span> <span data-ttu-id="a097f-106">Próbka pokazuje Gzip, Brotli i dostawców niestandardowych kompresji odpowiedzi tekstu i obrazów i pokazuje, jak dodać typ MIME dla kompresji.</span><span class="sxs-lookup"><span data-stu-id="a097f-106">The sample demonstrates Gzip, Brotli, and custom compression providers for text and image responses and shows how to add a MIME type for compression.</span></span> <span data-ttu-id="a097f-107">Dla platformy ASP.NET Core 1.x przykładu, zobacz [aplikacji przykładowej kompresji odpowiedzi (Platforma ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).</span><span class="sxs-lookup"><span data-stu-id="a097f-107">For the ASP.NET Core 1.x sample, see [Response compression sample application (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="a097f-108">Przykłady w tym przykładzie</span><span class="sxs-lookup"><span data-stu-id="a097f-108">Examples in this sample</span></span>

* `BrotliCompressionProvider`
  * `text/plain`
    * <span data-ttu-id="a097f-109">**/** -Lorem Ipsum tekst pliku odpowiedzi w bajtach 2,044 kompresuje ~ 979 bajtów.</span><span class="sxs-lookup"><span data-stu-id="a097f-109">**/** - Lorem Ipsum text file response at 2,044 bytes that compresses to ~979 bytes.</span></span>
    * <span data-ttu-id="a097f-110">**/testfile1kb.txt** — tekst pliku odpowiedzi w bajtach 1,033 kompresuje ~ 36 bajtów.</span><span class="sxs-lookup"><span data-stu-id="a097f-110">**/testfile1kb.txt** - Text file response at 1,033 bytes that compresses to ~36 bytes.</span></span>
    * <span data-ttu-id="a097f-111">**/ ścieknie** -wystawione jako pojedyncze znaki w 1, drugi odstępach czasu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-111">**/trickle** - Response issued as single characters at 1 second intervals.</span></span>
* `GzipCompressionProvider`
  * `text/plain`
    * <span data-ttu-id="a097f-112">**/** -Lorem Ipsum tekst pliku odpowiedzi w bajtach 2,044 kompresuje ~ 927 bajtów.</span><span class="sxs-lookup"><span data-stu-id="a097f-112">**/** - Lorem Ipsum text file response at 2,044 bytes that compresses to ~927 bytes.</span></span>
    * <span data-ttu-id="a097f-113">**/testfile1kb.txt** — tekst pliku odpowiedzi w bajtach 1,033 kompresuje ~ 47 bajtów.</span><span class="sxs-lookup"><span data-stu-id="a097f-113">**/testfile1kb.txt** - Text file response at 1,033 bytes that compresses to ~47 bytes.</span></span>
    * <span data-ttu-id="a097f-114">**/ ścieknie** -wystawione jako pojedyncze znaki w 1, drugi odstępach czasu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-114">**/trickle** - Response issued as single characters at 1 second intervals.</span></span>
  * `image/svg+xml`
    * <span data-ttu-id="a097f-115">**/Banner.SVG** -odpowiedzi obraz grafiki wektorowej skalowalne (SVG) na 9,707 bajtów, która kompresuje ~ 4,459 bajtów.</span><span class="sxs-lookup"><span data-stu-id="a097f-115">**/banner.svg** - A Scalable Vector Graphics (SVG) image response at 9,707 bytes that compresses to ~4,459 bytes.</span></span>
* `CustomCompressionProvider`<br><span data-ttu-id="a097f-116">Pokazuje, jak do implementowania dostawcy kompresji niestandardowych do użytku z oprogramowaniem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="a097f-116">Shows how to implement a custom compression provider for use with the middleware.</span></span>

<span data-ttu-id="a097f-117">Jeśli żądanie zawiera `Accept-Encoding` kompresja nagłówków i odpowiedzi zakończy się pomyślnie, oprogramowanie pośredniczące automatycznie dodaje `Vary: Accept-Encoding` nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-117">When the request includes the `Accept-Encoding` header and response compression is successful, the middleware automatically adds a `Vary: Accept-Encoding` header to the response.</span></span> <span data-ttu-id="a097f-118">`Vary` Pamięci podręcznych do obsługi wielu kopii odpowiedzi na podstawie alternatywne wartości powoduje, że nagłówek `Accept-Encoding`, więc obie skompresowany (Gzip lub Brotli) i wersji nieskompresowanych są przechowywane w pamięci podręcznej dla systemów, które mogą akceptować skompresowany lub bez kompresji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-118">The `Vary` header instructs caches to maintain multiple copies of the response based on alternative values of `Accept-Encoding`, so both a compressed (Gzip or Brotli) and uncompressed version are stored in caches for systems that can either accept the compressed or the uncompressed response.</span></span>

## <a name="using-the-sample"></a><span data-ttu-id="a097f-119">Przy użyciu przykładu</span><span class="sxs-lookup"><span data-stu-id="a097f-119">Using the sample</span></span>

1. <span data-ttu-id="a097f-120">Upewnij się, żądanie, używając [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), lub [Postman](https://www.getpostman.com/) do aplikacji bez `Accept-Encoding` nagłówka i zanotuj ładunek odpowiedzi rozmiar odpowiedzi i nagłówki odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-120">Make a request using [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), or [Postman](https://www.getpostman.com/) to the application without an `Accept-Encoding` header and note the response payload, response size, and response headers.</span></span>
1. <span data-ttu-id="a097f-121">Dodaj `Accept-Encoding: br` lub `Accept-Encoding: gzip` nagłówka i zwróć uwagę, Rozmiar skompresowanych odpowiedzi i nagłówkami odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-121">Add an `Accept-Encoding: br` or `Accept-Encoding: gzip` header and note the compressed response size and response headers.</span></span> <span data-ttu-id="a097f-122">Rozmiar odpowiedzi spadnie i `Content-Encoding` nagłówek odpowiedzi znajduje się przez oprogramowanie pośredniczące wskazująca, że kompresja za pomocą dowolnego narzędzia Gzip lub Brotli wystąpił.</span><span class="sxs-lookup"><span data-stu-id="a097f-122">The response size drops, and the `Content-Encoding` response header is included by the middleware indicating that compression with either Gzip or Brotli occurred.</span></span> <span data-ttu-id="a097f-123">Patrząc na treść odpowiedzi dla Lorem Ipsum lub **testfile1kb.txt** odpowiedzi, możesz zobaczyć, że tekst jest skompresowany i nie można go odczytać.</span><span class="sxs-lookup"><span data-stu-id="a097f-123">When you look at the response body for the Lorem Ipsum or **testfile1kb.txt** response, you see that the text is compressed and unreadable.</span></span>
1. <span data-ttu-id="a097f-124">Dodaj `Accept-Encoding: mycustomcompression` nagłówka i zanotuj nagłówki odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a097f-124">Add an `Accept-Encoding: mycustomcompression` header and note the response headers.</span></span> <span data-ttu-id="a097f-125">`CustomCompressionProvider` Jest pusty implementacji, które faktycznie nie Kompresuj odpowiedzi, ale można utworzyć otokę strumienia niestandardowe kompresji dla `CreateStream()` metody.</span><span class="sxs-lookup"><span data-stu-id="a097f-125">The `CustomCompressionProvider` is an empty implementation that doesn't actually compress the response, but you can create a custom compression stream wrapper for the `CreateStream()` method.</span></span>