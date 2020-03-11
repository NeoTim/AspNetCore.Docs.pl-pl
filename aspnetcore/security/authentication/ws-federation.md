---
title: Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core
author: chlowell
description: W tym samouczku pokazano, jak używać usługi WS-Federation w aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655429"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="23418-103">Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23418-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="23418-104">W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się przy użyciu dostawcy uwierzytelniania WS-Federation, takiego jak Active Directory Federation Services (ADFS) lub [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="23418-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="23418-105">Używa przykładowej aplikacji ASP.NET Core 2,0 opisanej w temacie [uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="23418-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="23418-106">W przypadku aplikacji ASP.NET Core 2,0 obsługa protokołu WS-Federation jest zapewniana przez [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="23418-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="23418-107">Ten składnik jest przewoźny z [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) i udostępnia wiele elementów tego Mechanics.</span><span class="sxs-lookup"><span data-stu-id="23418-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="23418-108">Składniki te są jednak różne na kilka ważnych sposobów.</span><span class="sxs-lookup"><span data-stu-id="23418-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="23418-109">Domyślnie nowe oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="23418-109">By default, the new middleware:</span></span>

* <span data-ttu-id="23418-110">Nie zezwala na nieżądane nazwy logowania.</span><span class="sxs-lookup"><span data-stu-id="23418-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="23418-111">Ta funkcja protokołu WS-Federation jest narażona na ataki XSRF.</span><span class="sxs-lookup"><span data-stu-id="23418-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="23418-112">Można go jednak włączyć przy użyciu opcji `AllowUnsolicitedLogins`.</span><span class="sxs-lookup"><span data-stu-id="23418-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="23418-113">Nie sprawdza każdego wpisu w formularzu dla wiadomości logowania.</span><span class="sxs-lookup"><span data-stu-id="23418-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="23418-114">Tylko żądania do `CallbackPath` są sprawdzane pod kątem logowań. `CallbackPath` domyślnie `/signin-wsfed`, ale można je zmienić za pomocą dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="23418-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="23418-115">Ta ścieżka może być współużytkowana z innymi dostawcami uwierzytelniania przez włączenie opcji [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="23418-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="23418-116">Zarejestruj aplikację w Active Directory</span><span class="sxs-lookup"><span data-stu-id="23418-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="23418-117">Usługi Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="23418-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="23418-118">Otwórz **Kreatora dodawania zaufania jednostki uzależnionej** serwera z konsoli zarządzania usług AD FS:</span><span class="sxs-lookup"><span data-stu-id="23418-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Witamy](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="23418-120">Wybierz, aby ręcznie wprowadzić dane:</span><span class="sxs-lookup"><span data-stu-id="23418-120">Choose to enter data manually:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Wybieranie źródła danych](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="23418-122">Wprowadź nazwę wyświetlaną jednostki uzależnionej.</span><span class="sxs-lookup"><span data-stu-id="23418-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="23418-123">Nazwa nie jest ważna dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23418-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="23418-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) nie obsługuje szyfrowania tokenów, dlatego nie należy konfigurować certyfikatu szyfrowania tokenu:</span><span class="sxs-lookup"><span data-stu-id="23418-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie certyfikatu](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="23418-126">Włącz obsługę protokołu pasywnego usługi WS-Federation przy użyciu adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23418-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="23418-127">Sprawdź, czy port jest prawidłowy dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="23418-127">Verify the port is correct for the app:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie adresu URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="23418-129">Musi to być adres URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="23418-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="23418-130">IIS Express może podać certyfikat z podpisem własnym podczas udostępniania aplikacji podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="23418-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="23418-131">Kestrel wymaga ręcznej konfiguracji certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="23418-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="23418-132">Aby uzyskać więcej informacji, zobacz [dokumentację Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="23418-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="23418-133">Kliknij przycisk **dalej** w pozostałej części kreatora i **Zamknij** na końcu.</span><span class="sxs-lookup"><span data-stu-id="23418-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="23418-134">Tożsamość ASP.NET Core wymaga podania **identyfikatora nazwy** .</span><span class="sxs-lookup"><span data-stu-id="23418-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="23418-135">Dodaj jedną z okna dialogowego **Edytowanie reguł dotyczących roszczeń** :</span><span class="sxs-lookup"><span data-stu-id="23418-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Edytuj reguły dotyczące oświadczeń](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="23418-137">W **Kreatorze dodawania reguły przekształcania oświadczeń**pozostaw zaznaczone pole wyboru domyślne **Wysyłaj atrybuty LDAP as** , a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23418-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="23418-138">Dodaj mapowanie reguły atrybut LDAP **nazwa konta sam** do żądania wychodzącego **Identyfikator nazwy** :</span><span class="sxs-lookup"><span data-stu-id="23418-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Kreator dodawania reguły przekształcania roszczeń: Konfigurowanie reguły dotyczącej roszczeń](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="23418-140">Kliknij przycisk **zakończ** > **OK** w oknie **Edytowanie reguł roszczeń** .</span><span class="sxs-lookup"><span data-stu-id="23418-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="23418-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="23418-141">Azure Active Directory</span></span>

* <span data-ttu-id="23418-142">Przejdź do bloku rejestracje aplikacji dzierżawy usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="23418-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="23418-143">Kliknij pozycję **rejestracja nowej aplikacji**:</span><span class="sxs-lookup"><span data-stu-id="23418-143">Click **New application registration**:</span></span>

![Azure Active Directory: Rejestracje aplikacji](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="23418-145">Wprowadź nazwę rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23418-145">Enter a name for the app registration.</span></span> <span data-ttu-id="23418-146">Nie ma to znaczenia dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23418-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="23418-147">Wprowadź adres URL, na który aplikacja nasłuchuje jako **adres URL logowania**:</span><span class="sxs-lookup"><span data-stu-id="23418-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: Utwórz rejestrację aplikacji](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="23418-149">Kliknij pozycję **punkty końcowe** i Zanotuj adres URL **dokumentu metadanych Federacji** .</span><span class="sxs-lookup"><span data-stu-id="23418-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="23418-150">Jest to `MetadataAddress`oprogramowania pośredniczącego usługi WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="23418-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: punkty końcowe](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="23418-152">Przejdź do rejestracji nowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23418-152">Navigate to the new app registration.</span></span> <span data-ttu-id="23418-153">Kliknij pozycję **ustawienia** > **Właściwości** i zanotuj **Identyfikator URI aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="23418-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="23418-154">Jest to `Wtrealm`oprogramowania pośredniczącego usługi WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="23418-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: właściwości rejestracji aplikacji](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="23418-156">Korzystanie z usługi WS-Federation bez tożsamości ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23418-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="23418-157">Oprogramowanie pośredniczące WS-Federation może być używane bez tożsamości.</span><span class="sxs-lookup"><span data-stu-id="23418-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="23418-158">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="23418-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="23418-159">Dodawanie protokołu WS-Federation jako dostawcy logowania zewnętrznego dla tożsamości ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23418-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="23418-160">Dodaj zależność od elementu [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) do projektu.</span><span class="sxs-lookup"><span data-stu-id="23418-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="23418-161">Dodaj usługę WS-Federation do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="23418-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="23418-162">Logowanie za pomocą usługi WS-Federation</span><span class="sxs-lookup"><span data-stu-id="23418-162">Log in with WS-Federation</span></span>

<span data-ttu-id="23418-163">Przejdź do aplikacji, a następnie kliknij link **Zaloguj** w nagłówku nawigacji.</span><span class="sxs-lookup"><span data-stu-id="23418-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="23418-164">Istnieje możliwość zalogowania się za pomocą WsFederation: ![log na stronie](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="23418-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="23418-165">Za pomocą usług ADFS jako dostawca przycisk przekieruje się do strony logowania usług ADFS: ![strony logowania usług ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="23418-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="23418-166">Za pomocą Azure Active Directory jako dostawca przycisk przekierowuje do strony logowania usługi AAD: ![stronie logowania w usłudze AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="23418-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="23418-167">Pomyślne logowanie do nowego użytkownika przekieruje się do strony rejestracji użytkownika aplikacji: ![rejestracji strony](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="23418-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>