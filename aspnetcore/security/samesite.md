---
title: Pracuj z plikami cookie SameSite w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać programu do SameSite plików cookie w ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Electron
uid: security/samesite
ms.openlocfilehash: eeba2c4403d33312692ed187021a125c22df5d08
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667742"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="5a6f6-103">Pracuj z plikami cookie SameSite w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a6f6-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="5a6f6-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a6f6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5a6f6-105">SameSite jest projektem standardowym [IETF](https://ietf.org/about/) opracowanym w celu zapewnienia pewnej ochrony przed atakami polegającymi na translokacjach (CSRF).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="5a6f6-106">Pierwotnie Sporządzono w [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), projekt Standard został zaktualizowany w [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="5a6f6-107">Zaktualizowany standard nie jest zgodny z poprzednią wersją standardową, co poniżej przedstawiono najbardziej zauważalne różnice:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="5a6f6-108">Pliki cookie bez nagłówka SameSite są domyślnie traktowane jako `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="5a6f6-109">`SameSite=None` należy użyć, aby zezwolić na użycie plików cookie między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="5a6f6-110">Pliki cookie, które potwierdzają `SameSite=None` muszą być również oznaczone jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="5a6f6-111">Aplikacje korzystające z [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) mogą napotkać problemy `sameSite=Lax` lub `sameSite=Strict` plików cookie, ponieważ `<iframe>` jest traktowana jako scenariusze między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="5a6f6-112">Wartość `SameSite=None` nie jest dozwolona przez [standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) i powoduje, że niektóre implementacje traktują takie pliki cookie jako `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="5a6f6-113">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="5a6f6-114">Ustawienie `SameSite=Lax` działa w przypadku większości plików cookie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="5a6f6-115">Niektóre formy uwierzytelniania, takie jak [OpenID Connect Connect](https://openid.net/connect/) (OIDC) i [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , domyślnie mogą publikować przekierowania na podstawie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="5a6f6-116">Przekierowania na podstawie wpisu wyzwalają ochronę za pomocą przeglądarki SameSite, więc SameSite jest wyłączona dla tych składników.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="5a6f6-117">Nie ma to wpływ na większość nazw logowania [OAuth](https://oauth.net/) z powodu różnic w sposobie przepływu żądania.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="5a6f6-118">Każdy składnik ASP.NET Core, który emituje pliki cookie, musi zdecydować, czy SameSite jest odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="5a6f6-119">Przykładowy kod testu SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="5a6f6-120">Następujące przykłady można pobrać i przetestować:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="5a6f6-121">Sample</span><span class="sxs-lookup"><span data-stu-id="5a6f6-121">Sample</span></span>               | <span data-ttu-id="5a6f6-122">Dokument</span><span class="sxs-lookup"><span data-stu-id="5a6f6-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5a6f6-123">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5a6f6-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="5a6f6-124">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5a6f6-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a6f6-125">Można pobrać i przetestować następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="5a6f6-126">Sample</span><span class="sxs-lookup"><span data-stu-id="5a6f6-126">Sample</span></span>               | <span data-ttu-id="5a6f6-127">Dokument</span><span class="sxs-lookup"><span data-stu-id="5a6f6-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5a6f6-128">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5a6f6-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="5a6f6-129">Obsługa platformy .NET Core dla atrybutu sameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="5a6f6-130">Program .NET Core 2,2 obsługuje wersję standardową 2019 dla SameSite od momentu wydania aktualizacji w grudniu 2019.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="5a6f6-131">Deweloperzy mogą programowo kontrolować wartość atrybutu sameSite przy użyciu właściwości `HttpCookie.SameSite`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="5a6f6-132">Ustawienie właściwości `SameSite` na Strict, swobodny lub None powoduje, że te wartości są zapisywane w sieci przy użyciu pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="5a6f6-133">Ustawienie tej opcji równe (SameSiteMode) (-1) wskazuje, że żaden atrybut sameSite nie powinien być uwzględniony w sieci z plikiem cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="5a6f6-134">Program .NET Core 3,0 obsługuje zaktualizowane wartości SameSite i dodaje dodatkową wartość enum, `SameSiteMode.Unspecified` do `SameSiteMode` enum.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="5a6f6-135">Ta nowa wartość wskazuje, że żaden sameSite nie powinien być wysyłany z plikiem cookie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="5a6f6-136">Zmiany zachowania poprawek w grudniu</span><span class="sxs-lookup"><span data-stu-id="5a6f6-136">December patch behavior changes</span></span>

