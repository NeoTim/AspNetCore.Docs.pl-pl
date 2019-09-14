---
title: Klient platformy .NET ASP.NET Core sygnalizujący
author: bradygaster
description: Informacje dotyczące klienta programu ASP.NET Core sygnalizującego
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/13/2019
uid: signalr/dotnet-client
ms.openlocfilehash: d2755f652e734bad6447ddeb9a82345dcde25b28
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985493"
---
# <a name="aspnet-core-signalr-net-client"></a>Klient platformy .NET ASP.NET Core sygnalizujący

Biblioteka kliencka ASP.NET Coreowego sygnalizującego platformę .NET umożliwia komunikowanie się z centrami sygnałów z aplikacji .NET.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([sposobu pobierania](xref:index#how-to-download-a-sample))

Przykładowy kod w tym artykule jest aplikacją WPF korzystającą z klienta programu ASP.NET Core sygnalizującego.

## <a name="install-the-signalr-net-client-package"></a>Instalowanie pakietu klienckiego programu sygnalizującego

Pakiet [Microsoft. AspNetCore. signaler. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) jest wymagany do nawiązania połączenia z centrami sygnałów przez klientów platformy .NET.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w oknie **konsola Menedżera pakietów** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w powłoce poleceń:

```console
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Połączenia z koncentratorem

Aby nawiązać połączenie, Utwórz `HubConnectionBuilder` wywołanie `Build`i. Podczas tworzenia połączenia można skonfigurować adres URL centrum, protokół, typ transportu, poziom dziennika, nagłówki i inne opcje. Skonfiguruj wszystkie wymagane opcje, wstawiając dowolne `HubConnectionBuilder` metody do `Build`programu. Rozpocznij połączenie z usługą `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Obsługa utraconych połączeń

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatycznie Połącz ponownie

Można skonfigurować do automatycznego ponownego łączenia `WithAutomaticReconnect` przy użyciu metody w <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Domyślnie nie będzie automatycznie ponownie łączyć się.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Bez żadnych parametrów program `WithAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.

Przed rozpoczęciem wszelkich ponownych prób `HubConnection` nawiązania połączenia nastąpi przejście `HubConnectionState.Reconnecting` do stanu `Reconnecting` i wyzwolenie zdarzenia.  Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika. Aplikacje nieinteraktywne mogą uruchamiać kolejkowanie lub porzucanie komunikatów.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót `Connected` do stanu i wyzwolenie `Reconnected` zdarzenia. Zapewnia to możliwość informowania użytkowników o tym, że połączenie zostało ponownie nawiązane i usuwa wszystkie wiadomości w kolejce.

Ponieważ połączenie jest całkowicie nowe dla serwera, do obsługi `ConnectionId` `Reconnected` zdarzeń zostanie udostępniona nowa.

> [!WARNING]
> Parametr programu obsługi `Reconnected` zdarzeń`HubConnection` będzie miał wartość null, jeśli został skonfigurowany do [pomijania negocjacji.](xref:signalr/configuration#configure-client-options) `connectionId`

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Jeśli klient nie `HubConnection` zostanie pomyślnie ponownie połączony w ramach pierwszych czterech prób, przejdzie `Disconnected` do stanu i uruchomi <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> zdarzenie. Zapewnia to możliwość próby ponownego uruchomienia połączenia ręcznie lub poinformowanie użytkowników, że połączenie zostało trwale utracone.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program akceptuje `WithAutomaticReconnect` tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

W powyższym przykładzie `HubConnection` konfiguruje się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia. Dotyczy to również konfiguracji domyślnej.

Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.

Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.

Zachowanie niestandardowe jest następnie ponownie niezależne od zachowania domyślnego przez zatrzymanie po trzecim nieudanej próbie połączenia. W konfiguracji domyślnej będzie jeszcze jedna kolejna próba ponownego połączenia w ciągu 30 sekund.

Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `WithAutomaticReconnect` zaakceptuje obiekt `IRetryPolicy` implementujący interfejs, który ma pojedynczą metodę o nazwie `NextRetryDelay`.

`NextRetryDelay`przyjmuje jeden argument z typem `RetryContext`. `PreviousRetryCount` Matrzy`ElapsedTime` właściwości: ,a`RetryReason`które są odpowiednio,a`TimeSpan`ia. `long` `Exception` `RetryContext` Przed pierwszym ponownym połączeniem, oba `PreviousRetryCount` i `ElapsedTime` będą `RetryReason` miały wartość zero, a będzie wyjątek, który spowodował utratę połączenia. Po każdym nieudanej próbie `PreviousRetryCount` ponowieniu próby zostanie `ElapsedTime` zaktualizowany w celu odzwierciedlenia czasu, który połączył się do tej pory, i `RetryReason` będzie to wyjątek, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.

`NextRetryDelay`musi zwrócić wartość TimeSpan reprezentującą czas oczekiwania przed kolejną próbą ponownego połączenia lub `null` `HubConnection` Jeśli należy zatrzymać Ponowne nawiązywanie połączenia.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w ręcznym [ponownym nawiązaniu połączenia](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ręczne ponowne łączenie

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Przed 3,0 klient platformy .NET dla sygnalizującego nie będzie automatycznie ponownie łączyć się. Należy napisać kod, który zostanie nawiązana ponownie ręcznie klienta.

::: moniker-end

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Użyj zdarzenia, aby odpowiedzieć na utracone połączenie. Na przykład możesz chcieć zautomatyzować ponowne łączenie.

Zdarzenie wymaga delegata zwracającego element `Task`, który umożliwia uruchamianie kodu asynchronicznego bez użycia `async void`. `Closed` Aby spełnić podpis delegata w programie `Closed` obsługi zdarzeń, który jest uruchamiany synchronicznie `Task.CompletedTask`, zwróć:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Główną przyczyną obsługi asynchronicznej jest to, że można ponownie uruchomić połączenie. Rozpoczęcie połączenia jest akcją asynchroniczną.

W programie `Closed` obsługi, który ponownie uruchamia połączenie, rozważ oczekiwanie na losowe opóźnienie, aby zapobiec przeciążeniu serwera, jak pokazano w następującym przykładzie:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Wywoływanie metod koncentratora z klienta

`InvokeAsync`wywołuje metody w centrum. Przekaż nazwę metody Hub i wszystkie argumenty zdefiniowane w metodzie Hub do `InvokeAsync`. Sygnalizujący jest asynchroniczny, dlatego `async` należy `await` używać i podczas wykonywania wywołań.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync` Metoda zwraca,którakończysię,gdy`Task` Metoda serwera zwraca wartość. Wartość zwracana, jeśli istnieje, jest podawana jako wynik `Task`. Wszystkie wyjątki zgłoszone przez metodę na serwerze generują błędy `Task`. Użyj `await` składni, aby poczekać na zakończenie i składnię `try...catch` metody serwera, aby obsłużyć błędy.

`SendAsync` Metoda zwraca,którakończysię,gdykomunikat`Task` został wysłany do serwera. Nie podano wartości zwracanej od `Task` momentu zaczekania na zakończenie metody serwera. Wszystkie wyjątki zgłoszone na kliencie podczas wysyłania komunikatu generują błędy `Task`. Użyj `await` składni `try...catch` i, aby obsłużyć błędy wysyłania.

> [!NOTE]
> Jeśli używasz usługi Azure sygnalizującej w *trybie*bezserwerowym, nie możesz wywoływać metod centralnych z poziomu klienta. Aby uzyskać więcej informacji, zobacz [dokumentację usługi sygnalizującej](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Wywoływanie metody klienta z Centrum

Zdefiniuj metody, które są używane `connection.On` przez centrum po skompilowaniu, ale przed rozpoczęciem połączenia.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Poprzedni kod w `connection.On` działa, gdy kod po stronie serwera wywołuje go `SendAsync` przy użyciu metody.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Rejestrowanie i obsługa błędów

Obsługa błędów przy użyciu instrukcji try-catch. Zbadaj `Exception` obiekt, aby określić odpowiednią akcję do wykonania po wystąpieniu błędu.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Centra](xref:signalr/hubs)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
* [Dokumentacja bezserwerowa usługi sygnalizującej platformę Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
