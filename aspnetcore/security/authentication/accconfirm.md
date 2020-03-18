---
title: Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 3a6b0501d507929c9929207a7bb871b3b81b7cb8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511629"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="ef1e8-103">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef1e8-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="ef1e8-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ef1e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ef1e8-105">W tym samouczku pokazano, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="ef1e8-106">Ten samouczek **nie** jest tematem początkowym.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="ef1e8-107">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-107">You should be familiar with:</span></span>

* [<span data-ttu-id="ef1e8-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef1e8-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ef1e8-109">Uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="ef1e8-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ef1e8-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ef1e8-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ef1e8-111">Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) dla wersji ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="ef1e8-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ef1e8-112">Prerequisites</span></span>

[<span data-ttu-id="ef1e8-113">.NET Core 3,0 SDK lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ef1e8-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="ef1e8-114">Tworzenie i testowanie aplikacji sieci Web przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ef1e8-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="ef1e8-115">Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="ef1e8-116">Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ef1e8-117">Po zarejestrowaniu nastąpi przekierowanie do strony programu do `/Identity/Account/RegisterConfirmation`, która zawiera link umożliwiający zasymulowanie potwierdzenia wiadomości e-mail:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="ef1e8-118">Wybierz łącze `Click here to confirm your account`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="ef1e8-119">Wybierz łącze **logowania** i zaloguj się przy użyciu tych samych poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="ef1e8-120">Wybierz łącze `Hello YourEmail@provider.com!`, które przekierowuje Cię do strony `/Identity/Account/Manage/PersonalData`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="ef1e8-121">Wybierz kartę **dane osobowe** po lewej stronie, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="ef1e8-122">Konfigurowanie dostawcy poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-122">Configure an email provider</span></span>

<span data-ttu-id="ef1e8-123">W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ef1e8-124">Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ef1e8-125">Możesz użyć innych dostawców poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-125">You can use other email providers.</span></span> <span data-ttu-id="ef1e8-126">Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ef1e8-127">Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ef1e8-128">Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ef1e8-129">Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ef1e8-130">Konfigurowanie kluczy tajnych użytkownika SendGrid</span><span class="sxs-lookup"><span data-stu-id="ef1e8-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ef1e8-131">Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ef1e8-132">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ef1e8-133">W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w pliku Secret *. JSON* w katalogu `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ef1e8-134">Zawartość pliku Secret *. JSON* nie jest zaszyfrowana.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ef1e8-135">W poniższym znaczniku przedstawiono plik Secret *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ef1e8-136">Wartość `SendGridKey` została usunięta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ef1e8-137">Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ef1e8-138">Zainstaluj SendGrid</span><span class="sxs-lookup"><span data-stu-id="ef1e8-138">Install SendGrid</span></span>

<span data-ttu-id="ef1e8-139">W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ef1e8-140">Zainstaluj pakiet NuGet `SendGrid`:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef1e8-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef1e8-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef1e8-142">W konsoli Menedżera pakietów wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ef1e8-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ef1e8-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ef1e8-144">W konsoli programu wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ef1e8-145">Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ef1e8-146">Implementuj IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ef1e8-146">Implement IEmailSender</span></span>

<span data-ttu-id="ef1e8-147">Aby zaimplementować `IEmailSender`, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ef1e8-148">Konfigurowanie uruchamiania do obsługi poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-148">Configure startup to support email</span></span>

<span data-ttu-id="ef1e8-149">Dodaj następujący kod do metody `ConfigureServices` w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="ef1e8-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ef1e8-150">Dodaj `EmailSender` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ef1e8-151">Zarejestruj wystąpienie konfiguracji `AuthMessageSenderOptions`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ef1e8-152">Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła</span><span class="sxs-lookup"><span data-stu-id="ef1e8-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ef1e8-153">Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ef1e8-154">Uruchom aplikację i zarejestrować nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="ef1e8-154">Run the app and register a new user</span></span>
* <span data-ttu-id="ef1e8-155">Sprawdź wiadomość e-mail z linkiem potwierdzenia konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ef1e8-156">Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ef1e8-157">Kliknij link, aby potwierdzić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ef1e8-158">Zaloguj się przy użyciu adresu e-mail i hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="ef1e8-159">Wyloguj się.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ef1e8-160">Resetowanie hasła do testu</span><span class="sxs-lookup"><span data-stu-id="ef1e8-160">Test password reset</span></span>

* <span data-ttu-id="ef1e8-161">Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj**.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ef1e8-162">Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**</span><span class="sxs-lookup"><span data-stu-id="ef1e8-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ef1e8-163">Wprowadź adres e-mail, który został użyty do zarejestrowania konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ef1e8-164">Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ef1e8-165">Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ef1e8-166">Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ef1e8-167">Zmień limit czasu wiadomości e-mail i aktywności</span><span class="sxs-lookup"><span data-stu-id="ef1e8-167">Change email and activity timeout</span></span>

<span data-ttu-id="ef1e8-168">Domyślny limit czasu braku aktywności wynosi 14 dni.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ef1e8-169">Następujący kod ustawia limit czasu braku aktywności na 5 dni:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ef1e8-170">Zmień wszystkie lifespans tokenów ochrony danych</span><span class="sxs-lookup"><span data-stu-id="ef1e8-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="ef1e8-171">Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="ef1e8-172">Wbudowane tokeny użytkownika tożsamości (zobacz [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ef1e8-173">Zmień cykl życia tokenu poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-173">Change the email token lifespan</span></span>

<span data-ttu-id="ef1e8-174">Domyślny token cykl życia tokenów [użytkownika tożsamości](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ef1e8-175">W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ef1e8-176">Dodaj niestandardową [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ef1e8-177">Dodaj dostawcę niestandardowego do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ef1e8-178">Potwierdzenie ponownego wysłania wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-178">Resend email confirmation</span></span>

<span data-ttu-id="ef1e8-179">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5410)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ef1e8-180">Debuguj wiadomość e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-180">Debug email</span></span>

<span data-ttu-id="ef1e8-181">Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-181">If you can't get email working:</span></span>

* <span data-ttu-id="ef1e8-182">Ustaw punkt przerwania w `EmailSender.Execute`, aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ef1e8-183">Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ef1e8-184">Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ef1e8-185">Sprawdź swój folder spamu.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-185">Check your spam folder.</span></span>
* <span data-ttu-id="ef1e8-186">Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)</span><span class="sxs-lookup"><span data-stu-id="ef1e8-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ef1e8-187">Spróbuj wysłać konto do innych kont e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="ef1e8-188">**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ef1e8-189">Jeśli opublikujesz aplikację na platformie Azure, ustaw wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ef1e8-190">System konfiguracji jest skonfigurowany do odczytu klucze ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ef1e8-191">Łączenie kont logowania społecznościowych i lokalnych</span><span class="sxs-lookup"><span data-stu-id="ef1e8-191">Combine social and local login accounts</span></span>

<span data-ttu-id="ef1e8-192">Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ef1e8-193">Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ef1e8-194">Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ef1e8-195">W następującej kolejności "RickAndMSFT@gmail.com" jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

<span data-ttu-id="ef1e8-197">Kliknij link **Zarządzaj** .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-197">Click on the **Manage** link.</span></span> <span data-ttu-id="ef1e8-198">Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-198">Note the 0 external (social logins) associated with this account.</span></span>

![Zarządzaj widokiem](accconfirm/_static/manage.png)

<span data-ttu-id="ef1e8-200">Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ef1e8-201">Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-201">In the following image, Facebook is the external authentication provider:</span></span>

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="ef1e8-203">Te dwa konta zostały połączone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-203">The two accounts have been combined.</span></span> <span data-ttu-id="ef1e8-204">Możesz zalogować się przy użyciu dowolnego konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-204">You are able to sign in with either account.</span></span> <span data-ttu-id="ef1e8-205">Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ef1e8-206">Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy</span><span class="sxs-lookup"><span data-stu-id="ef1e8-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ef1e8-207">Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ef1e8-208">Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ef1e8-209">Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ef1e8-210">Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ef1e8-211">Potwierdź istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-211">Confirm existing users.</span></span> <span data-ttu-id="ef1e8-212">Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ef1e8-213">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ef1e8-213">Prerequisites</span></span>

[<span data-ttu-id="ef1e8-214">.NET Core 2,2 SDK lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ef1e8-214">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="ef1e8-215">Tworzenie aplikacji sieci Web i tożsamości szkieletu</span><span class="sxs-lookup"><span data-stu-id="ef1e8-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="ef1e8-216">Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="ef1e8-217">Testowanie rejestracji nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="ef1e8-217">Test new user registration</span></span>

<span data-ttu-id="ef1e8-218">Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ef1e8-219">W tym momencie jedynym sprawdzaniem poprawności w wiadomości e-mail jest atrybut [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="ef1e8-220">Po przesłaniu rejestracji nastąpi zalogowanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="ef1e8-221">W dalszej części tego samouczka kod zostanie zaktualizowany, więc nowi użytkownicy nie będą mogli się zalogować do momentu zweryfikowania ich poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="ef1e8-222">Zwróć uwagę na to, że pole `EmailConfirmed` tabeli jest `False`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="ef1e8-223">Możesz chcieć ponownie użyć tej wiadomości e-mail w następnym kroku, gdy aplikacja wyśle wiadomość e-mail z potwierdzeniem.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="ef1e8-224">Kliknij prawym przyciskiem myszy wiersz i wybierz polecenie **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="ef1e8-225">Usunięcie aliasu e-mail ułatwia wykonanie następujących czynności.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="ef1e8-226">Żądaj potwierdzenia wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-226">Require email confirmation</span></span>

<span data-ttu-id="ef1e8-227">Najlepszym rozwiązaniem jest potwierdzenie wiadomości e-mail o nowej rejestracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="ef1e8-228">Potwierdzenie poczty e-mail pomaga sprawdzić, czy nie personifikują kogoś innego (oznacza to, że nie zostały zarejestrowane w wiadomości e-mail innej osoby).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="ef1e8-229">Załóżmy, że masz forum dyskusyjne i chcesz zapobiec rejestrowaniu się "yli@example.com" jako "nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="ef1e8-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="ef1e8-230">Bez potwierdzenia wiadomości e-mail "nolivetto@contoso.com" może odbierać niechciane wiadomości e-mail z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="ef1e8-231">Załóżmy, że użytkownik przypadkowo zarejestrował się jako "ylo@example.com" i nie błąd pisowni "yli".</span><span class="sxs-lookup"><span data-stu-id="ef1e8-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="ef1e8-232">Nie będą one mogły korzystać z odzyskiwania hasła, ponieważ aplikacja nie ma poprawnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="ef1e8-233">Potwierdzenie poczty e-mail zapewnia ograniczoną ochronę z botów.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="ef1e8-234">Potwierdzenie poczty e-mail nie zapewnia ochrony przed złośliwymi użytkownikami z wieloma kontami e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="ef1e8-235">Zazwyczaj chcesz uniemożliwić nowym użytkownikom ogłaszanie danych w witrynie sieci Web przed potwierdzeniem wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="ef1e8-236">Zaktualizuj `Startup.ConfigureServices`, aby wymagały potwierdzonej wiadomości e-mail:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="ef1e8-237">`config.SignIn.RequireConfirmedEmail = true;` uniemożliwia rejestrowanie zalogowanych użytkowników do momentu potwierdzenia ich wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="ef1e8-238">Konfigurowanie dostawcy poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-238">Configure email provider</span></span>

<span data-ttu-id="ef1e8-239">W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ef1e8-240">Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ef1e8-241">Możesz użyć innych dostawców poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-241">You can use other email providers.</span></span> <span data-ttu-id="ef1e8-242">ASP.NET Core 2. x zawiera `System.Net.Mail`, co umożliwia wysyłanie wiadomości e-mail z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="ef1e8-243">Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ef1e8-244">Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ef1e8-245">Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ef1e8-246">Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ef1e8-247">Konfigurowanie kluczy tajnych użytkownika SendGrid</span><span class="sxs-lookup"><span data-stu-id="ef1e8-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ef1e8-248">Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ef1e8-249">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ef1e8-250">W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w pliku Secret *. JSON* w katalogu `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ef1e8-251">Zawartość pliku Secret *. JSON* nie jest zaszyfrowana.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ef1e8-252">W poniższym znaczniku przedstawiono plik Secret *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ef1e8-253">Wartość `SendGridKey` została usunięta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ef1e8-254">Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ef1e8-255">Zainstaluj SendGrid</span><span class="sxs-lookup"><span data-stu-id="ef1e8-255">Install SendGrid</span></span>

<span data-ttu-id="ef1e8-256">W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ef1e8-257">Zainstaluj pakiet NuGet `SendGrid`:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef1e8-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef1e8-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef1e8-259">W konsoli Menedżera pakietów wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ef1e8-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ef1e8-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ef1e8-261">W konsoli programu wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ef1e8-262">Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ef1e8-263">Implementuj IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ef1e8-263">Implement IEmailSender</span></span>

<span data-ttu-id="ef1e8-264">Aby zaimplementować `IEmailSender`, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ef1e8-265">Konfigurowanie uruchamiania do obsługi poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-265">Configure startup to support email</span></span>

<span data-ttu-id="ef1e8-266">Dodaj następujący kod do metody `ConfigureServices` w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="ef1e8-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ef1e8-267">Dodaj `EmailSender` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ef1e8-268">Zarejestruj wystąpienie konfiguracji `AuthMessageSenderOptions`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="ef1e8-269">Włącz potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="ef1e8-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="ef1e8-270">Szablon zawiera kod służący do potwierdzenia konta i odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="ef1e8-271">Znajdź metodę `OnPostAsync` w *obszarach/Identity/Pages/Account/register. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="ef1e8-272">Zablokuj automatyczne logowanie nowo zarejestrowanych użytkowników, dodając komentarz do następującego wiersza:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="ef1e8-273">Pełna metoda jest pokazywana z wyróżnioną zmienionym wierszem:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ef1e8-274">Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła</span><span class="sxs-lookup"><span data-stu-id="ef1e8-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ef1e8-275">Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ef1e8-276">Uruchom aplikację i zarejestrować nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="ef1e8-276">Run the app and register a new user</span></span>
* <span data-ttu-id="ef1e8-277">Sprawdź wiadomość e-mail z linkiem potwierdzenia konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ef1e8-278">Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ef1e8-279">Kliknij link, aby potwierdzić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ef1e8-280">Zaloguj się przy użyciu adresu e-mail i hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="ef1e8-281">Wyloguj się.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="ef1e8-282">Wyświetlanie strony zarządzania</span><span class="sxs-lookup"><span data-stu-id="ef1e8-282">View the manage page</span></span>

<span data-ttu-id="ef1e8-283">Wybierz swoją nazwę użytkownika w przeglądarce: ![okno przeglądarki z nazwą użytkownika](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="ef1e8-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="ef1e8-284">Zostanie wyświetlona strona zarządzanie z wybraną kartą **profil** .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="ef1e8-285">**Wiadomość e-mail** zawiera pole wyboru informujące o potwierdzeniu wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ef1e8-286">Resetowanie hasła do testu</span><span class="sxs-lookup"><span data-stu-id="ef1e8-286">Test password reset</span></span>

* <span data-ttu-id="ef1e8-287">Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj**.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ef1e8-288">Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**</span><span class="sxs-lookup"><span data-stu-id="ef1e8-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ef1e8-289">Wprowadź adres e-mail, który został użyty do zarejestrowania konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ef1e8-290">Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ef1e8-291">Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ef1e8-292">Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ef1e8-293">Zmień limit czasu wiadomości e-mail i aktywności</span><span class="sxs-lookup"><span data-stu-id="ef1e8-293">Change email and activity timeout</span></span>

<span data-ttu-id="ef1e8-294">Domyślny limit czasu braku aktywności wynosi 14 dni.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ef1e8-295">Następujący kod ustawia limit czasu braku aktywności na 5 dni:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ef1e8-296">Zmień wszystkie lifespans tokenów ochrony danych</span><span class="sxs-lookup"><span data-stu-id="ef1e8-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="ef1e8-297">Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="ef1e8-298">Wbudowane tokeny użytkownika tożsamości (zobacz [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ef1e8-299">Zmień cykl życia tokenu poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-299">Change the email token lifespan</span></span>

<span data-ttu-id="ef1e8-300">Domyślny token cykl życia tokenów [użytkownika tożsamości](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ef1e8-301">W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ef1e8-302">Dodaj niestandardową [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ef1e8-303">Dodaj dostawcę niestandardowego do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ef1e8-304">Potwierdzenie ponownego wysłania wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-304">Resend email confirmation</span></span>

<span data-ttu-id="ef1e8-305">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5410)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ef1e8-306">Debuguj wiadomość e-mail</span><span class="sxs-lookup"><span data-stu-id="ef1e8-306">Debug email</span></span>

<span data-ttu-id="ef1e8-307">Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-307">If you can't get email working:</span></span>

* <span data-ttu-id="ef1e8-308">Ustaw punkt przerwania w `EmailSender.Execute`, aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ef1e8-309">Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ef1e8-310">Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ef1e8-311">Sprawdź swój folder spamu.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-311">Check your spam folder.</span></span>
* <span data-ttu-id="ef1e8-312">Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)</span><span class="sxs-lookup"><span data-stu-id="ef1e8-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ef1e8-313">Spróbuj wysłać konto do innych kont e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="ef1e8-314">**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ef1e8-315">Jeśli opublikujesz aplikację na platformie Azure, możesz ustawić wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ef1e8-316">System konfiguracji jest skonfigurowany do odczytu klucze ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ef1e8-317">Łączenie kont logowania społecznościowych i lokalnych</span><span class="sxs-lookup"><span data-stu-id="ef1e8-317">Combine social and local login accounts</span></span>

<span data-ttu-id="ef1e8-318">Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ef1e8-319">Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ef1e8-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ef1e8-320">Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ef1e8-321">W następującej kolejności "RickAndMSFT@gmail.com" jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

<span data-ttu-id="ef1e8-323">Kliknij link **Zarządzaj** .</span><span class="sxs-lookup"><span data-stu-id="ef1e8-323">Click on the **Manage** link.</span></span> <span data-ttu-id="ef1e8-324">Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-324">Note the 0 external (social logins) associated with this account.</span></span>

![Zarządzaj widokiem](accconfirm/_static/manage.png)

<span data-ttu-id="ef1e8-326">Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ef1e8-327">Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-327">In the following image, Facebook is the external authentication provider:</span></span>

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="ef1e8-329">Te dwa konta zostały połączone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-329">The two accounts have been combined.</span></span> <span data-ttu-id="ef1e8-330">Możesz zalogować się przy użyciu dowolnego konta.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-330">You are able to sign in with either account.</span></span> <span data-ttu-id="ef1e8-331">Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ef1e8-332">Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy</span><span class="sxs-lookup"><span data-stu-id="ef1e8-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ef1e8-333">Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ef1e8-334">Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ef1e8-335">Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ef1e8-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ef1e8-336">Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ef1e8-337">Potwierdź istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-337">Confirm existing users.</span></span> <span data-ttu-id="ef1e8-338">Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.</span><span class="sxs-lookup"><span data-stu-id="ef1e8-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
