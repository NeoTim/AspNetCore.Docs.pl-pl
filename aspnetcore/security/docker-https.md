---
title: Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS
author: rick-anderson
description: Dowiedz się, jak hostować ASP.NET Core obrazy za pomocą platformy Docker za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: 2f338e8883ca926c0f9a7ab339f58b088151cc87
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089204"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core [domyślnie używa protokołu HTTPS](/aspnet/core/security/enforcing-ssl). [Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) do zaufania, tożsamości i szyfrowania.

W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.

Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) na potrzeby scenariuszy programistycznych.

Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://www.microsoft.com/net/download) lub nowszy.

## <a name="certificates"></a>Certyfikaty

Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny. [Let's Encrypt](https://letsencrypt.org/) to urząd certyfikacji, który oferuje bezpłatne certyfikaty.

W tym dokumencie są stosowane [Certyfikaty deweloperskie z](https://en.wikipedia.org/wiki/Self-signed_certificate) podpisem własnym na potrzeby udostępniania wstępnie utworzonych obrazów przez `localhost`. Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.

Dla certyfikatów produkcyjnych:

* Narzędzie `dotnet dev-certs` nie jest wymagane.
* Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach. Każda lokalizacja powinna funkcjonować, chociaż przechowywanie certyfikatów w katalogu witryn nie jest zalecane.

Instrukcje zawarte w poniższej sekcji dotyczą instalowania certyfikatów w kontenerach za pomocą opcji wiersza polecenia `-v` Docker. Można dodać certyfikaty do obrazów kontenerów za pomocą polecenia `COPY` w *pliku dockerfile*, ale nie jest to zalecane. Nie zaleca się kopiowania certyfikatów do obrazu z następujących powodów:

* Trudno jest użyć tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.
* Trudno jest użyć tego samego obrazu do hostowania z certyfikatami produkcyjnymi.
* Istnieje duże ryzyko ujawnienia certyfikatu.

## <a name="running-pre-built-container-images-with-https"></a>Uruchamianie wstępnie skompilowanych obrazów kontenerów za pomocą protokołu HTTPS

Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.

### <a name="windows-using-linux-containers"></a>System Windows korzystający z kontenerów systemu Linux

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach Zastąp `{ password here }` hasłem.

Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Hasło musi być zgodne z hasłem użytym dla certyfikatu.

### <a name="macos-or-linux"></a>macOS lub Linux

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` jest obsługiwana tylko w systemach macOS i Windows. Certyfikaty w systemie Linux muszą być zaufane w sposób, który jest obsługiwany przez dystrybucji. Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.

W poprzednich poleceniach Zastąp `{ password here }` hasłem.

Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Hasło musi być zgodne z hasłem użytym dla certyfikatu.

### <a name="windows-using-windows-containers"></a>Windows przy użyciu kontenerów systemu Windows

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach Zastąp `{ password here }` hasłem.

Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Hasło musi być zgodne z hasłem użytym dla certyfikatu.
