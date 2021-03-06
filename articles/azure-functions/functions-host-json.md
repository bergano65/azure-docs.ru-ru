---
title: Справочник по файлу host.json для службы "Функции Azure" версии 2.x
description: Справочная документация по файлу host.json для Функций Azure в среде выполнения версии V2.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: 08d772fc9b2871262b449a017f8be59a344576b2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975454"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Справочник по Host. JSON для функций Azure 2. x и более поздних версий 

> [!div class="op_single_selector" title1="Выберите версию используемой среды выполнения функций Azure: "]
> * [Версия 1](functions-host-json-v1.md)
> * [Версия 2](functions-host-json.md)

Файл метаданных *host.json* содержит параметры глобальной конфигурации, влияющие на все функции приложения-функции. В этой статье перечислены параметры, доступные начиная с версии 2. x среды выполнения функций Azure.  

> [!NOTE]
> Эта статья предназначена для функций Azure 2. x и более поздних версий.  Сведения о файле host.json в Функциях Azure версии 1.x см. в [этой статье](functions-host-json-v1.md).

В [параметрах приложения](functions-app-settings.md) можно управлять другими настройками приложения-функции.

Некоторые параметры host.json используются только при локальном запуске в файле [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Пример файла host.json

В приведенном ниже примере файлов *host.json* указаны все возможные параметры.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

В следующих разделах этой статьи объясняется каждое свойство верхнего уровня. Все они являются необязательными, если не указано иное.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Этот параметр является дочерним элементом [ведения журнала](#logging).

Управляет [функцией выборки в Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Выборка журналов может привести к тому, что некоторые выполнения не будут отображаться в колонке монитора Application Insights.

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|isEnabled|true|Включает или отключает выборку.| 
|maxTelemetryItemsPerSecond|20|Пороговое значение, при котором начинается выборка.| 
|енаблеливеметрикс |true|Включает сбор динамических метрик.|
|енабледепенденцитраккинг|true|Включает отслеживание зависимостей.|
|енаблеперформанцекаунтерсколлектион|true|Включает сбор счетчиков производительности KUDU.|

## <a name="cosmosdb"></a>СosmosDB

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-cosmosdb-v2.md#host-json) (параметры файла host.json).

## <a name="durabletask"></a>durableTask

Параметры конфигурации можно найти в разделе [host.json settings](durable/durable-functions-bindings.md#host-json) (параметры файла host.json).

## <a name="eventhub"></a>eventHub

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-event-hubs.md#host-json) (параметры файла host.json). 

## <a name="extensions"></a>extensions

Свойство, которое возвращает объект, содержащий все параметры определенной привязки, такие как [http](#http) и [eventHub](#eventhub).

## <a name="extensionbundle"></a>екстенсионбундле 

Пакеты расширений позволяют добавить совместимый набор функций расширений привязки к приложению-функции. Дополнительные сведения см. в разделе [пакеты расширений для локальной разработки](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>функции

Список функций, которые выполняют узел заданий. Пустой массив означает выполнение всех функций. Предназначен для использования только при [локальном выполнении](functions-run-local.md). Чтобы отключить определенные функции в приложениях-функциях Azure, выполните следующие действия в разделе [Способы отключения функций в решении "Функции Azure"](disable-function.md) вместо использования этого параметра.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Указывает время ожидания для всех функций. Он соответствует формату строки TimeSpan. В бессерверных планах потребления допускается диапазон от 1 секунды до 10 минут, а значение по умолчанию — 5 минут.  

В плане Premium допустимый диапазон составляет от 1 секунды до 60 минут, а значение по умолчанию — 30 минут.

В выделенном плане (службе приложений) нет общего ограничения, а значение по умолчанию — 30 минут. Значение `-1` указывает на неограниченное выполнение, но рекомендуется сохранить фиксированную верхнюю границу.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Параметры конфигурации для [монитора работоспособности узла](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|включено|true|Указывает, включена ли функция. | 
|healthCheckInterval|10 с|Интервал времени между периодическими фоновыми проверками работоспособности. | 
|healthCheckWindow|2 минуты|Скользящее окно времени, используемое в сочетании с параметром `healthCheckThreshold`.| 
|healthCheckThreshold|6|Максимальное количество попыток проверки работоспособности, которые могут завершиться сбоем, прежде чем инициируется повторный запуск.| 
|counterThreshold|0,80|Пороговое значение, при достижении которого счетчик производительности будет считаться неработоспособным.| 

## <a name="http"></a>http

Параметры конфигурации см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>Ведение журналов

Управляет поведением ведения журнала приложения-функции, включая Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Определяет, какой уровень журнала файла включен.  Доступны следующие параметры: `never`, `always` и `debugOnly`. |
|LogLevel|Н/Д|Объект, который определяет фильтрацию категорий журналов для функций в приложении. Версии 2. x и более поздние следуют за разметкой ASP.NET Core для фильтрации категорий журнала. Это позволяет фильтровать ведения журнала определенных функций. Дополнительные сведения см. в документации по использованию ASP.NET Core [Фильтрация журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). |
|console|Н/Д| Параметр ведения журнала [консоли](#console). |
|applicationInsights|Н/Д| Параметр [applicationInsights](#applicationinsights). |

## <a name="console"></a>console

Этот параметр является дочерним элементом [ведения журнала](#logging). Если не в режиме отладки, этот параметр контролирует ведение журнала консоли.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|isEnabled|false|Включает или отключает ведение журнала консоли.| 

## <a name="manageddependency"></a>манажеддепенденци

Управляемая зависимость — это функция, которая в настоящее время поддерживается только функциями на основе PowerShell. Это позволяет автоматически управлять зависимостями службой. Если для свойства `enabled` задано значение `true`, `requirements.psd1` файл обрабатывается. Зависимости обновляются при выпуске любых дополнительных версий. Дополнительные сведения см. в разделе [управляемая зависимость](functions-reference-powershell.md#dependency-management) в статье PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>SendGrid

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-sendgrid.md#host-json) (параметры файла host.json).

## <a name="servicebus"></a>serviceBus

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-service-bus.md#host-json) (параметры файла host.json).

## <a name="singleton"></a>singleton

Параметры конфигурации для одноэлементной блокировки. Дополнительные сведения см. в [проблеме, посвященной одноэлементной блокировке, на портале GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|lockPeriod|00:00:15|Период времени, на который применяются блокировки уровня функции. Эти блокировки возобновляются автоматически.| 
|listenerLockPeriod|00:01:00|Период времени, на который применяются блокировки прослушивателя.| 
|listenerLockRecoveryPollingInterval|00:01:00|Интервал времени, используемый для восстановления блокировки прослушивателя, если блокировку прослушивателя не удалось получить при запуске.| 
|lockAcquisitionTimeout|00:01:00|Максимальный период времени, за который среда выполнения будет пытаться получить блокировку.| 
|lockAcquisitionPollingInterval|Н/Д|Интервал между попытками получения блокировки.| 

## <a name="version"></a>версия

Строка версии `"version": "2.0"` является обязательной для приложения-функции, предназначенного для среды выполнения v2.

## <a name="watchdirectories"></a>watchDirectories

Набор [каталогов общего кода](functions-reference-csharp.md#watched-directories), изменения которого должны отслеживаться.  Гарантирует, что если код в этих каталогах изменится, то эти изменения будут применены вашими функциями.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как обновить файл host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ознакомьтесь с глобальными параметрами в переменных среды](functions-app-settings.md)
