---
title: Typy zwracane akcje kontrolera w ASP.NET Core Web API
author: scottaddie
description: Dowiedz się więcej o używaniu różnych typów zwracanych metody akcji kontrolera w interfejsie API sieci Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: 991265810324d6339ebf346ff9aa14c479112af9
ms.sourcegitcommit: fa61d882be9d0c48bd681f2efcb97e05522051d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71205758"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Typy zwracane akcje kontrolera w ASP.NET Core Web API

Przez [Scott Addie](https://github.com/scottaddie)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([sposobu pobierania](xref:index#how-to-download-a-sample))

ASP.NET Core oferuje następujące opcje dla zwracanych typów akcji kontrolera API sieci Web:

::: moniker range=">= aspnetcore-2.1"

* [Określony typ](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Określony typ](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Ten dokument wyjaśnia, kiedy jest najbardziej odpowiedni do użycia każdego typu zwracanego.

## <a name="specific-type"></a>Określony typ

Najprostsza akcja zwraca typ danych pierwotnych lub złożonych (na przykład `string` typ obiektu niestandardowego). Rozważ poniższą akcję, która zwraca kolekcję obiektów niestandardowych `Product` :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Bez znanych warunków zabezpieczających przed wykonaniem akcji, zwrócenie określonego typu może być wystarczające. Poprzednia akcja nie akceptuje żadnych parametrów, więc Walidacja ograniczeń parametrów nie jest konieczna.

Gdy w akcji musi być uwzględnione znane warunki, zostaną wprowadzone wiele ścieżek powrotu. W takim przypadku typowym sposobem jest mieszanie <xref:Microsoft.AspNetCore.Mvc.ActionResult> typu zwracanego z pierwotnym lub złożonym typem zwracanym. [IActionResult](#iactionresult-type) lub [\<ActionResult > T](#actionresultt-type) są niezbędne do zaspokojenia tego typu akcji.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Zwracaj\<interfejs IEnumerable t >\<lub IAsyncEnumerable T >

W ASP.NET Core 2,2 i wcześniejszych zwraca <xref:System.Collections.Generic.IAsyncEnumerable%601> z akcji wyniki w iteracji kolekcji synchronicznej przez serializator. Wynikiem jest blokowanie wywołań i potencjalna blokada puli wątków. Aby zilustrować, Wyobraź sobie, że Entity Framework (EF) rdzeń jest używany do potrzeb dostępu do danych interfejsu API sieci Web. Zwracany typ następującego działania jest synchronicznie wyliczany podczas serializacji:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Aby uniknąć synchronicznego wyliczania i blokowania w bazie danych w ASP.NET Core 2,2 i wcześniejszych, `ToListAsync`Wywołaj:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

W ASP.NET Core 3,0 i nowszych zwraca `IAsyncEnumerable<T>` z akcji:

* Nie jest już wynikiem iteracji synchronicznej.
* Staną się efektywnym sposobem <xref:System.Collections.Generic.IEnumerable%601>powrotu.

ASP.NET Core 3,0 i później buforuje wynik poniższego działania przed dostarczeniem go do serializatora:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Rozważ zadeklarowanie zwracanego typu sygnatury akcji `IAsyncEnumerable<T>` jako do zagwarantowania asynchronicznej iteracji. Ostatecznie tryb iteracji jest oparty na zwracanym konkretnym typie. MVC automatycznie buforuje każdy konkretny typ, `IAsyncEnumerable<T>`który implementuje.

Należy wziąć pod uwagę następujące działania, które zwracają rekordy `IEnumerable<Product>`produktu z ceną sprzedaży:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>` Odpowiednik poprzedniej akcji to:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Obie powyższe akcje nie są blokowane w przypadku ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Typ IActionResult

Typ <xref:Microsoft.AspNetCore.Mvc.IActionResult> zwracany jest odpowiedni, gdy w `ActionResult` akcji jest możliwe wiele typów zwracanych. `ActionResult` Typy reprezentują różne kody stanu HTTP. Każda Klasa nieabstrakcyjna będąca wynikiem `ActionResult` kwalifikatora jest prawidłowym typem zwracanym. Niektóre typowe typy zwracane w tej kategorii to <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) i <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternatywnie, wygodne metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase> klasy mogą być używane do zwracania `ActionResult` typów z akcji. Na przykład, `return BadRequest();` jest to skrócona `return new BadRequestResult();`forma.

Ponieważ istnieje wiele typów zwracanych i ścieżek w tym typie akcji, konieczne jest użycie zliberalizowanej wartości atrybutu [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) . Ten atrybut zawiera bardziej szczegółowe szczegóły odpowiedzi dla stron pomocy interfejsu API sieci Web generowanych przez narzędzia takie jak [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]`wskazuje znane typy i kody stanu HTTP do zwrócenia przez akcję.

### <a name="synchronous-action"></a>Akcja synchroniczna

Rozważ wykonanie następującej akcji synchronicznej, w której istnieją dwa możliwe typy zwracane:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

W poprzedniej akcji:

* Kod stanu 404 jest zwracany, gdy produkt reprezentowany przez `id` nie istnieje w źródłowym magazynie danych. Wygodna metoda jest wywoływana jako skrót dla `return new NotFoundResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>
* Kod stanu 200 jest zwracany wraz z `Product` obiektem, gdy produkt jest istniejący. Wygodna metoda jest wywoływana jako skrót dla `return new OkObjectResult(product);`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>

### <a name="asynchronous-action"></a>Akcja asynchroniczna

Rozważ wykonanie następującej akcji asynchronicznej, w której istnieją dwa możliwe typy zwracane:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

W poprzedniej akcji:

* Kod stanu 400 jest zwracany, gdy opis produktu zawiera "widżet XYZ". Wygodna metoda jest wywoływana jako skrót dla `return new BadRequestResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>
* Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> wygodną metodę podczas tworzenia produktu. Alternatywą dla wywołania `CreatedAtAction` jest `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. W tej ścieżce `Product` kodu obiekt jest podany w treści odpowiedzi. Podano nagłówek `Location` odpowiedzi zawierający nowo utworzony adres URL produktu.

Na przykład następujący model wskazuje, że żądania muszą zawierać `Name` właściwości i. `Description` Niedostarczenie `Name` i `Description` w żądaniu powoduje niepowodzenie walidacji modelu.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Jeśli jest stosowany atrybut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) w ASP.NET Core 2,1 lub nowszej, błędy walidacji modelu powodują powstanie kodu stanu 400. Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>ActionResult\<T > typ

W ASP.NET Core 2,1 wprowadzono zwracany typ [\<ActionResult T >](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dla akcji kontrolera interfejsu API sieci Web. Umożliwia zwracanie typu pochodnego <xref:Microsoft.AspNetCore.Mvc.ActionResult> lub zwracanego [określonego typu](#specific-type). `ActionResult<T>`oferuje następujące korzyści dotyczące [typu IActionResult](#iactionresult-type):

* `Type` Właściwość atrybutu [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) może być wykluczona. Na przykład `[ProducesResponseType(200, Type = typeof(Product))]` uproszczony dla `[ProducesResponseType(200)]`. Oczekiwany typ zwracany akcji jest wywnioskowany na podstawie `T`. `ActionResult<T>`
* [Operatory rzutowania niejawnego](/dotnet/csharp/language-reference/keywords/implicit) obsługują konwersję `ActionResult` obu `ActionResult<T>` `T` i do. `T`Konwertuje na <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, co oznacza `return new ObjectResult(T);` , że jest `return T;`uproszczony dla.

C#nie obsługuje operatorów rzutowania niejawnego w interfejsach. W związku z tym konwersja interfejsu na konkretny typ jest niezbędna do użycia `ActionResult<T>`. Na przykład użycie `IEnumerable` w programie w poniższym przykładzie nie działa:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Jedną z opcji naprawy powyższego kodu jest zwrócenie `_repository.GetProducts().ToList();`.

Większość akcji ma określony typ zwracany. Podczas wykonywania akcji mogą wystąpić nieoczekiwane warunki. w takim przypadku określony typ nie jest zwracany. Na przykład parametr wejściowy akcji może kończyć się niepowodzeniem walidacji modelu. W takim przypadku często zwraca odpowiedni `ActionResult` typ zamiast określonego typu.

### <a name="synchronous-action"></a>Akcja synchroniczna

Należy rozważyć akcję synchroniczną, w której są dostępne dwa typy zwracane:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

W poprzedniej akcji:

* Kod stanu 404 jest zwracany, gdy produkt nie istnieje w bazie danych.
* Kod stanu 200 jest zwracany wraz z odpowiednim `Product` obiektem, gdy produkt istnieje. Przed ASP.NET Core 2,1, `return product;` wiersz musi być. `return Ok(product);`

### <a name="asynchronous-action"></a>Akcja asynchroniczna

Rozważ akcję asynchroniczną, w której istnieją dwa możliwe typy zwracane:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

W poprzedniej akcji:

* Kod stanu 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) jest zwracany przez środowisko uruchomieniowe ASP.NET Core w przypadku:
  * Atrybut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) został zastosowany i walidacja modelu kończy się niepowodzeniem.
  * Opis produktu zawiera "widżet XYZ".
* Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodę podczas tworzenia produktu. W tej ścieżce `Product` kodu obiekt jest podany w treści odpowiedzi. Podano nagłówek `Location` odpowiedzi zawierający nowo utworzony adres URL produktu.

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
