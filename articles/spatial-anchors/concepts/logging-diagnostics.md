---
title: Ведение журнала и диагностика
description: Глубокое объяснение того, как генерировать и извлекать журналы и диагностику в пространственных якорях Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270133"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Регистрация и диагностика в лазурных пространственных якорях

Пространственные якоря Azure обеспечивают стандартный механизм регистрации, который полезен для разработки приложений. Режим регистрации spatial Anchors полезен, когда вам требуется дополнительная информация для отладки. Диагностика журналов хранит изображения среды.

## <a name="standard-logging"></a>Стандартная регистрация
В API spatial Anchors можно подписаться на механизм ведения журнала, чтобы получить полезные журналы для разработки и отладки приложений. Стандартные прутья регистрации не хранят изображения среды на диске устройства. SDK предоставляет эти журналы в виде обратных вызовов событий. Интеграция этих журналов в механизм регистрации приложения должна быть интегрирована.

### <a name="configuration-of-log-messages"></a>Конфигурация сообщений журналов
Есть два обратных вызовов, представляющих интерес для пользователя. Следующий пример показывает, как настроить сеанс.

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

Эти обратные вызовы событий предоставляются для обработки журналов и ошибок из сеанса:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Определяет уровень детализации событий, получаемых из времени выполнения.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Обеспечивает стандартные события журнала отладки.
- [Ошибка](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Обеспечивает события журнала, которые время выполнения считает ошибками.

## <a name="diagnostics-logging"></a>ведет журнал диагностики.

В дополнение к стандартному режиму работы для ведения журнала, пространственные якоря также имеет режим диагностики. Режим диагностики фиксирует изображения среды и регистрирует их на диске. Этот режим можно использовать для отладки определенных видов проблем, таких как неспособность предсказуемо найти якорь. Включить журнал диагностики только для воспроизведения конкретной проблемы. Тогда отодвить его. Не включайте диагностику при нормальном запуске приложений.

Во время взаимодействия с службой поддержки с корпорацией Майкрософт представитель корпорации Майкрософт может спросить, хотите ли вы отправить пакет диагностики для дальнейшего исследования. В этом случае можно включить диагностику и воспроизвести проблему, чтобы можно было отправить диагностический пакет.

Если вы отправите журнал диагностики в корпорацию Майкрософт без предварительного подтверждения от представителя корпорации Майкрософт, отправка останется без ответа.

В следующих разделах показанспособное включение режима диагностики, а также способ отправки журналов диагностики в корпорацию Майкрософт.

### <a name="enable-diagnostics-logging"></a>Включение ведения журналов диагностики

При включании сеанса для регистрации диагностики все операции в сеансе имеют соответствующую диагностику входа в локальную файловую систему. Во время регистрации изображения среды сохраняются на диске.

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

Следующий фрагмент кода показывает, как отправить пакет диагностики в корпорацию Майкрософт. Этот пакет будет включать изображения среды, захваченные сеансом после включения диагностики.

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
Пакет диагностики может содержать следующую информацию:

- **Изображения ключей:** Изображения среды, полученные во время сеанса во время диагностики.
- **Входы**: События журнала, записанные временем выполнения.
- **Метаданные сеанса**: Метаданные, идентифицирующие сеанс.
