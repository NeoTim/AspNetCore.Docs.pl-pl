---
title: ASP.NET Core Blazor operacje przekazywania plików
author: guardrex
description: Dowiedz się, jak przekazywać pliki w Blazor składniku plik_wejściowy.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606652"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor operacje przekazywania plików

Autorzy [Daniel Roth](https://github.com/danroth27) i [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Użyj `InputFile` składnika do odczytywania danych z pliku przeglądarki w kodzie .NET, w tym do przekazywania plików.

> [!WARNING]
> Zawsze przestrzegaj najlepszych rozwiązań w zakresie zabezpieczeń przekazywania plików. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile` cm6long

`InputFile`Składnik jest renderowany jako dane wejściowe HTML typu `file` .

Domyślnie użytkownik wybiera pojedyncze pliki. Dodaj `multiple` atrybut, aby zezwolić użytkownikowi na przekazywanie wielu plików jednocześnie. Gdy użytkownik wybierze co najmniej jeden plik, `InputFile` składnik uruchamia `OnChange` zdarzenie i przekazuje w programie `InputFileChangeEventArgs` , który zapewnia dostęp do listy wybranych plików i szczegółowe informacje o każdym z nich.

Aby odczytać dane z pliku wybranego przez użytkownika:

* Wywołaj `OpenReadStream` plik i odczytaj go ze zwróconego strumienia. Aby uzyskać więcej informacji, zobacz sekcję [strumienie plików](#file-streams) .
* Użyj polecenia `ReadAsync`. Domyślnie `ReadAsync` zezwala tylko na odczytywanie plików o rozmiarze mniejszym niż 524 288 KB (512 KB). Ten limit jest obecny, aby uniemożliwić deweloperom przypadkowe odczytywanie dużych plików w pamięci. Określ rozsądne przybliżenie dla maksymalnego oczekiwanego rozmiaru pliku, jeśli muszą być obsługiwane większe pliki. Należy unikać odczytywania przychodzącego strumienia plików bezpośrednio do pamięci. Na przykład nie Kopiuj bajtów pliku do <xref:System.IO.MemoryStream> lub Odczytaj jako tablicę bajtów. Te podejścia mogą powodować problemy z wydajnością i zabezpieczeniami, szczególnie w Blazor Server . Zamiast tego należy rozważyć kopiowanie bajtów plików do magazynu zewnętrznego, takiego jak obiekt BLOB lub plik na dysku.

Składnik, który odbiera plik obrazu, może wywołać `RequestImageFileAsync` wygodną metodę na pliku, aby zmienić rozmiar danych obrazu w środowisku uruchomieniowym JavaScript przeglądarki, zanim obraz zostanie przesłany do aplikacji.

Poniższy przykład ilustruje przekazywanie wielu plików obrazu w składniku. `InputFileChangeEventArgs.GetMultipleFiles` umożliwia odczytywanie wielu plików. Określ maksymalną liczbę plików, które należy odczytać, aby uniemożliwić złośliwemu użytkownikowi przekazanie większej liczby plików niż oczekiwano. `InputFileChangeEventArgs.File` zezwala na odczytywanie pierwszego i tylko pliku, jeśli przekazywanie plików nie obsługuje wielu plików.

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

`IBrowserFile` zwraca metadane [uwidocznione przez przeglądarkę](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako właściwości. Te metadane mogą być przydatne do wstępnej weryfikacji. Na przykład zobacz [ `FileUpload.razor` i `FilePreview.razor` przykładowe składniki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Strumienie plików

W Blazor WebAssembly aplikacji dane są przesyłane strumieniowo bezpośrednio do kodu .NET w przeglądarce.

W Blazor Server aplikacji dane plików są przesyłane strumieniowo przez SignalR połączenie do kodu platformy .NET na serwerze, ponieważ plik jest odczytywany ze strumienia. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) umożliwia skonfigurowanie parametrów przekazywania plików dla programu Blazor Server .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
