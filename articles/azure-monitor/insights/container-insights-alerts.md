---
title: Создание оповещений о производительности в Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как создать настраиваемые оповещения Azure на основе запросов к журналу для получения сведений об использовании памяти и ЦП с помощью Azure Monitor для контейнеров.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: 5bb0a727adcfb35b5d840a063b6fdb478d150953
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804830"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Как настроить оповещения для проблем производительности в Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). 

В этой статье описывается, как включить отправку предупреждений в следующих ситуациях:

* Если загрузка ЦП или памяти на узлах кластера превышает указанный порог.
* Если использование ЦП или памяти на любом из контейнеров в контроллере превышает заданным порогом по сравнению с ограничения, установленного на соответствующем ресурсе.
* **NotReady** подсчитывает узел «состояние»
* Подсчет этап POD **Failed**, **ожидающие**, **Неизвестный**, **под управлением**, или **Succeeded**

Для создания предупреждения, когда ЦП или памяти, что использование высокого уровня на узлах кластера, можно либо создать оповещение метрики или оповещения правило по измерению метрики с помощью запросов к журналу предоставляются. Оповещения метрик, имеют меньшую задержку, чем оповещений журнала, оповещения журнала предоставляет расширенные запросы и сложности, чем оповещения метрики. Для оповещений журнала запросов Сравните значения datetime с помощью оператора теперь текущей и возвращается один час. Все даты, которые хранятся в Azure Monitor для контейнеров, указаны в формате UTC.

Прежде чем начать, если вы не знакомы с оповещениями в Azure Monitor, см. в разделе [Обзор оповещений в Microsoft Azure](../platform/alerts-overview.md). Дополнительные сведения об оповещениях, с помощью журнала запросов, см. в разделе [оповещениях журналов в Azure Monitor](../platform/alerts-unified-log.md). Дополнительные сведения об оповещениях см. в разделе [оповещения о метриках в Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Запросов поиска по журналу использования ресурсов
Запросы в этом разделе предоставляются для поддержки каждого сценария оповещений. Запросы являются обязательными для шага 7 в разделе [Создание оповещения](#create-alert-rule) разделе ниже.  

Следующий запрос вычисляет средний уровень использования ЦП как среднее значение использования ЦП по всем узлам за каждую минуту.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Следующий запрос вычисляет среднее использование памяти как среднее значение использования памяти по всем узлам за каждую минуту.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Ниже запросах содержат строковые значения заполнителя для кластера и контроллера имен - <-имя вашего кластера-> и < your контроллера name >. Перед настройкой оповещений, замените заполнители значениями для конкретной среды. 


Следующий запрос вычисляет средний объем использования ЦП всех контейнеров в контроллере как среднее значение использования ЦП каждого экземпляра контейнера в контроллере каждую минуту в процентах от предела для контейнера.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Следующий запрос вычисляет средний показатель использования памяти всех контейнеров в контроллере как среднее значение использования памяти каждого экземпляра контейнера в контроллере каждую минуту в процентах от предела для контейнера.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Следующий запрос возвращает все узлы и count с состоянием **готовы** и **NotReady**.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Следующий запрос возвращает число pod этап зависимости на всех этапах — **Failed**, **ожидающие**, **Неизвестный**, **под управлением**, или **Успешно**.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Для оповещения об определенных этапов pod, такие как **ожидающие**, **Failed**, или **Неизвестный**, необходимо внести изменения в последней строке запроса. Например, для создания предупреждения *FailedCount* `| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`.  

## <a name="create-alert-rule"></a>Создать правило генерации оповещений
Выполните следующие действия для создания оповещения журнала в Azure Monitor с помощью одного из правил поиска журнала, указанным выше.  

>[!NOTE]
>Эта процедура требует переключиться на новые API оповещений Log, как описано в разделе [предпочтения переключения API для оповещений журнала](../platform/alerts-log-api-switch.md) при создании правила генерации оповещений для использования ресурсов контейнера. 
>

1. Войдите на [портале Azure](https://portal.azure.com).
2. На панели слева на портале Azure выберите **Монитор**. В разделе **Аналитика** выберите **Контейнеры**.    
3. На вкладке **Отслеживаемые кластеры** выберите из списка нужный кластер, щелкнув его имя.
4. На панели слева в разделе **Мониторинг** выберите **Журналы**, чтобы открыть страницу журналов Azure Monitor, где можно создать и выполнить запросы к Azure Log Analytics.
5. На странице **Журналы** щелкните **+ Новое правило генерации оповещений**.
6. В разделе **Условие** щелкните предварительно определенное пользовательское условие журнала с именем **Whenever the Custom log search is <logic undefined>** (Всякий раз, когда пользовательский поиск по журналу...). Для нас здесь автоматически устанавливается тип сигнала **Custom log search** (Пользовательский поиск по журналам), так как мы перешли к созданию правила генерации оповещений непосредственно со страницы журналов Azure Monitor.  
7. Вставьте один из [запросы](#resource-utilization-log-search-queries) ранее указанный в **поисковый запрос** поля. 

8. Настройте оповещение, указав следующие сведения:

    a. В раскрывающемся списке **На основе** выберите **Измерение метрик**. Измерение метрик позволяет создать оповещение для каждого объекта в запросе, для которого значение превышает указанное пороговое значение.  
    2. В поле **Условие**выберите **Больше, чем** и введите значение **75** в качестве начального **порогового значения** (или другое значение, которое соответствует вашим требованиям).  
    c. В поле **Условие срабатывания предупреждения** выберите **Последовательные бреши**, а из раскрывающегося списка выберите **Больше, чем** и введите значение **2**.  
    d. Если для настройки оповещения контейнера ЦП или использование памяти, в разделе **агрегация по** выберите **ContainerName** из раскрывающегося списка.  
    д. В разделе **Вычисляется на основе** задайте для параметра **Период** значение 60 (минут). Это правило будет запускаться каждые пять минут и возвращать все записи, созданные за последний час. Установка большего периода потенциально может привести к задержкам при передаче данных, но в то же время гарантирует, что запрос всегда будет возвращать данные. Это, в свою очередь, позволит избежать ложноотрицательных результатов, при которых оповещение не срабатывает. 

9. Щелкните **Готово**, чтобы завершить создание правила генерации оповещений.
10. Введите имя оповещения в поле **Имя правила генерации оповещений**. Укажите для оповещения подробное **описание** и выберите подходящий уровень серьезности из предложенных вариантов.
11. Чтобы правило генерации оповещений начало работать сразу после создания, сохраните установленный по умолчанию флажок **Включить правило при создании**.
12. На последнем шаге укажите или создайте **группу действий**, которая позволит гарантировать выполнение одинаковых действий при каждом срабатывании оповещения. Эту функцию можно использовать для каждого созданного правила. При настройке учитывайте, как управляют инцидентами ИТ-специалисты или DevOps в вашей организации. 
13. Щелкните **Создать правило генерации оповещений**, чтобы завершить создание этого правила. Оно начнет выполняться немедленно.

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [примеры запросов к журналу](container-insights-analyze.md#search-logs-to-analyze-data), чтобы разобраться в предопределенных запросах, оценить и внедрить примеры, настроить оповещения для других сценариев. 
* Чтобы продолжить изучение способов использования Azure Monitor и отслеживать другие аспекты кластера AKS, ознакомьтесь со статьей [Общие сведения о производительности кластера AKS с Azure Monitor для контейнеров](container-insights-analyze.md).
