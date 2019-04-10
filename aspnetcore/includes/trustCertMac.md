---
ms.openlocfilehash: 2ec079606cb48670dbc3852482fd8d401e7db44b
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472297"
---
* <span data-ttu-id="e971f-101">Zaufanie certyfikatu deweloperskiego HTTPS, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e971f-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="e971f-102">Poprzednie polecenie wyświetla następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="e971f-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="e971f-103">Jeśli zostanie wyświetlony monit, wprowadź nazwę i hasło administratora.</span><span class="sxs-lookup"><span data-stu-id="e971f-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="e971f-104">Certyfikat zostanie teraz zainstalowany i zaufany.</span><span class="sxs-lookup"><span data-stu-id="e971f-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="e971f-105">Zobacz [ufać certyfikatowi rozwoju platformy ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="e971f-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>