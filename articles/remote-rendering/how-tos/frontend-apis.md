---
title: Интерфейсные API Azure для проверки подлинности
description: Объясняется, как использовать интерфейсный API C# для проверки подлинности.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681354"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Использование внешних API Azure для проверки подлинности

В этом разделе мы рассмотрим, как использовать API C# для проверки подлинности.

## <a name="azurefrontendaccountinfo"></a>азурефронтендаккаунтинфо

Азурефронтендаккаунтинфо используется для настройки данных проверки подлинности для ```AzureFrontend``` экземпляра в пакете SDK.

Ниже перечислены важные поля.

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Для части _региона_ в домене используйте [ближайший к вам регион](../reference/regions.md).

Сведения об учетной записи можно получить на портале, как описано в абзаце [Получение сведений об учетной записи](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Интерфейсный сервер Azure

Соответствующие классы — ```AzureFrontend``` и ```AzureSession```. ```AzureFrontend```используется для функций управления учетными записями и уровня учетной записи, которые включают в себя преобразование ресурсов и создание сеансов отрисовки. ```AzureSession```используется для функциональности уровня сеанса и включает в себя обновление сеанса, запросы, обновление и списание.

Каждый открытый/созданный ```AzureSession``` объект сохранит ссылку на интерфейс, который его создал. Чтобы аккуратно завершить работу, необходимо освободить все сеансы, прежде чем будет освобожден внешний интерфейс.

При отмене выделения сеанса виртуальная машина в Azure `AzureSession.StopAsync` не будет вызываться явным образом.

После создания сеанса и его состояния, которое было помечено как готовое, оно может подключиться к удаленной среде выполнения отрисовки `AzureSession.ConnectToRuntime`с помощью.

### <a name="threading"></a>Потоки

Все асинхронные вызовы Азуресессион и Азурефронтенд выполняются в фоновом потоке, а не в главном потоке приложения.

### <a name="conversion-apis"></a>API-интерфейсы преобразования

Дополнительные сведения о службе преобразования см. в разделе [Преобразование модели REST API](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Начать преобразование ресурса

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Получение состояния преобразования

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>API-интерфейсы подготовки отчетов

Дополнительные сведения об управлении сеансами см. [в REST API управления сеансами](session-rest-api.md) .

Сеанс отрисовки может быть создан либо динамически в службе, либо уже существующий идентификатор сеанса можно открыть в объекте Азуресессион.

#### <a name="create-rendering-session"></a>Создание сеанса подготовки к просмотру

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Открытие существующего сеанса подготовки к просмотру

Открытие существующего сеанса — это синхронный вызов.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Получение текущих сеансов отрисовки

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>API-интерфейсы сеанса

#### <a name="get-rendering-session-properties"></a>Получение свойств сеанса отрисовки

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Обновить сеанс отрисовки

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Прерывать сеанс подготовки к просмотру

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Подключение к инспектору ARR

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание учетной записи](create-an-account.md)
* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
