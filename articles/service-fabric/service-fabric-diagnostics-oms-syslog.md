---
title: Мониторинг событий кластера Linux в Azure Service Fabric | Документация Майкрософт
description: Сведения о мониторинге событий кластера Linux из системного журнала
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 25452d3d65518511c47087d1cb712d0a512416fc
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245562"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>События кластера Service Fabric под управлением Linux в системном журнале

Service Fabric предоставляет набор событий платформы, информирующих о важных действиях, выполняемых в кластере. Полный список предоставленных событий доступен [здесь](service-fabric-diagnostics-event-generation-operational.md). Существует множество способов использования этих событий. В данной статье мы рассмотрим настройку Service Fabric для записи этих событий в системный журнал.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Общие сведения

В выпуске 6.4 был представлен SyslogConsumer для отправки событий платформы Service Fabric в системный журнал кластеров Linux. После включения события будут автоматически передаваться в системный журнал, который собирает и отправляет агент Log Analytics.

Каждое событие системного журнала состоит из 4 компонентов.
* Facility
* Удостоверение
* Сообщение
* Уровень серьезности

SyslogConsumer записывает все события платформы с помощью средства `Local0`. Любое допустимое средство можно обновить, изменив файл конфигурации. Используемое удостоверение — `ServiceFabric`. Поле "Сообщение" содержит все событие, сериализованное в JSON, чтобы различные средства могли его запрашивать и использовать. 

## <a name="enable-syslogconsumer"></a>Включение Syslog Consumer

Для включения SyslogConsumer необходимо обновить кластер. Раздел `fabricSettings` необходимо обновить с помощью следующего кода. Обратите внимание, этот код включает только разделы, связанные с SyslogConsumer.

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Необходимо вызвать следующие изменения:
1. В разделе Common находится новый параметр с именем `LinuxStructuredTracesEnabled`. **Он необходим для структурирования и сериализации событий Linux при отправке в системный журнал.**
2. В разделе Diagnostics добавлен новый параметр ConsumerInstance: SyslogConsumer. Он сообщает платформе, что доступен другой потребитель событий. 
3. В разделе нового параметра SyslogConsumer для `IsEnabled` должно быть установлено значение `true`. Этот параметр настроен для автоматического использования средства Local0. Вы можете переопределить его, добавив еще один параметр.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Интеграция журналов Azure Monitor
Вы можете прочитать эти события системного журнала в средство наблюдения, таких как журналы Azure Monitor. Вы можете создать рабочую область Log Analytics с помощью Azure Marketplace, выполнив эти [инструкции].(../azure-monitor/learn/quick-create-workspace.md). Кроме того, вам необходимо добавить в кластер агент Log Analytics для сбора и отправки этих данных в рабочую область. Этот же агент используется для сбора счетчиков производительности. 

1. Перейдите к колонке `Advanced Settings`.

    ![Параметры рабочей области](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Щелкните `Data`.
3. Щелкните `Syslog`.
4. Настройте Local0 в качестве средства для отслеживания. Вы можете добавить другое средство, если вы изменили его в разделе fabricSettings.

    ![Настройка системного журнала](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Перейдите в проводник запросов, щелкнув `Logs` в меню ресурсов рабочей области, чтобы выполнить запрос.

    ![Журналы рабочей области](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Вы можете выполнить запрос к таблице `Syslog`, выполнив поиск `ServiceFabric` в качестве имени процесса. Приведенный ниже запрос является примером анализа JSON в событии и отображения его содержимого.

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Запрос системного журнала](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

В приведенном выше примере описано событие NodeDown. Полный список событий см. [здесь](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Разверните агент Log Analytics](service-fabric-diagnostics-oms-agent.md) на узлах для сбора данных счетчиков производительности и статистики Docker, а также журналов для контейнеров.
* Ознакомьтесь с функциями [поиска и запроса журналов](../log-analytics/log-analytics-log-searches.md) функции, предоставляемые как часть журналов Azure Monitor
* [Использование конструктора представлений для создания пользовательских представлений в журналах Azure Monitor](../log-analytics/log-analytics-view-designer.md)
* Справочник по как [Azure Monitor входит интеграция с Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
