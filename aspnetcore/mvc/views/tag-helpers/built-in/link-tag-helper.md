---
title: Pomocnik tagu linku w ASP.NET Core
author: rick-anderson
ms.author: riande
description: Odkryj atrybuty pomocnika znacznika łącza ASP.NET Core i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania taga HTML tag.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: d7514433bee8a138cd7d75bfd15c9798d4fd31a3
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809110"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="fb1cf-103">Pomocnik tagu linku w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb1cf-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="fb1cf-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fb1cf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb1cf-105">[Pomocnik tagu linku](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generuje łącze do podstawowego lub przywróconego pliku CSS.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="fb1cf-106">Zazwyczaj podstawowy plik CSS znajduje się w [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="fb1cf-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="fb1cf-107">Pomocnik tagu linku umożliwia określenie sieci CDN dla pliku CSS i rezerwę, gdy sieć CDN jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="fb1cf-108">Pomocnik tagu linku zapewnia wydajność sieci CDN z niezawodną obsługą hostingu lokalnego.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="fb1cf-109">Poniższy znacznik Razor przedstawia element `head` pliku układu utworzonego za pomocą szablonu ASP.NET Core aplikacji sieci Web:</span><span class="sxs-lookup"><span data-stu-id="fb1cf-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="fb1cf-110">Następujące elementy są renderowane HTML z poprzedniego kodu (w środowisku innym niż programowanie):</span><span class="sxs-lookup"><span data-stu-id="fb1cf-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="fb1cf-111">W poprzednim kodzie pomocnik tagu linku wygenerował element `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` i następujący kod JavaScript, który jest używany do weryfikowania żądanego pliku *Bootstrap. min. css* jest dostępny w sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="fb1cf-112">W takim przypadku plik CSS był dostępny, aby pomocnik tagów wygenerował `<link />` element z plikiem CSS usługi CDN.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="fb1cf-113">Atrybuty pomocnika często używanych tagów linków</span><span class="sxs-lookup"><span data-stu-id="fb1cf-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="fb1cf-114">Zobacz [pomocnika tagów linków](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) dla wszystkich atrybutów pomocnika tagów linków, właściwości i metod.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="fb1cf-115">{1&gt;href&lt;1}</span><span class="sxs-lookup"><span data-stu-id="fb1cf-115">href</span></span>

<span data-ttu-id="fb1cf-116">Preferowany adres połączonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="fb1cf-117">Adres jest przekazaniem do wygenerowanego kodu HTML we wszystkich przypadkach.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="fb1cf-118">ASP-Fallback-href</span><span class="sxs-lookup"><span data-stu-id="fb1cf-118">asp-fallback-href</span></span>

<span data-ttu-id="fb1cf-119">Adres URL arkusza stylów CSS, do którego ma nastąpić powrót w przypadku niepowodzenia podstawowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="fb1cf-120">ASP-Fallback-test-Klasa</span><span class="sxs-lookup"><span data-stu-id="fb1cf-120">asp-fallback-test-class</span></span>

<span data-ttu-id="fb1cf-121">Nazwa klasy zdefiniowana w arkuszu stylów do użycia w teście Fallback.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="fb1cf-122">Aby uzyskać więcej informacji, zobacz temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="fb1cf-123">ASP-Fallback-test-Property</span><span class="sxs-lookup"><span data-stu-id="fb1cf-123">asp-fallback-test-property</span></span>

<span data-ttu-id="fb1cf-124">Nazwa właściwości CSS do użycia w teście Fallback.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="fb1cf-125">Aby uzyskać więcej informacji, zobacz temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="fb1cf-126">ASP — wartość testowa</span><span class="sxs-lookup"><span data-stu-id="fb1cf-126">asp-fallback-test-value</span></span>

<span data-ttu-id="fb1cf-127">Wartość właściwości CSS do użycia dla testu powrotu.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="fb1cf-128">Aby uzyskać więcej informacji, zobacz temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="fb1cf-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb1cf-129">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fb1cf-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
