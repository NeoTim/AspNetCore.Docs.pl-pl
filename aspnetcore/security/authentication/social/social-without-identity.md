---
title: Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym bez tożsamości ASP.NET Core
author: rick-anderson
description: Wyjaśnienie dotyczące korzystania z usługi Facebook, Google, Twitter itp. i uwierzytelniania użytkownika konta bez tożsamości ASP.NET Core.
ms.author: riande
ms.date: 09/25/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 54dd93a13b2f7ed09c2c305f529d5f4610567184
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999900"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f14fe-103">Korzystanie z uwierzytelniania przy użyciu dostawcy logowania społecznego bez tożsamości ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f14fe-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f14fe-104"><xref:security/authentication/social/index> opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="f14fe-105">Podejście opisane w tym temacie obejmuje ASP.NET Core tożsamość jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="f14fe-106">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** tożsamości ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14fe-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="f14fe-107">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core tożsamość, ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="f14fe-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="f14fe-108">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="f14fe-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="f14fe-109">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="f14fe-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="f14fe-110">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="f14fe-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="f14fe-111">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="f14fe-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f14fe-112">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="f14fe-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f14fe-113">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="f14fe-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f14fe-114">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="f14fe-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="f14fe-115">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="f14fe-115">Configuration</span></span>

<span data-ttu-id="f14fe-116">W metodzie `ConfigureServices` Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> i <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="f14fe-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet1)]

<span data-ttu-id="f14fe-117">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ustawia <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f14fe-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="f14fe-118">@No__t-0 jest domyślnym schematem używanym przez następujące metody rozszerzenia uwierzytelniania `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="f14fe-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="f14fe-119">Ustawienie @no__t aplikacji-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") umożliwia skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="f14fe-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="f14fe-120">Ustawienie @no__t aplikacji-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do używania Google jako domyślnego schematu dla wywołań do `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f14fe-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="f14fe-121">`DefaultChallengeScheme` zastępuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="f14fe-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="f14fe-122">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>, aby uzyskać dodatkowe właściwości, które zastępują `DefaultScheme` po ustawieniu.</span><span class="sxs-lookup"><span data-stu-id="f14fe-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="f14fe-123">W `Startup.Configure` Wywołaj `UseAuthentication` i `UseAuthorization`, aby ustawić właściwość `HttpContext.User` i uruchomić oprogramowanie pośredniczące autoryzacji dla żądań.</span><span class="sxs-lookup"><span data-stu-id="f14fe-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="f14fe-124">Wywołaj metody `UseAuthentication` i `UseAuthorization` przed wywołaniem `UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="f14fe-124">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet2)]

<span data-ttu-id="f14fe-125">Aby dowiedzieć się więcej o schematach uwierzytelniania i uwierzytelnianiu plików cookie, zobacz <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="f14fe-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="f14fe-126">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="f14fe-126">Apply authorization</span></span>

<span data-ttu-id="f14fe-127">Przetestuj konfigurację uwierzytelniania aplikacji, stosując atrybut `AuthorizeAttribute` do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="f14fe-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="f14fe-128">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="f14fe-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="f14fe-129">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="f14fe-129">Sign out</span></span>

<span data-ttu-id="f14fe-130">Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="f14fe-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="f14fe-131">Poniższy kod dodaje procedurę obsługi strony `Logout` do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="f14fe-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Pages/Index.cshtml.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="f14fe-132">Zwróć uwagę, że wywołanie `SignOutAsync` nie określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="f14fe-133">Wartość `DefaultScheme` z `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="f14fe-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f14fe-134">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f14fe-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f14fe-135"><xref:security/authentication/social/index> opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="f14fe-136">Podejście opisane w tym temacie obejmuje ASP.NET Core tożsamość jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="f14fe-137">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** tożsamości ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14fe-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="f14fe-138">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core tożsamość, ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="f14fe-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="f14fe-139">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="f14fe-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="f14fe-140">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="f14fe-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="f14fe-141">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="f14fe-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="f14fe-142">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="f14fe-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f14fe-143">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="f14fe-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f14fe-144">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="f14fe-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f14fe-145">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="f14fe-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="f14fe-146">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="f14fe-146">Configuration</span></span>

<span data-ttu-id="f14fe-147">W metodzie `ConfigureServices` Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu metod `AddAuthentication`, `AddCookie` i `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="f14fe-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="f14fe-148">Wywołanie metody [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) ustawia [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f14fe-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="f14fe-149">@No__t-0 jest domyślnym schematem używanym przez następujące metody rozszerzenia uwierzytelniania `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="f14fe-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="f14fe-150">Ustawienie @no__t aplikacji-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") umożliwia skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="f14fe-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="f14fe-151">Ustawienie @no__t aplikacji-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do używania Google jako domyślnego schematu dla wywołań do `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f14fe-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="f14fe-152">`DefaultChallengeScheme` zastępuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="f14fe-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="f14fe-153">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>, aby uzyskać dodatkowe właściwości, które zastępują `DefaultScheme` po ustawieniu.</span><span class="sxs-lookup"><span data-stu-id="f14fe-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="f14fe-154">W metodzie `Configure` Wywołaj metodę `UseAuthentication`, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia właściwość `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="f14fe-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f14fe-155">Wywołaj metodę `UseAuthentication` przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="f14fe-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="f14fe-156">Aby dowiedzieć się więcej o schematach uwierzytelniania i uwierzytelnianiu plików cookie, zobacz <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="f14fe-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="f14fe-157">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="f14fe-157">Apply authorization</span></span>

<span data-ttu-id="f14fe-158">Przetestuj konfigurację uwierzytelniania aplikacji, stosując atrybut `AuthorizeAttribute` do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="f14fe-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="f14fe-159">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="f14fe-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="f14fe-160">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="f14fe-160">Sign out</span></span>

<span data-ttu-id="f14fe-161">Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="f14fe-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="f14fe-162">Poniższy kod dodaje procedurę obsługi strony `Logout` do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="f14fe-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="f14fe-163">Zwróć uwagę, że wywołanie `SignOutAsync` nie określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f14fe-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="f14fe-164">Wartość `DefaultScheme` z `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="f14fe-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f14fe-165">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f14fe-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
