---
title: Omówienie uwierzytelniania ASP.NET Core
author: mjrousos
description: Informacje o uwierzytelnianiu w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434333"
---
# <a name="overview-of-aspnet-core-authentication"></a>Omówienie uwierzytelniania ASP.NET Core

Według [Jan Rousos](https://github.com/mjrousos)

Uwierzytelnianie to proces określania tożsamości użytkownika. [Autoryzacja](xref:security/authorization/introduction) to proces ustalania, czy użytkownik ma dostęp do zasobu. W ASP.NET Core uwierzytelnianie jest obsługiwane przez `IAuthenticationService`, który jest używany przez [oprogramowanie pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania. Usługa uwierzytelniania używa zarejestrowanych programów obsługi uwierzytelniania do ukończenia akcji związanych z uwierzytelnianiem. Przykłady akcji związanych z uwierzytelnianiem obejmują:

* Uwierzytelnianie użytkownika.
* Odpowiada, gdy nieuwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu z ograniczeniami.

Zarejestrowane programy obsługi uwierzytelniania i ich opcje konfiguracji są nazywane "schematami".

Schematy uwierzytelniania są określane przez zarejestrowanie usług uwierzytelniania w `Startup.ConfigureServices`:

* Wywołując metodę rozszerzenia specyficzną dla schematu po wywołaniu `services.AddAuthentication` (na przykład `AddJwtBearer` lub `AddCookie`). Te metody rozszerzające używają [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) do rejestrowania schematów przy użyciu odpowiednich ustawień.
* Rzadziej, przez wywołanie [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) bezpośrednio.

Na przykład poniższy kod rejestruje usługi uwierzytelniania i programy obsługi dla systemów plików cookie i uwierzytelniania okaziciela JWT:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication` parametr `JwtBearerDefaults.AuthenticationScheme` jest nazwą schematu, który ma być używany domyślnie, gdy określony schemat nie jest wymagany.

W przypadku użycia wielu schematów zasady autoryzacji (lub atrybuty autoryzacji) mogą [określać schemat uwierzytelniania (lub schematy),](xref:security/authorization/limitingidentitybyscheme) od których zależą w celu uwierzytelnienia użytkownika. W powyższym przykładzie schemat uwierzytelniania plików cookie może być używany przez określenie jego nazwy (`CookieAuthenticationDefaults.AuthenticationScheme` domyślnie, ale podczas wywoływania `AddCookie`) można podać inną nazwę.

W niektórych przypadkach wywołanie `AddAuthentication` jest wykonywane automatycznie przez inne metody rozszerzenia. Na przykład podczas korzystania z [ASP.NET Core Identity](xref:security/authentication/identity)`AddAuthentication` jest wywoływana wewnętrznie.

Oprogramowanie pośredniczące uwierzytelniania jest dodawane w `Startup.Configure` przez wywołanie metody rozszerzenia <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> na `IApplicationBuilder`aplikacji. Wywołanie `UseAuthentication` rejestruje oprogramowanie pośredniczące, które używa poprzednio zarejestrowanego schematu uwierzytelniania. Wywołaj `UseAuthentication` przed dowolnym oprogramowanie pośredniczące zależne od użytkowników, którzy są uwierzytelniani. W przypadku korzystania z routingu punktów końcowych wywołanie `UseAuthentication` musi mieć wartość:

* Po `UseRouting`, dzięki czemu informacje o trasie są dostępne na potrzeby podejmowania decyzji dotyczących uwierzytelniania.
* Przed `UseEndpoints`, aby użytkownicy mieli uwierzytelnieni przed uzyskaniem dostępu do punktów końcowych.

## <a name="authentication-concepts"></a>Pojęcia związane z uwierzytelnianiem

### <a name="authentication-scheme"></a>Schemat uwierzytelniania

Schematem uwierzytelniania jest nazwa, która odnosi się do:

* Program obsługi uwierzytelniania.
* Opcje konfigurowania określonego wystąpienia programu obsługi.

Schematy są przydatne jako mechanizm do odwoływania się do zachowań uwierzytelnianie, wyzwanie i Zabroń skojarzonej procedury obsługi. Na przykład zasady autoryzacji mogą używać nazw schematów, aby określić, który schemat uwierzytelniania (lub schematy) powinien zostać użyty do uwierzytelnienia użytkownika. Podczas konfigurowania uwierzytelniania często można określić domyślny schemat uwierzytelniania. Domyślny schemat jest używany, chyba że zasób żąda określonego schematu. Możliwe jest również:

* Określ różne domyślne schematy do użycia w akcjach uwierzytelniania, wyzwania i zabraniania.
* Połącz wiele schematów przy użyciu [schematów zasad](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Procedura obsługi uwierzytelniania

Procedura obsługi uwierzytelniania:

* Jest typem, który implementuje zachowanie schematu.
* Pochodzi od <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> lub <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.
* Ma główną odpowiedzialność za uwierzytelnianie użytkowników.

W oparciu o konfigurację schematu uwierzytelniania i kontekst żądania przychodzącego, programy obsługi uwierzytelniania:

* Konstruowanie obiektów <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> reprezentujących tożsamość użytkownika w przypadku pomyślnego uwierzytelnienia.
* Zwróć "Brak wyniku" lub "Niepowodzenie", jeśli uwierzytelnianie nie powiedzie się.
* Mają metody dla akcji wyzwania i Zabraniaj, gdy użytkownicy próbują uzyskać dostęp do zasobów:
  * Dostęp do nich nie jest autoryzowany (Zabroń).
  * Gdy nie są uwierzytelniane (wyzwanie).

### <a name="authenticate"></a>Uwierzytelnianie

Akcja uwierzytelniania schematu uwierzytelniania jest odpowiedzialna za konstruowanie tożsamości użytkownika na podstawie kontekstu żądania. Zwraca <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> wskazujący, czy uwierzytelnianie zakończyło się pomyślnie, a jeśli tak, tożsamość użytkownika w biletu uwierzytelniania. Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Przykłady uwierzytelniania obejmują:

* Schemat uwierzytelniania plików cookie, który konstruuje tożsamość użytkownika z plików cookie.
* Schemat okaziciela JWT deserializacji i weryfikacji tokenu okaziciela JWT w celu utworzenia tożsamości użytkownika.

### <a name="challenge"></a>Sprawdz

Wyzwanie uwierzytelniania jest wywoływane przez autoryzację, gdy nieuwierzytelniony użytkownik żąda punktu końcowego wymagającego uwierzytelniania. Jest wystawiane wyzwanie uwierzytelniania, na przykład gdy użytkownik anonimowy żąda zasobu z ograniczeniami lub klika łącze logowania. Autoryzacja wywołuje wyzwanie przy użyciu określonych schematów uwierzytelniania lub wartość domyślną, jeśli nie została określona. Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Przykłady wyzwania uwierzytelniania obejmują:

* Schemat uwierzytelniania plików cookie przekierowuje użytkownika do strony logowania.
* Schemat okaziciela JWT zwracający wynik 401 z nagłówkiem `www-authenticate: bearer`.

Akcja wyzwania powinna dać użytkownikowi informacje o mechanizmie uwierzytelniania używanym do uzyskiwania dostępu do żądanego zasobu.

### <a name="forbid"></a>Uniemożliwia

Akcja zabraniania schematu uwierzytelniania jest wywoływana przez autoryzację, gdy uwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu, do którego nie ma dostępu. Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Przykładem zabraniania uwierzytelniania są:
* Schemat uwierzytelniania plików cookie przekierowuje użytkownika do strony wskazującej dostęp był zabroniony.
* Schemat okaziciela JWT zwracający wynik 403.
* Niestandardowy schemat uwierzytelniania przekierowuje do strony, na której użytkownik może zażądać dostępu do zasobu.

Akcja Zabroń może pozwolić użytkownikowi na:

* Są uwierzytelniane.
* Nie mogą uzyskać dostępu do żądanego zasobu.

Poniżej znajdują się linki dotyczące różnic między wyzwaniem i zabranianiem:

* [Wyzwania i Zabroń z obsługą zasobów operacyjnych](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Różnice między wyzwaniem i zabranianiem](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Dostawcy uwierzytelniania na dzierżawcę

Platforma ASP.NET Core nie ma wbudowanego rozwiązania do uwierzytelniania wielodostępnego.
Chociaż jest to możliwe, aby klienci mogli pisać jedną, przy użyciu wbudowanych funkcji, zalecamy, aby w tym celu przyjrzeć się klientom na [rdzeń](https://www.orchardcore.net/) .

Rdzeń sadu:

* Modularna i wielodostępna platforma aplikacji Open Source skompilowana przy użyciu ASP.NET Core.
* System zarządzania zawartością (CMS) zbudowany na podstawie tej struktury aplikacji.

Zobacz [podstawowe źródło sadu](https://github.com/OrchardCMS/OrchardCore) dla przykładu dostawców uwierzytelniania na dzierżawcę.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
