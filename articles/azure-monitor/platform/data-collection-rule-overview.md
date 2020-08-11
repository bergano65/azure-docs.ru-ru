---
title: Правила сбора данных в Azure Monitor (Предварительная версия)
description: Общие сведения о правилах сбора данных (DCR) в Azure Monitor включая их содержимое и структуру, а также способ создания и работы с ними.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: af5324373359cea643a3e31b6bb94e614ddb7e36
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083183"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Правила сбора данных в Azure Monitor (Предварительная версия)
Правила сбора данных (ДКР) определяют данные, поступающие в Azure Monitor, и указывают, куда должны отправляться или храниться эти данные. В этой статье приводятся общие сведения о правилах сбора данных, включая их содержимое и структуру, а также способах создания и работы с ними.

## <a name="input-sources"></a>Источники входных данных
В настоящее время правила сбора данных поддерживают следующие источники данных:

- Виртуальная машина Azure с агентом Azure Monitor. См. раздел [Настройка сбора данных для агента Azure Monitor (Предварительная версия)](data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Компоненты правила сбора данных
Правило сбора данных включает следующие компоненты.

| Компонент | Описание |
|:---|:---|
| Источники данных | Уникальный источник данных мониторинга с собственным форматом и методом предоставления данных. Примеры источников данных включают в себя журнал событий Windows, счетчики производительности и syslog. Каждый источник данных соответствует определенному типу источника данных, как описано ниже. |
| Потоки | Уникальный описатель, описывающий набор источников данных, которые будут преобразованы и схематизированных как один тип. Каждому источнику данных требуется один или несколько потоков, а один поток может использоваться несколькими источниками данных. Все источники данных в потоке совместно используют общую схему. Используйте несколько потоков, например, если требуется отправить определенный источник данных в несколько таблиц в одной рабочей области Log Analytics. |
| Назначения | Набор назначений, куда должны отправляться данные. Примеры включают Log Analytics рабочую область, Azure Monitor метрики и концентраторы событий Azure. | 
| Потоки данных | Определение, какие потоки должны отправляться в назначения. | 

На следующей диаграмме показаны компоненты правила сбора данных и их связь.

[![Схема ДКР](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Типы источников данных
Каждый источник данных имеет тип источника данных. Каждый тип определяет уникальный набор свойств, которые должны быть указаны для каждого источника данных. Доступные в настоящее время типы источников данных показаны в следующей таблице.

| Тип источника данных | Описание | 
|:---|:---|
| Расширение | Источник данных на основе расширения виртуальной машины |
| performanceCounters | Счетчики производительности для Windows и Linux |
| syslog | События системного журнала на виртуальной машине Linux |
| виндовсевентлогс | Журнал событий Windows |


## <a name="limits"></a>Ограничения
В следующей таблице перечислены ограничения, которые в настоящее время применяются к каждому правилу сбора данных.

| Ограничение | Значение |
|:---|:---|
| Максимальное количество источников данных | 10 |
| Максимальное число спецификаторов счетчиков в производительности | 100 |
| Максимальное число имен устройств в системном журнале | 20 |
| Максимальное число запросов XPath в журнале событий | 100 |
| Максимальное число потоков данных | 10 |
| Максимальное число потоков данных | 10 |
| Максимальное число расширений | 10 |
| Максимальный размер параметров расширения | 32 КБ |
| Максимальное Log Analytics рабочих областей | 10 |


## <a name="create-a-dcr"></a>Создание ДКР
В настоящее время существует два доступных метода для создания ДКР:

- [Используйте портал Azure](data-collection-rule-azure-monitor-agent.md) , чтобы создать правило сбора данных и связать его с одной или несколькими виртуальными машинами.
- Непосредственно измените правило сбора данных в JSON и отправьте его с помощью REST API.

## <a name="sample-data-collection-rule"></a>Правило сбора образцов данных
Приведенное ниже правило сбора образцов данных предназначено для виртуальных машин с агентом управления Azure и содержит следующие сведения.

- Данные о производительности
  - Собирает данные счетчиков "процессор", "память", "логический диск" и "физический диск каждые 15 секунд" и отправляет каждую минуту.
  - Собирает данные счетчиков конкретных процессов каждые 30 секунд и отправляет их каждые 5 минут.
- События Windows
  - Собирает события безопасности Windows и передает каждую минуту.
  - Собирает события приложения и системы Windows и передает данные каждые 5 минут.
- Системный журнал
  - Собирает события отладки, критической и аварийной ситуации из механизма cron.
  - Собирает предупреждения, критические события и аварийные ситуации из средства syslog.
- Назначения
  - Отправляет все данные в Log Analytics рабочую область с именем Централтеамворкспаце.
  - Отправляет данные о производительности в метрики Azure Monitor в текущей подписке.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "sylogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Дальнейшие действия

- [Создайте правило сбора данных](data-collection-rule-azure-monitor-agent.md) и привязку к нему из виртуальной машины с помощью агента Azure Monitor.