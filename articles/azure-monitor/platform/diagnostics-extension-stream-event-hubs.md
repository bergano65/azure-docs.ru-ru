---
title: Отправка данных из расширения диагностики Windows Azure в концентраторы событий Azure
description: Настройте расширение диагностики в Azure Monitor для отправки данных в концентратор событий Azure, чтобы вы могли пересылать их в места за пределами Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672537"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Отправка данных из расширения диагностики Windows Azure в концентраторы событий Azure
Расширение azure diagnostics является агентом Azure Monitor, который собирает данные мониторинга с гостевой операционной системы и рабочие нагрузки виртуальных машин Azure и других вычислительных ресурсов. В этой статье описывается, как отправлять данные из расширения Windows Azure Diagnostic (WAD) в [концентраторы событий Azure,](https://azure.microsoft.com/services/event-hubs/) чтобы можно было пересылать их в места за пределами Azure.

## <a name="supported-data"></a>Поддерживаемые данные

Данные, собранные из гостевой операционной системы, которые могут быть отправлены в Event Hubs, включают в себя следующее. Другие источники данных, собранные WAD, включая журналы IIS и сбои, не могут быть отправлены в концентраторы событий.

* Трассировка событий Windows (ETW)
* Счетчики производительности
* Журналы событий Windows, включая журналы приложений в журнале событий Windows
* Журналы инфраструктуры системы диагностики Azure

## <a name="prerequisites"></a>Предварительные требования

* Расширение диагностики Windows 1.6 или выше. См. [версии схемы схемы расширения Azure Diagnostics и историю](diagnostics-extension-versions.md) для истории версий и [расширенного обзора Azure Diagnostics](diagnostics-extension-overview.md) для поддерживаемых ресурсов.
* Пространство имен событий Концентраторы всегда должно быть подготовлено. Подробную информацию смотрите [в концентрах событий.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Схема конфигурации
Смотрите [расширение диагностики Windows Azure (WAD)](diagnostics-extension-windows-install.md) для различных опций включения и настройки расширения диагностики и [схемы конфигурации Azure Diagnostics](diagnostics-extension-schema-windows.md) для ссылки на схему конфигурации. В остальной части этой статьи будет описано, как использовать эту конфигурацию для отправки данных в концентратор событий. 

Azure Diagnostics всегда отправляет журналы и метрики в учетную запись хранения Azure. Можно настроить одну или несколько *топов данных,* которые отправляют данные в дополнительные места. Каждая раковина определяется в [элементе SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) публичной конфигурации с конфиденциальной информацией в частной конфигурации. В этой конфигурации для концентраторов событий используются значения в следующей таблице.

| Свойство | Описание |
|:---|:---|
| name | Описательное название раковины. Используется в конфигурации для указания источников данных для отправки в раковину. |
| Url  | Url концентратора \<событий в форме event-hubs-namespace\>.servicebus.windows.net/\<event-hub-name\>.          |
| SharedAccessKeyName | Название политики общего доступа для концентратора событий, который имеет по крайней мере **полномочия Отправки.** |
| SharedAccessKey     | Основной или вторичный ключ из политики общего доступа для концентратора событий. |

Приведены ниже примеры общедоступных и частных конфигураций. Это минимальная конфигурация с одним счетчиком производительности и журналом событий, чтобы проиллюстрировать, как настроить и использовать раковину данных концентратора событий. Для более сложного примера смотрите [схему конфигурации Azure Diagnostics.](diagnostics-extension-schema-windows.md)

### <a name="public-configuration"></a>Открытая конфигурация

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Закрытая конфигурация

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Варианты настройки
Чтобы отправить данные в раковину данных, вы указываете атрибут **раковины** на узлах источника данных. Место размещения **атрибута раковин** определяет область назначения. В следующем примере атрибут **раковинопределяется** в **узеле PerformanceCounters,** который приведет к отправке всех счетчиков производительности ребенка в концентратор событий.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


В следующем примере атрибут **раковины** применяется непосредственно к трем счетчикам, что приведет к отправке в концентратор событий только те счетчики производительности. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Конфигурация проверки
Для проверки того, что данные отправляются в концентратор событий, можно использовать различные методы. Не простой метод заключается в использовании захвата концентраторов событий, как описано в [событиях захвата через концентраторы событий Azure в Azure Blob Storage или Azure Data Lake Storage.](../../event-hubs/event-hubs-capture-overview.md) 


## <a name="troubleshoot-event-hubs-sinks"></a>Устранение неполадок приемников Центров событий

- Посмотрите таблицу хранения данных Azure **WADDiagnosticInfrastructureLogsTable,** которая содержит журналы и ошибки для самой Azure Diagnostics. Для этого можно использовать, например, [обозреватель хранилищ Azure](https://www.storageexplorer.com). Подключитесь к соответствующей учетной записи хранения, просмотрите эту таблицу и выполните запрос по метке времени за последние 24 часа. С помощью этого инструмента можно экспортировать информацию в CSV-файл и открыть его в таком приложении, как Microsoft Excel. Excel упрощает поиск строк телефонных карточек, таких как **EventHubs**, чтобы узнать, какая обнаружена ошибка.  

- Убедитесь в том, что концентратор событий успешно подготовлен. Вся информация о подключении в разделе **PrivateConfig** конфигурации должна соответствовать значениям вашего ресурса, увиденным на портале. Убедитесь, что на портале определена политика SAS *(SendRule* в примере) и выдается разрешение *на отправку.*  

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения о Центрах событий](../../event-hubs/event-hubs-about.md)
* [Создание концентратора событий](../../event-hubs/event-hubs-create.md)
* [Центры событий: часто задаваемые вопросы](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



