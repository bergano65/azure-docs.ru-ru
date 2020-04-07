---
title: AБИИ Azure Frontend для проверки подлинности
description: Объясняет, как использовать aPI-фронтовую проверку для проверки подлинности
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681354"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Используйте AIS Azure Frontend для проверки подлинности

В этом разделе мы опишите, как использовать API для проверки подлинности.

## <a name="azurefrontendaccountinfo"></a>AzureFrontFrontendAccountInfo

AzureFrontfrontendAccountInfo используется для настройки информации ```AzureFrontend``` о проверке подлинности для экземпляра в SDK.

Важными областями являются:

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

Для _части региона_ в домене используйте [ближайший к вам регион.](../reference/regions.md)

Информация об учетной записи может быть получена на портале, как описано в пункте [получения информации об учетной записи.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Лазурный фронтенд

Соответствующие классы ```AzureSession```и ```AzureFrontend``` . ```AzureFrontend```используется для управления учетной записью и функциональности уровня учетной записи, которая включает в себя: преобразование активов и создание сеанса рендеринга. ```AzureSession```используется для функциональности уровня сессии и включает в себя: обновление сеанса, запросы, обновление и вывод из эксплуатации.

Каждый открытый/созданный ```AzureSession``` будет держать ссылку на фронт, который создал его. Чтобы очистить, все сеансы должны быть разобраны до того, как фасад будет разобран.

Сделка с сеансом не остановит VM `AzureSession.StopAsync` на Azure, должен быть прямо вызван.

После создания сеанса и пометки его состояния как готового, он `AzureSession.ConnectToRuntime`может подключиться к удаленному времени выполнения рендеринга с помощью .

### <a name="threading"></a>Потоки

Все вызовы AzureSession и AzureFrontend выполняются в фоновом потоке, а не в основном потоке приложения.

### <a name="conversion-apis"></a>Конверсионные AIS

Для получения дополнительной информации [the model conversion REST API](conversion/conversion-rest-api.md)об услуге преобразования см.

#### <a name="start-asset-conversion"></a>Начало конверсии активов

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

#### <a name="get-conversion-status"></a>Получить статус конверсии

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

### <a name="rendering-apis"></a>Рендеринг AIS

Подробную информацию об управлении сеансами можно узнать [о руководстве сеансами.](session-rest-api.md)

Сеанс рендеринга может быть создан динамически в службе, либо уже существующий идентификатор сеанса может быть "открыт" в объект AzureSession.

#### <a name="create-rendering-session"></a>Создание сеанса рендеринга

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

#### <a name="open-an-existing-rendering-session"></a>Открыть существующую сессию рендеринга

Открытие существующего сеанса является синхронным вызовом.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Получить текущие сеансы рендеринга

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

### <a name="session-apis"></a>Сессионные AIS

#### <a name="get-rendering-session-properties"></a>Получить свойства сеанса рендеринга

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

#### <a name="update-rendering-session"></a>Сеанс отобрасывания обновления

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

#### <a name="stop-rendering-session"></a>Остановка сеанса рендеринга

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

## <a name="next-steps"></a>Дальнейшие действия

* [Создание учетной записи](create-an-account.md)
* [Пример скриптов PowerShell](../samples/powershell-example-scripts.md)
