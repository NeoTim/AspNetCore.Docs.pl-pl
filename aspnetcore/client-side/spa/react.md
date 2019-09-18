---
title: Użyj szablonu projektu reagowanie z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu aplikacji jednostronicowej (SPA) ASP.NET Core na potrzeby reakcji i tworzenia aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 0e61c5b3e31a0b050d356b8f8e16306dc1e2a7f3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080415"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Użyj szablonu projektu reagowanie z ASP.NET Core

Zaktualizowany szablon projektu dotyczącego reakcji umożliwia korzystanie z dogodnych punktów początkowych dla ASP.NET Core aplikacji przy użyciu konwencji reagowania i [tworzenia aplikacji](https://github.com/facebookincubator/create-react-app) (CRA) w celu zaimplementowania rozbudowanego interfejsu użytkownika po stronie klienta.

Szablon jest równoznaczny z tworzeniem projektu ASP.NET Core, który działa jako zaplecze interfejsu API, a standardowy projekt reakcji CRA reaguje na działanie jako interfejs użytkownika, ale z wygodą hostingu zarówno w jednym projekcie aplikacji, który można skompilować i opublikować jako pojedynczą jednostkę.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

Jeśli zainstalowano ASP.NET Core 2,1, nie ma potrzeby instalowania szablonu projektu z reagowaniem.

Utwórz nowy projekt z wiersza polecenia przy użyciu polecenia `dotnet new react` w pustym katalogu. Na przykład następujące polecenia tworzą aplikację w katalogu *My-New-App* i przełączają się do tego katalogu:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Uruchom aplikację z poziomu programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Otwórz wygenerowany plik *csproj* i uruchom aplikację w zwykły sposób.

Proces kompilacji przywraca zależności npm w pierwszym przebiegu, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Upewnij się, że masz zmienną środowiskową o `ASPNETCORE_Environment` nazwie `Development`z wartością. W systemie Windows (w komunikatach innych niż programu PowerShell `SET ASPNETCORE_Environment=Development`) Uruchom polecenie. W systemie Linux lub macOS Uruchom `export ASPNETCORE_Environment=Development`polecenie.

Uruchom [kompilację dotnet](/dotnet/core/tools/dotnet-build) , aby sprawdzić, czy aplikacja jest poprawnie skompilowana. W pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

Uruchom [uruchomienie programu dotnet](/dotnet/core/tools/dotnet-run) , aby uruchomić aplikację.

---

Szablon projektu tworzy aplikację ASP.NET Core i aplikację do reagowania. Aplikacja ASP.NET Core jest przeznaczona do użycia na potrzeby dostępu do danych, autoryzacji i innych zagadnień po stronie serwera. Aplikacja do reagowania znajdująca się w podkatalogu *ClientApp* jest przeznaczona do użycia w przypadku wszystkich problemów z interfejsem użytkownika.

## <a name="add-pages-images-styles-modules-etc"></a>Dodawanie stron, obrazów, stylów, modułów itp.

Katalog *ClientApp* jest standardową aplikacją do reagowania w CRA. Aby uzyskać więcej informacji, zobacz oficjalną [dokumentację CRA](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) .

Istnieją niewielkie różnice między aplikacją reakcji utworzoną przez ten szablon a tym utworzonym przez CRA. jednak możliwości aplikacji nie są zmieniane. Aplikacja utworzona przez szablon zawiera układ oparty na [Bootstrap](https://getbootstrap.com/)i podstawowy przykład routingu.

## <a name="install-npm-packages"></a>Zainstaluj pakiety npm

Aby zainstalować pakiety npm innych firm, należy użyć wiersza polecenia w podkatalogu *ClientApp* . Na przykład:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikowanie i wdrażanie

W trakcie opracowywania aplikacja jest uruchamiana w trybie zoptymalizowanym pod kątem wygody dla deweloperów. Na przykład pakiety JavaScript zawierają mapy źródłowe (w przypadku debugowania można zobaczyć oryginalny kod źródłowy). Aplikacja obserwuje zmiany plików JavaScript, HTML i CSS na dysku, a następnie automatycznie ponownie kompiluje i ładuje je, gdy zobaczy zmiany tych plików.

W środowisku produkcyjnym można korzystać z wersji aplikacji zoptymalizowanej pod kątem wydajności. To jest skonfigurowane tak, aby były wykonywane automatycznie. Podczas publikowania, konfiguracja kompilacji emituje zminimalizowanego, przenoszącą wbudowaną kompilację kodu po stronie klienta. W przeciwieństwie do kompilacji deweloperskiej kompilacja produkcyjna nie wymaga zainstalowania na serwerze programu Node. js.

Można użyć standardowych [ASP.NET Core metod hostingu i wdrażania](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Uruchom serwer CRA niezależnie

Projekt jest skonfigurowany tak, aby uruchamiał własne wystąpienie serwera CRA Development w tle podczas uruchamiania aplikacji ASP.NET Core w trybie tworzenia. Jest to wygodne, ponieważ oznacza to, że nie trzeba ręcznie uruchamiać oddzielnego serwera.

Istnieje zwrot do tej konfiguracji domyślnej. Za każdym razem, gdy C# modyfikujesz kod, a aplikacja ASP.NET Core wymaga ponownego uruchomienia, serwer CRA zostanie uruchomiony ponownie. Aby rozpocząć tworzenie kopii zapasowej, wymagane są kilka sekund. Jeśli wprowadzasz częste C# zmiany kodu i nie chcesz czekać na ponowne uruchomienie serwera CRA, uruchom serwer CRA na zewnątrz niezależnie od procesu ASP.NET Core. Aby to zrobić:

1. Dodaj plik *ENV* do podkatalogu *ClientApp* przy użyciu następującego ustawienia:

    ```
    BROWSER=none
    ```

    Uniemożliwi to otwieranie przeglądarki sieci Web podczas zewnętrznego uruchamiania serwera CRA.

2. W wierszu polecenia przejdź do podkatalogu *ClientApp* i uruchom serwer deweloperski CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Zmodyfikuj aplikację ASP.NET Core tak, aby korzystała z zewnętrznego wystąpienia serwera CRA, zamiast uruchamiania jednego z nich. W klasie *uruchomieniowej* Zastąp `spa.UseReactDevelopmentServer` wywołanie następującym:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Po uruchomieniu aplikacji ASP.NET Core nie zostanie uruchomiony serwer CRA. Wystąpienie, które zostało uruchomione ręcznie, jest używane w zamian. Pozwala to na szybkie uruchamianie i ponowne uruchamianie. Nie czeka już na odbudowanie aplikacji z reagowaniem za każdym razem.

> [!IMPORTANT]
> "Renderowanie po stronie serwera" nie jest obsługiwaną funkcją tego szablonu. Naszym celem tego szablonu jest spełnienie warunków dotyczących "Tworzenie — reagowanie aplikacji". W związku z tym scenariusze i funkcje, które nie są uwzględnione w projekcie "Create-rereagować na aplikacje" (na przykład SSR), nie są obsługiwane i pozostają jako ćwiczenie dla użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/authentication/identity/spa>
