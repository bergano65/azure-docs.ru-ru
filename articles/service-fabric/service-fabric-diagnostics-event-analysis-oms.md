---
title: Журналы Azure анализ событий Service Fabric с помощью Azure Monitor | Документация Майкрософт
description: Дополнительные сведения о визуализации и анализе событий с помощью журналов Azure Monitor для мониторинга и диагностики кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: ba4923edbc59f0e6650fda1a71e1c4f79b884cf2
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662165"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Анализ и визуализация с помощью Azure Monitor журналов событий
 Azure Monitor собирает и анализирует данные телеметрии от приложений и служб, размещенных в облаке, и предоставляет средства анализа, с помощью которых вы сможете максимально увеличить их доступность и производительность. В этой статье описывается, как выполнять запросы в журналах Azure Monitor для анализа и устранения неполадок, выполняемых в кластере. Рассматриваются следующие распространенные вопросы:

* Как устранить неполадки событий работоспособности?
* Как узнать, что узел вышел из строя?
* Как узнать, запущены или остановлены ли службы приложения?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Общие сведения о рабочей области Log Analytics

>[!NOTE] 
>Хранилище диагностики по умолчанию включено в кластере во время создания, но вам нужно настроить рабочую область Log Analytics для чтения из хранилища диагностики.

Azure Monitor регистрирует собирает данные из управляемых ресурсов, включая таблицу службы хранилища Azure или агента и сохраняет их в центральный репозиторий. Эти данные далее могут использоваться для анализа, оповещения, визуализации или последующего экспорта. Azure Monitor регистрирует поддерживает события, данные о производительности или любые иные пользовательские данные. Ознакомьтесь с [действия, чтобы настроить расширение системы диагностики для статистической обработки событий](service-fabric-diagnostics-event-aggregation-wad.md) и [действия, чтобы создать рабочую область Log Analytics для чтения из событий в хранилище](service-fabric-diagnostics-oms-setup.md) чтобы убедиться, что данные передаются в Azure Monitor журналы.

После получения данных по Azure Monitor журналы Azure имеет несколько *мониторинг решений* , которые эти решения оптимизированы или панели мониторинга оперативных для отслеживания входящих данных, для нескольких сценариев. К ним относятся *Аналитика Service Fabric* и *Контейнеры* — два наиболее важных решения для диагностики и мониторинга кластеров Service Fabric. В этой статье описывается использование решения "Аналитика Service Fabric", созданного с помощью рабочей области.

## <a name="access-the-service-fabric-analytics-solution"></a>Получение доступа к решению "Аналитика Service Fabric"

В [портал Azure](https://portal.azure.com), перейдите в группу ресурсов, в котором вы создали решение аналитики Service Fabric.

Выберите ресурс **ServiceFabric\<имя_рабочей_области_OMS\>**.

В `Summary` вы увидите плитки в форме графа для каждого включенного решения, включая одну для Service Fabric. Щелкните граф **Service Fabric**, чтобы продолжить решение службы Fabric Analytics.

![Решение Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

На приведенном ниже рисунке показана домашняя страница решения "Аналитика Service Fabric". На этой домашней странице представлена информация, касающаяся работы вашего кластера.

![Решение Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Если включить диагностику во время создания кластера, можно просмотреть такие события: 

* [События кластера Service Fabric](service-fabric-diagnostics-event-generation-operational.md).
* [События модели программирования на основе Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
* [События модели программирования на основе Reliable Services](service-fabric-reliable-services-diagnostics.md).

>[!NOTE]
>Помимо готовых событий Service Fabric, можно собирать дополнительные системные события. Для этого [обновите файл конфигурации расширения диагностики](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Просмотр событий Service Fabric, включая действия на узлах

На странице аналитики Service Fabric щелкните диаграмму для **События Service Fabric**.

![Операционный канал решения Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Щелкните **Список**, чтобы просмотреть список событий. Здесь вы увидите все собранные системные события. Эти данные взяты из таблицы **WADServiceFabricSystemEventsTable** в учетной записи хранения Azure, а события служб Reliable Services и субъектов Reliable Actors, которые показаны далее, также взяты из этих соответствующих таблиц.
    
![Операционный канал запроса](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Кроме того, чтобы найти необходимые данные, можно щелкнуть значок лупы в левой части экрана и воспользоваться языком запросов Kusto. Например, чтобы найти все действия, выполняемые в узлах кластера, можно использовать приведенный ниже запрос. Идентификаторы событий, используемые ниже, можно найти в [справочнике по событиям операционного канала](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Вы можете запрашивать много дополнительных полей (например, конкретные узлы (Компьютер) и системную службу (TaskName)).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Просмотр событий служб Reliable Services и субъектов Reliable Actors в Service Fabric

На странице аналитики Service Fabric щелкните диаграмму для **Reliable Services**.

![Reliable Services в решении Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Щелкните **Список**, чтобы просмотреть список событий. Здесь можно просмотреть события из служб Reliable Services. Когда служба RunAsync запускается и завершается, могут отображаться различные события. Обычно это происходит при развертывании и обновлении. 

![Запрос к Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Аналогичным образом можно просмотреть события субъектов Reliable Actors. Чтобы настроить более подробные события для субъектов Reliable Actors, необходимо изменить `scheduledTransferKeywordFilter` в конфигурации для расширения диагностики (см. ниже). Сведения об этих значениях можно найти в [справочнике по событиям субъектов Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Язык запросов Kusto предоставляет широкие возможности. Вы можете выполнить другой полезный запрос, чтобы узнать, какие узлы создают больше событий. Запрос на снимке экрана, приведенном ниже, показывает операционное событие Service Fabric агрегированное с конкретной службой и узлом.

![События запросов на каждом узле](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы включить мониторинг инфраструктуры (т. е. счетчики производительности), перейдите к разделу о [добавлении агента Log Analytics](service-fabric-diagnostics-oms-agent.md). Агент собирает счетчики производительности и добавляет их в имеющуюся рабочую область.
* Для локальных кластерах журналы Azure Monitor предлагает шлюз (HTTP прокси-сервер переадресации), который может использоваться для отправки данных в журналы Azure Monitor. Дополнительные сведения о том, что в [подключение компьютеров без доступа к Интернету с помощью шлюза Log Analytics журналы Azure Monitor](../azure-monitor/platform/gateway.md).
* Настройте [автоматические оповещения](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью журналов Azure Monitor.
* Более подробные сведения журналов Azure Monitor и ее возможностях, ознакомьтесь со статьей [что такое журналы Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md).
