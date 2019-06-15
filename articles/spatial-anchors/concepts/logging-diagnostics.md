---
title: Ведение журнала и диагностики в Azure пространственных привязки | Документация Майкрософт
description: Подробное объяснение того, как для создания и получения ведения журнала и диагностики в Azure пространственных привязки.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: b66dc7d6ec9d11fe645587fe791824009231b7c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964743"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Ведение журнала и диагностики в Azure пространственных привязки

Azure пространственных привязки предоставляет механизм стандартном ведении, который удобен для разработки приложений. Режим ведения журнала диагностики пространственных привязки полезно в тех случаях, когда необходимы дополнительные сведения для отладки. Ведение журнала диагностики хранятся образы среды.

## <a name="standard-logging"></a>Стандартный ведения журнала
В пространственных API привязки можно подписаться на механизм ведения журнала для получения журнала, полезные для разработки приложений и отладки. Стандартный API-интерфейсы ведения журнала не хранить изображения среды на диске устройства. Пакет SDK предоставляет эти журналы как обратные вызовы событий. Именно вы интегрировать эти журналы механизма ведения журнала приложения.

### <a name="configuration-of-log-messages"></a>Конфигурация журнала сообщений
Существуют два обратных интерес для пользователя. Следующий пример показано, как для настройки сеанса.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>События и свойства

Эти обратные вызовы событий предоставляются для обработки журналов и ошибки из сеанса:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Задает уровень детализации для события, для получения из среды выполнения.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Предоставляет стандартные отладки журнала событий.
- [Ошибка](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Предоставляет события журнала, которые среда выполнения рассматривает ошибок.

## <a name="diagnostics-logging"></a>ведет журнал диагностики.

В дополнение к стандартный режим работы для ведения журнала пространственных привязки также предусмотрен режим диагностики. Режим диагностики записывает образов среды и записывает их на диск. Этот режим можно использовать для отладки определенных типов проблем, таких как невозможность предсказуемо найти привязку. Включите ведение журнала требуется воспроизвести определенную проблему диагностики. Отключите его. Не включайте диагностики при запуске приложения обычным образом.

Во время поддерживающие взаимодействие с корпорацией Майкрософт с представителем Microsoft может возникнуть вопрос, если вы хотите отправить пакет диагностики для дальнейшего изучения. В этом случае можно включить диагностику и воспроизвести проблему, поэтому вы можете отправить пакет диагностики. 

Если отправить журнал диагностики в корпорацию Майкрософт без предыдущего подтверждения от представителя Майкрософт, выдача будут оставаться без ответа.

В следующих разделах показано, как включить режим диагностики, а также отправка журналов диагностики в корпорацию Майкрософт.

### <a name="enable-diagnostics-logging"></a>Включение ведения журналов диагностики

Когда вы включаете сеанс для ведения журнала диагностики, все операции в сеансе имеют соответствующий ведении журналов диагностики в локальной файловой системе. Во время ведения журнала, образы среды сохраняются на диск.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Отправить пакет диагностики

В следующем фрагменте кода показано, как отправить пакет диагностики в корпорацию Майкрософт. Этот пакет будет включать изображения среды, собранных в сеансе, после включения диагностики. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Части пакета диагностики
Диагностика пакете может содержать следующие сведения:

- **Опорный кадр образы**: Образы среды, собранных во время сеанса, хотя были включены диагностики.
- **Журналы**. Журнал событий, записанных средой выполнения.
- **Метаданные сеанса**: Метаданные, идентифицирующий сеанс.
