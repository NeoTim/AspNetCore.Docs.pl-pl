---
title: Pomocnik tagu środowiska w ASP.NET Core
author: pkellner
description: Zdefiniowano pomocnika tagów środowiska ASP.NET Core, w tym wszystkie właściwości
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: de37bf704ac377a15ef600b96ba78056c51ea7be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633867"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Pomocnik tagu środowiska w ASP.NET Core

Według [Piotr Kellner](https://peterkellner.net) i [Hisham bin Ateya](https://twitter.com/hishambinateya)

Pomocnik tagu środowiska warunkowo renderuje zawartą zawartość w oparciu o bieżące [środowisko hostingu](xref:fundamentals/environments). Pojedynczy atrybut pomocnika tagów środowiska, `names` , jest rozdzielaną przecinkami listą nazw środowiska. Jeśli dowolna z podanych nazw środowiska jest zgodna z bieżącym środowiskiem, załączona zawartość jest renderowana.

Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .

## <a name="environment-tag-helper-attributes"></a>Atrybuty pomocnika tagów środowiska

### <a name="names"></a>nazwy

`names` akceptuje jedną nazwę środowiska hostingu lub listę rozdzielonych przecinkami nazw środowiska hostingu, które wyzwalają renderowanie załączonej zawartości.

Wartości środowiskowe są porównywane z bieżącą wartością zwracaną przez [IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). Porównanie ignoruje wielkość liter.

W poniższym przykładzie jest używana pomocnik tagów środowiska. Zawartość jest renderowana, jeśli środowisko hostingu jest przejściowe lub produkcyjne:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Dołączanie i wykluczanie atrybutów

`include`& `exclude` kontrolka atrybutów renderuje zawartą zawartość na podstawie nazw dołączanych lub wykluczonych środowisk hostingu.

### <a name="include"></a>include

`include`Właściwość wykazuje podobne zachowanie w odróżnieniu od `names` atrybutu. Środowisko wymienione w `include` wartości atrybutu musi być zgodne ze środowiskiem hostingu aplikacji ([IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)), aby renderować zawartość `<environment>` znacznika.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>wykluczanie

W przeciwieństwie do `include` atrybutu, zawartość `<environment>` tagu jest renderowana, gdy środowisko hostingu nie jest zgodne ze środowiskiem wymienionym w `exclude` wartości atrybutu.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/environments>