<span data-ttu-id="5a6f6-137">Określone zmiany zachowania dla .NET Framework i .NET Core 2,1 to sposób, w jaki Właściwość `SameSite` interpretuje wartość `None`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="5a6f6-138">Przed zastosowaniem przez poprawkę wartości `None` "nie emituj atrybutu wcale", po zastosowaniu poprawki oznacza "Emituj atrybut wartością `None`".</span><span class="sxs-lookup"><span data-stu-id="5a6f6-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="5a6f6-139">Po zastosowaniu poprawki `SameSite` wartość `(SameSiteMode)(-1)` powoduje, że atrybut nie będzie emitowany.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="5a6f6-140">Domyślna wartość SameSite dla plików cookie związanych z uwierzytelnianiem formularzy i stanem sesji została zmieniona z `None` na `Lax`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="5a6f6-141">Użycie interfejsu API z SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-141">API usage with SameSite</span></span>

<span data-ttu-id="5a6f6-142">[HttpContext. Response. cookies. Dołącz](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) wartości domyślne do `Unspecified`, co oznacza, że żaden atrybut SameSite nie został dodany do pliku cookie, a klient użyje jego domyślnego zachowania (swobodny dla nowych przeglądarek, brak dla starych).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="5a6f6-143">Poniższy kod przedstawia sposób zmiany wartości SameSite w pliku cookie na `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5a6f6-144">Wszystkie składniki ASP.NET Core, które emitują pliki cookie, zastępują poprzednie wartości domyślne przy użyciu ustawień odpowiednich dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="5a6f6-145">Zastąpione poprzednie wartości domyślne nie zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="5a6f6-146">Składnik</span><span class="sxs-lookup"><span data-stu-id="5a6f6-146">Component</span></span> | <span data-ttu-id="5a6f6-147">plików</span><span class="sxs-lookup"><span data-stu-id="5a6f6-147">cookie</span></span> | <span data-ttu-id="5a6f6-148">Domyślne</span><span class="sxs-lookup"><span data-stu-id="5a6f6-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="5a6f6-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="5a6f6-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="5a6f6-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="5a6f6-152">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="5a6f6-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="5a6f6-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="5a6f6-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="5a6f6-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="5a6f6-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="5a6f6-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="5a6f6-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="5a6f6-158">ASP.NET Core 3,1 i nowsze oferują następujące wsparcie SameSite:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="5a6f6-159">Ponownie definiuje zachowanie `SameSiteMode.None`, aby emitować `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="5a6f6-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="5a6f6-160">Dodaje nową wartość `SameSiteMode.Unspecified`, aby pominąć atrybut SameSite.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="5a6f6-161">Wszystkie interfejsy API plików cookie są domyślnie `Unspecified`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="5a6f6-162">Niektóre składniki używające plików cookie ustawiają wartości bardziej specyficzne dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="5a6f6-163">Przykłady można znaleźć w powyższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a6f6-164">W ASP.NET Core 3,0 i nowszych wartości domyślnych SameSite zostały zmienione, aby uniknąć konfliktu z niespójnymi wartościami domyślnymi klienta.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="5a6f6-165">Następujące interfejsy API zmieniły się domyślnie z `SameSiteMode.Lax ` na `-1`, aby uniknąć emitowania atrybutu SameSite dla tych plików cookie:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="5a6f6-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> używany z obiektem [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="5a6f6-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="5a6f6-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder> używany jako fabryka dla `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="5a6f6-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="5a6f6-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5a6f6-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="5a6f6-169">Historia i zmiany</span><span class="sxs-lookup"><span data-stu-id="5a6f6-169">History and changes</span></span>

