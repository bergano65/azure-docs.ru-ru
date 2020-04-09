---
title: Справочник по файлу host.json для службы "Функции Azure" версии 2.x
description: Справочная документация по файлу host.json для Функций Azure в среде выполнения версии V2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1a861d500f0b8cc31b8312d6c955916ab741b649
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878258"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>host.json ссылка на Azure Функции 2.x и более поздние 

> [!div class="op_single_selector" title1="Выберите версию времени выполнения функций Azure, которые вы используете: "]
> * [Версия 1](functions-host-json-v1.md)
> * [Версия 2](functions-host-json.md)

Файл метаданных *host.json* содержит параметры глобальной конфигурации, влияющие на все функции приложения-функции. В этой статье перечислены параметры, доступные начиная с версии 2.x времени выполнения функций Azure.  

> [!NOTE]
> Эта статья предназначена для Azure Functions 2.x и более поздних версий.  Чтобы получить дополнительные сведения о файле host.json в Функции 1.x, см. статью [host.json reference for Azure Functions 1.x](functions-host-json-v1.md)(Справочник по файлу host.json для службы "Функции Azure" версии 1.x.).

Другие параметры конфигурации приложения функции управляются в [настройках приложения](functions-app-settings.md) (для развернутых приложений) или файле [local.settings.json](functions-run-local.md#local-settings-file) (для локальной разработки).

Конфигурации в host.json, связанные с привязками, применяются в равной степени к каждой функции в приложении функции. 

## <a name="sample-hostjson-file"></a>Пример файла host.json

В следующем примере файла *host.json* для версии 2.x имеет сяочащее количество всех возможных вариантов (за исключением любых, которые только для внутреннего использования).

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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
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

Параметры управления для анализа приложений, включая [варианты выборки.](./functions-monitoring.md#configure-sampling)

Для полной структуры JSON [example host.json file](#sample-hostjson-file)см.

> [!NOTE]
> Выборка журналов может привести к тому, что некоторые выполнения не будут отображаться в колонке монитора Application Insights. Чтобы избежать выборки журнала, добавьте `samplingExcludedTypes: "Request"` к значению. `applicationInsights`

| Свойство | Значение по умолчанию | Описание |
| --------- | --------- | --------- | 
| выборкаУстановка | Недоступно | Смотрите [приложениеInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Позволяет собирать живые метрики. |
| enableDependencyTracking | true | Позволяет отслеживать зависимость. |
| enablePerformanceCountersCollection | true | Позволяет собирать счетчики производительности Kudu. |
| liveMetrics ПервоначальнизацияРазить | 00:00:15 | Только для внутреннего использования. |
| httpAutoCollectionOptions | Недоступно | Смотрите [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| снимокНастройка | Недоступно | Смотрите [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingНастройки

|Свойство | Значение по умолчанию | Описание |
| --------- | --------- | --------- | 
| isEnabled | true | Включает или отключает выборку. | 
| maxTelemetryItemsPerSecond | 20 | Целевое количество элементов телеметрии, зарегистрированных в секунду на каждом узлах сервера. Если приложение работает на многих хостах, уменьшите это значение, чтобы оставаться в пределах общей целевой скорости трафика. | 
| оценкаInterval | 01:00:00 | Интервал, с которым переоценивается текущая скорость телеметрии. Вычисление выполняется на основе скользящего среднего. Вы можете сократить этот интервал, если наблюдаете неожиданные скачки данных телеметрии в сторону увеличения. |
| начальнаяВыборнаяпроцентная| 1.0 | Первоначальный процент выборки, применяемый в начале процесса отбора проб, динамически изменяется в процентном соотношении. Не уменьшаете значение во время отладки. |
| выборкаПроцентУвеличениеTimeout | 00:00:01 | При изменении значения процентного значения выборки это свойство определяет, как скоро после этого Application Insights будет разрешено снова повысить процент выборки, чтобы собрать больше данных. |
| выборкаПроцентDecreaseTimeout | 00:00:01 | При изменении значения процентного значения выборки это свойство определяет, как скоро после этого Application Insights может снова снизить процент выборки, чтобы захватить меньше данных. |
| minSamplingПроцент | 0,1 | Поскольку процент выборки варьируется, это свойство определяет минимальный допустимый процент выборки. |
| maxSamplingПроцент | 0,1 | Поскольку процент выборки варьируется, это свойство определяет максимально допустимый процент выборки. |
| movingAverageRatio | 1.0 | Взвешенное значение, присваиваемое последнему значению при вычислении скользящего среднего. Используйте значение не больше 1. Использование значений ниже рекомендуемых приводит к тому, что скорость реагирования алгоритма на резкие изменения замедляется. |
| исключеныТипы | null | Полуколонновый делимитировайся список типов, которые вы не хотите, чтобы быть пробы. Распознаваемые типы: Dependency, Event, Exception, PageView, Request, Trace. Все экземпляры указанных типов передаются; типы, которые не указаны, являются выборки. |
| включеныТипы | null | Полуколонновый делимитировайся список типов, которые вы хотите попробовать; пустой список подразумевает все типы. Введите `excludedTypes` перечисленные в переопределениятипах, перечисленных здесь. Распознаваемые типы: Dependency, Event, Exception, PageView, Request, Trace. Все экземпляры указанных типов передаются; типы, которые не указаны, являются выборки. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Свойство | Значение по умолчанию | Описание |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Включает или отключает расширенную информацию запроса HTTP для триггеров HTTP: входящие заголовки корреляции запросов, поддержка ключей мультиинструментации, метод HTTP, путь и ответ. |
| enableW3CDistributedTracing | true | Включает или отключает поддержку протокола распределенного отслеживания W3C (и включает устаревшую схему корреляции). Включен по `enableHttpTriggerExtendedInfoCollection` умолчанию, если это правда. Если `enableHttpTriggerExtendedInfoCollection` это неверно, этот флаг применяется только к исходящим запросам, а не к входящим запросам. |
| включитьResponseHeaderInjection | true | Позволяет или отсваивает впрыски нескольких компонентных заголовков корреляции в ответы. Включение инъекций позволяет Application Insights создавать карту приложений, когда используются несколько ключей приборов. Включен по `enableHttpTriggerExtendedInfoCollection` умолчанию, если это правда. Эта настройка не `enableHttpTriggerExtendedInfoCollection` применяется, если является ложной. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

Для получения дополнительной информации о снимках смотрите [снимки отогиотов на исключениях в приложениях .NET](/azure/azure-monitor/app/snapshot-debugger) и [проблемы устранения неполадок, позволяющие application Insights Snapshot Debugger или просмотра снимков.](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)

|Свойство | Значение по умолчанию | Описание |
| --------- | --------- | --------- | 
| agentEndpoint | null | Конечная точка, используемая для подключения к службе Snapshot Debugger Application Insights. Если она недействительна, используется конечная точка по умолчанию. |
| captureSnapshotMemoryWeight | 0,5 | Вес, приданный размерпамяти текущего процесса при проверке, достаточно ли памяти, чтобы сделать снимок. Ожидаемое значение превышает 0 надлежащей фракции (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Ограничение числа неудавшихся запросов на запрос моментальных снимков до отключения процессора телеметрии.|
| handleUntrackedExceptions | true | Позволяет или отключит отслеживание исключений, которые не отслеживаются телеметрией Application Insights. |
| isEnabled | true | Позволяет или отравливает коллекцию моментальных снимков | 
| isEnabledInDeveloperMode | false | В режиме разработчика включена коллекция моментальных снимков. |
| isEnabledWhenProfiling | true | Позволяет или отсрочивает создание моментального снимка, даже если Профиль Application Insights собирает подробный сеанс профилирования. |
| isExceptionSnappointsEnabled | false | Позволяет или отключит фильтрацию исключений. |
| isLowPrioritySnapshotUploader | true | Определяет, следует ли запускать процесс SnapshotUploader при ниже обычного приоритета. |
| maximumCollectionPlanSize | 50 | Максимальное количество проблем, которые мы можем отслеживать в любое время в диапазоне от одного до 9999. |
| maximumSnapshotsRequired | 3 | Максимальное количество снимков, собранных для одной проблемы, в диапазоне от одного до 999. Проблема может рассматриваться как индивидуальное заявление о броске в приложении. Как только количество моментальных снимков, собранных для проблемы, достигнет этого значения, больше не `problemCounterResetInterval`будет собираться снимки для этой проблемы до тех пор, пока счетчики проблем не будут сбросить (см.) и `thresholdForSnapshotting` не достигнут лимит снова. |
| проблемаCounterResetInterval | 24:00:00 | Как часто сбросить счетчики проблем в диапазоне от одной минуты до семи дней. Когда этот интервал достигается, все количество проблем сбросить до нуля. Существующие проблемы, которые уже достигли порога для выполнения моментальных снимков, но еще не сгенерировали количество моментальных снимков в, `maximumSnapshotsRequired`остаются активными. |
| обеспечитьАнонимныеТелеметрии | true | Определяет, отправлять ли оператору анонимного использования и телеметрии ошибок в корпорацию Майкрософт. Эта телеметрия может быть использована, если вы обратитесь в корпорацию Майкрософт, чтобы помочь устранить неполадки с Snapshot Debugger. Он также используется для мониторинга шаблонов использования. |
| Восстановить подключениеInterval | 00:15:00 | Как часто мы воссоединяемся с конечным пунктом Snapshot Debugger. Допустимый диапазон составляет от одной минуты до одного дня. |
| shadowCopyFolder | null | Определяет папку для использования для копирования теней в файлах. Если не установить, папки, указанные следующие переменные среды, опробованы в порядке: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Если это так, то только один экземпляр SnapshotUploader будет собирать и загружать снимки для нескольких приложений, которые разделяют InstrumentationKey. Если набор на ложные, SnapshotUploader будет уникальным для каждого (ProcessName, InstrumentationKey) tuple. |
| snapshotinLowPriorityThread | true | Определяет, обрабатывать или не обрабатывать моментальные снимки в потоке с низким приоритетом IO. Создание снимка — это быстрая операция, но для загрузки снимка в службу Snapshot Debugger он должен быть сначала записан на диск как minidump. Это происходит в процессе SnapshotUploader. Установка этого значения на истину использует низкоприоритетный io-код для написания мини-дампа, который не будет конкурировать с вашим приложением за ресурсы. Установка этого значения на ложное ускоряет создание minidump за счет замедления вашего приложения. |
| снимкиPerDayLimit | 30 | Максимальное количество снимков, разрешенных за один день (24 часа). Это ограничение также применяется на стороне службы Application Insights. Загрузка ставка ограничена 50 в день за приложение (т.е. на ключ приборов). Это значение помогает предотвратить создание дополнительных снимков, которые в конечном итоге будут отклонены во время загрузки. Значение нуля полностью удаляет ограничение, что не рекомендуется. |
| снимкиPerTenMinutesLimit | 1 | Максимальное количество снимков разрешено за 10 минут. Хотя верхняя граница этого значения отсутствует, будьте осторожны, увеличивая его на производственных нагрузках, поскольку это может повлиять на производительность приложения. Создание снимка происходит быстро, но создание мини-свалки снимка и его загрузка в службу Snapshot Debugger — это гораздо более медленная операция, которая будет конкурировать с вашим приложением для ресурсов (как процессора, так и ввоза). |
| tempFolder | null | Определяет папку для записи файлов журналов minidumps и uploader. Если не установить, то *%TEMP% -Dumps* используется. |
| порогДляснимок | 1 | Сколько раз Application Insights должен увидеть исключение, прежде чем запрашивать моментальные снимки. |
| uploaderProxy | null | Отменяет прокси-сервер, используемый в процессе snapshot Uploader. Возможно, вам придется использовать эту настройку, если ваше приложение подключается к Интернету через прокси-сервер. Коллектор моментального снимка выполняется в процессе приложения и будет использовать те же настройки прокси. Тем не менее, Snapshot Uploader выполняется как отдельный процесс, и вам может потребоваться настроить прокси-сервер вручную. Если это значение является нулевым, то коллекционер моментального снимка попытается автоматически обнаружить адрес прокси, изучая System.Net.WebRequestWebProxy и передавая значение snapshot Uploader. Если это значение не является нулевым, то автоматическое обнаружение не используется, и прокси-сервер, указанный здесь, будет использоваться в Snapshot Uploader. |

## <a name="cosmosdb"></a>СosmosDB

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-cosmosdb-v2-output.md#host-json) (параметры файла host.json).

## <a name="durabletask"></a>durableTask

Параметры конфигурации можно найти в разделе [host.json settings](durable/durable-functions-bindings.md#host-json) (параметры файла host.json).

## <a name="eventhub"></a>eventHub

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-event-hubs-output.md#host-json) (параметры файла host.json). 

## <a name="extensions"></a>extensions

Свойство, которое возвращает объект, содержащий все параметры определенной привязки, такие как [http](#http) и [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Пакеты расширения позволяют добавлять совместимый набор связывающих расширений функций в приложение функции. Чтобы узнать больше, смотрите [пакеты расширения для локального развития.](functions-bindings-register.md#extension-bundles)

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Список функций, которые выполняют узел заданий. Пустой массив означает выполнение всех функций. Предназначен для использования только при [локальном выполнении](functions-run-local.md). Чтобы отключить определенные функции в приложениях-функциях Azure, выполните следующие действия в разделе [Способы отключения функций в решении "Функции Azure"](disable-function.md) вместо использования этого параметра.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Указывает время ожидания для всех функций. Он следует за форматом строки timespan. В бессерверных планах потребления допускается диапазон от 1 секунды до 10 минут, а значение по умолчанию — 5 минут.  

В плане Premium допустимый диапазон составляет от 1 секунды до 60 минут, а значение по умолчанию — 30 минут.

В плане Выделенного (Службы приложений) нет общего ограничения, а значение по умолчанию составляет 30 минут. Значение неограниченного `-1` выполнения, но рекомендуется сохранить фиксированную верхнюю границу.

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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------| 
|Включено|true|Указывает, включена ли функция. | 
|healthCheckInterval|10 с|Интервал времени между периодическими фоновыми проверками работоспособности. | 
|healthCheckWindow|2 минуты|Скользящее окно времени, используемое в сочетании с параметром `healthCheckThreshold`.| 
|healthCheckThreshold|6|Максимальное количество попыток проверки работоспособности, которые могут завершиться сбоем, прежде чем инициируется повторный запуск.| 
|counterThreshold|0,80|Пороговое значение, при достижении которого счетчик производительности будет считаться неработоспособным.| 

## <a name="http"></a>http

Параметры конфигурации см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>Ведение журналов

Управляет поведением ведения журнала приложения-функции, включая Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Определяет, какой уровень журнала файла включен.  Доступны следующие параметры: `never`, `always` и `debugOnly`. |
|LogLevel|Недоступно|Объект, который определяет фильтрацию категорий журналов для функций в приложении. Версии 2.x и позже следуют ASP.NET макету Core для фильтрации категории журнала. Эта настройка позволяет фильтровать журнал для определенных функций. Дополнительные сведения см. в документации по использованию ASP.NET Core [Фильтрация журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). |
|console|Недоступно| Параметр ведения журнала [консоли](#console). |
|applicationInsights|Недоступно| Параметр [applicationInsights](#applicationinsights). |

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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------| 
|isEnabled|false|Включает или отключает ведение журнала консоли.| 

## <a name="manageddependency"></a>управляемыйЗависимость

Управляемая зависимость — это функция, которая в настоящее время поддерживается только с помощью функций PowerShell. Это позволяет автоматически управлять зависимостями службой. Когда `enabled` свойство настроено `true` `requirements.psd1` на, файл обрабатывается. Зависимости обновляются при выпуске каких-либо незначительных версий. Для получения дополнительной [информации см. Управляемую зависимость](functions-reference-powershell.md#dependency-management) в статье PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>SendGrid

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-sendgrid.md#host-json) (параметры файла host.json).

## <a name="servicebus"></a>serviceBus

Параметры конфигурации можно найти в разделе [host.json settings](functions-bindings-service-bus-output.md#host-json) (параметры файла host.json).

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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------| 
|lockPeriod|00:00:15|Период времени, на который применяются блокировки уровня функции. Эти блокировки возобновляются автоматически.| 
|listenerLockPeriod|00:01:00|Период времени, на который применяются блокировки прослушивателя.| 
|listenerLockRecoveryPollingInterval|00:01:00|Интервал времени, используемый для восстановления блокировки прослушивателя, если блокировку прослушивателя не удалось получить при запуске.| 
|lockAcquisitionTimeout|00:01:00|Максимальный период времени, за который среда выполнения будет пытаться получить блокировку.| 
|lockAcquisitionPollingInterval|Недоступно|Интервал между попытками получения блокировки.| 

## <a name="version"></a>version

Это значение указывает на версию схемы host.json. Строка `"version": "2.0"` версии необходима для функционального приложения, ориентированного на время выполнения v2, или более позднюю версию. Нет изменений схемы host.json между v2 и v3.

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