<span data-ttu-id="5a6f6-170">Obsługa SameSite została najpierw zaimplementowana w ASP.NET Core w 2,0 przy użyciu [standardowego standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="5a6f6-171">Standard 2016 został zadecydować.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="5a6f6-172">ASP.NET Core wybierz opcję, ustawiając kilka plików cookie do `Lax` domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="5a6f6-173">Po napotkaniu kilku [problemów](https://github.com/aspnet/Announcements/issues/318) z uwierzytelnianiem większość SameSite zostało [wyłączone](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="5a6f6-174">[Poprawki](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zostały wydane w listopadzie 2019, aby zaktualizować ze standardu 2016 do normy 2019.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="5a6f6-175">[Wersja robocza 2019 specyfikacji SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="5a6f6-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="5a6f6-176">**Nie** jest wstecznie zgodne z wersją roboczą 2016.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="5a6f6-177">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="5a6f6-178">Określa, że pliki cookie są domyślnie traktowane jako `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="5a6f6-179">Określa pliki cookie, które jawnie potwierdzają `SameSite=None` w celu włączenia dostarczania między lokacjami, powinny być oznaczone jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="5a6f6-180">`None` to nowy wpis do rezygnacji.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="5a6f6-181">Jest obsługiwane przez poprawki wydane dla ASP.NET Core 2,1, 2,2 i 3,0.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="5a6f6-182">ASP.NET Core 3,1 oferuje dodatkową pomoc techniczną SameSite.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="5a6f6-183">Zaplanowano włączenie programu [Chrome](https://chromestatus.com/feature/5088147346030592) domyślnie w [lutym 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="5a6f6-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="5a6f6-184">Przeglądarki zaczynają przechodzenie do tego standardu w 2019.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="5a6f6-185">Interfejsy API, na które wpływem zmiany z standardu 2016 SameSite Draft do wersji Standard 2019</span><span class="sxs-lookup"><span data-stu-id="5a6f6-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="5a6f6-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5a6f6-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="5a6f6-187">CookieOptions.SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="5a6f6-188">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="5a6f6-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5a6f6-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="5a6f6-190">Obsługa starszych przeglądarek</span><span class="sxs-lookup"><span data-stu-id="5a6f6-190">Supporting older browsers</span></span>

<span data-ttu-id="5a6f6-191">Standard 2016 SameSite, że nieznane wartości muszą być traktowane jako wartości `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="5a6f6-192">Aplikacje dostępne ze starszych przeglądarek, które obsługują Standard 2016 SameSite, mogą ulec przerwie, gdy pobierają Właściwość SameSite o wartości `None`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="5a6f6-193">Aplikacje sieci Web muszą implementować wykrywanie przeglądarki, jeśli chcą obsługiwać starsze przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="5a6f6-194">ASP.NET Core nie implementuje wykrywania przeglądarki, ponieważ wartości agentów użytkownika są bardzo nietrwałe i często zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="5a6f6-195">Punkt rozszerzenia w <xref:Microsoft.AspNetCore.CookiePolicy> umożliwia podłączanie w logice określonej przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="5a6f6-196">W `Startup.Configure`Dodaj kod, który wywołuje <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> lub *dowolnej* metody, która zapisuje pliki cookie:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="5a6f6-197">W `Startup.ConfigureServices`Dodaj kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="5a6f6-198">W powyższym przykładzie `MyUserAgentDetectionLib.DisallowsSameSiteNone` jest biblioteką dostarczoną przez użytkownika, która wykrywa, czy agent użytkownika nie obsługuje SameSite `None`:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="5a6f6-199">Poniższy kod przedstawia przykładową metodę `DisallowsSameSiteNone`:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="5a6f6-200">Poniższy kod służy tylko do celów demonstracyjnych:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="5a6f6-201">Nie powinna być uważana za ukończoną.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="5a6f6-202">Nie jest on obsługiwany ani wspierany.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="5a6f6-203">Testowanie aplikacji pod kątem problemów z SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="5a6f6-204">Aplikacje, które współdziałają z witrynami zdalnymi, takie jak logowanie innych firm, muszą:</span><span class="sxs-lookup"><span data-stu-id="5a6f6-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="5a6f6-205">Przetestuj interakcję w wielu przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="5a6f6-206">Zastosuj [wykrywanie i środki zaradcze w przeglądarce CookiePolicy](#sob) omówione w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="5a6f6-207">Przetestuj aplikacje sieci Web przy użyciu wersji klienta, która może być zgodą na nowe zachowanie SameSite.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="5a6f6-208">Przeglądarki Chrome, Firefox i chrom Edge mają nowe flagi funkcji opt, których można użyć do testowania.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="5a6f6-209">Po zastosowaniu przez aplikację SameSite poprawek przetestuj ją ze starszymi wersjami klienta, szczególnie Safari.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="5a6f6-210">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="5a6f6-211">Testowanie za pomocą przeglądarki Chrome</span><span class="sxs-lookup"><span data-stu-id="5a6f6-211">Test with Chrome</span></span>

<span data-ttu-id="5a6f6-212">Chrome 78 + daje mylące wyniki, ponieważ ma tymczasowe środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="5a6f6-213">Chrom 78 + tymczasowe środki zaradcze dopuszczają pliki cookie mniejsze niż dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="5a6f6-214">Program Chrome 76 lub 77 z włączonymi odpowiednimi flagami testu zapewnia dokładniejsze wyniki.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="5a6f6-215">Aby przetestować nowe zachowanie SameSite, przełącz `chrome://flags/#same-site-by-default-cookies` na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="5a6f6-216">Starsze wersje programu Chrome (75 i poniżej) zostały zgłoszone w celu niepowodzenia z nowym ustawieniem `None`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="5a6f6-217">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="5a6f6-218">Firma Google nie udostępnia starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="5a6f6-219">Postępuj zgodnie z instrukcjami podanymi w części [pobieranie chromu](https://www.chromium.org/getting-involved/download-chromium) , aby przetestować starsze wersje programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="5a6f6-220">**Nie** Pobieraj programu Chrome z linków dostarczonych przez wyszukiwanie starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="5a6f6-221">Chrom 76 Win64</span><span class="sxs-lookup"><span data-stu-id="5a6f6-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="5a6f6-222">Chrom 74 Win64</span><span class="sxs-lookup"><span data-stu-id="5a6f6-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="5a6f6-223">Począwszy od wersji `80.0.3975.0`Kanaryjskich, można wyłączyć swobodny + POST tymczasowych środków zaradczych na potrzeby testowania przy użyciu nowej flagi `--enable-features=SameSiteDefaultChecksMethodRigorously`, aby umożliwić Testowanie witryn i usług w stanie końcowym funkcji, w której zostało usunięte.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="5a6f6-224">Aby uzyskać więcej informacji, zobacz artykuł dotyczący projektów chrom [SameSite Updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="5a6f6-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="5a6f6-225">Testowanie przy użyciu przeglądarki Safari</span><span class="sxs-lookup"><span data-stu-id="5a6f6-225">Test with Safari</span></span>

<span data-ttu-id="5a6f6-226">Przeglądarka Safari 12 ściśle wdrożyła poprzednią wersję roboczą i kończy się niepowodzeniem, gdy nowa wartość `None` jest w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="5a6f6-227">`None` jest możliwe za pośrednictwem kodu wykrywania przeglądarki [obsługującego starsze przeglądarki](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="5a6f6-228">Przetestuj logowanie za pomocą przeglądarki Safari 12, Safari 13 i WebKit opartej na programie MSAL, ADAL lub niezależnie od używanej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="5a6f6-229">Problem jest zależny od podstawowej wersji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="5a6f6-230">OSX Mojave (10,14) i iOS 12 są znane jako problemy ze zgodnością z nowym zachowaniem SameSite.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="5a6f6-231">Uaktualnienie systemu operacyjnego do wersji OSX Catalina (10,15) lub iOS 13 rozwiązuje problem.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="5a6f6-232">Przeglądarka Safari nie ma obecnie flagi zgody na testowanie nowych zachowań specyfikacji.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="5a6f6-233">Testowanie za pomocą przeglądarki Firefox</span><span class="sxs-lookup"><span data-stu-id="5a6f6-233">Test with Firefox</span></span>

<span data-ttu-id="5a6f6-234">Obsługę programu Firefox dla nowego standardu można przetestować w wersji 68 + przez wypróbowanie na stronie `about:config` z flagą funkcji `network.cookie.sameSite.laxByDefault`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="5a6f6-235">Nie zgłoszono problemów ze zgodnością ze starszymi wersjami programu Firefox.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="5a6f6-236">Testowanie przy użyciu przeglądarki Edge</span><span class="sxs-lookup"><span data-stu-id="5a6f6-236">Test with Edge browser</span></span>

<span data-ttu-id="5a6f6-237">Program Edge obsługuje stary Standard SameSite.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="5a6f6-238">Wersja brzegowa 44 nie ma żadnych znanych problemów ze zgodnością z nowym standardem.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="5a6f6-239">Testowanie przy użyciu krawędzi (chrom)</span><span class="sxs-lookup"><span data-stu-id="5a6f6-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="5a6f6-240">Flagi SameSite są ustawiane na stronie `edge://flags/#same-site-by-default-cookies`.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="5a6f6-241">Nie odnaleziono problemów ze zgodnością z funkcją chromu.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="5a6f6-242">Testuj przy użyciu elektronu</span><span class="sxs-lookup"><span data-stu-id="5a6f6-242">Test with Electron</span></span>

<span data-ttu-id="5a6f6-243">Wersje elektronów obejmują starsze wersje chromu.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="5a6f6-244">Na przykład wersja elektronów używana przez zespoły to chrom 66, który wykazuje starsze zachowanie.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="5a6f6-245">Należy przeprowadzić własne testy zgodności z wersją elektronów używaną przez produkt.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="5a6f6-246">Zapoznaj się z tematem [Obsługa starszych przeglądarek](#sob) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5a6f6-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a6f6-247">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5a6f6-247">Additional resources</span></span>

* [<span data-ttu-id="5a6f6-248">Blog chromu: deweloperzy: przygotowanie do nowego SameSite = none; Ustawienia bezpiecznego pliku cookie</span><span class="sxs-lookup"><span data-stu-id="5a6f6-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="5a6f6-249">Wyjaśniono pliki cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="5a6f6-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="5a6f6-250">Poprawki 2019 listopada</span><span class="sxs-lookup"><span data-stu-id="5a6f6-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="5a6f6-251">Sample</span><span class="sxs-lookup"><span data-stu-id="5a6f6-251">Sample</span></span>               | <span data-ttu-id="5a6f6-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="5a6f6-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5a6f6-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5a6f6-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="5a6f6-254">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5a6f6-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="5a6f6-255">Sample</span><span class="sxs-lookup"><span data-stu-id="5a6f6-255">Sample</span></span>               | <span data-ttu-id="5a6f6-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="5a6f6-256">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5a6f6-257">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5a6f6-257">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
