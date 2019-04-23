---
title: Создание предупреждения о производительности с помощью Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как использовать Azure Monitor для контейнеров для создания пользовательских оповещений на основе журнала запросов для памяти и ЦП.
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
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791547"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Как настроить оповещения для проблем производительности в Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, которые развертываются экземпляры контейнеров Azure или на управляемых кластеров Kubernetes, размещенных в службе Azure Kubernetes (AKS).

В этой статье описывается, как включить оповещения в следующих ситуациях:

* Если использование ЦП или памяти на узлах кластера превышает указанный порог
* Когда уровень использования ЦП или памяти на любой контейнер в контроллере превышает указанный порог по сравнению с ограничения, установленного в соответствующий ресурс
* *NotReady* подсчитывает узел «состояние»
*  *Не удалось*, *ожидающие*, *Неизвестный*, *под управлением*, или *Succeeded* подсчитывает этап pod

Оповещение об высокой загрузки ЦП или использование памяти на узлах кластера, используйте запросы, которые позволяют создавать оповещения, метрики и оповещения об измерении метрики. Оповещения о метриках имеют меньшую задержку, чем оповещений журнала. Но оповещений журнала предоставляет расширенные запросы и более изощренных. Запросы сравнения даты и времени, к текущей с помощью оповещений журнала *теперь* оператор и переход обратно один час. (Azure Monitor для контейнеров все даты хранятся в формате UTC (UTC) формате.)

Если вы не знакомы с Azure Monitor оповещения, см. в разделе [Обзор оповещений в Microsoft Azure](../platform/alerts-overview.md) перед началом. Дополнительные сведения об оповещениях журнала запросов см. в разделе [оповещениях журналов в Azure Monitor](../platform/alerts-unified-log.md). Дополнительные сведения о оповещения о метриках см. в разделе [оповещения о метриках в Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Запросов поиска по журналу использования ресурсов
Каждый предупреждений сценарий поддерживается запросы в этом разделе. Они используются в шаге 7 [Создание оповещения](#create-an-alert-rule) этой статьи.

Следующий запрос вычисляет среднее использование ЦП как среднее значение использования ЦП узлов членов каждую минуту.  

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

Следующий запрос вычисляет средний показатель использования памяти как среднее значение использования памяти узлов членов каждую минуту.

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
>В следующих запросах используется значения заполнителей \<your имя кластера > и \<your контроллера name > для представления кластера и контроллера. При настройке оповещений, замените их значениями, относящиеся к используемой среде.

Следующий запрос вычисляет средний объем использования ЦП всех контейнеров в контроллере как среднее значение использования ЦП каждого экземпляра контейнера в контроллере каждую минуту. Измерение — процент лимита для контейнера.

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

Следующий запрос вычисляет средний показатель использования памяти всех контейнеров в контроллере как среднее значение использования памяти каждого экземпляра контейнера в контроллере каждую минуту. Измерение — процент лимита для контейнера.

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

Следующий запрос возвращает все узлы и счетчики, которые имеют состояние *готовы* и *NotReady*.

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
Следующий запрос возвращает засчитывается этап pod с учетом всех этапов. *Не удалось*, *ожидающие*, *Неизвестный*, *под управлением*, или *успешно*.  

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
>Для оповещения об определенных этапов pod, такие как *ожидающие*, *Failed*, или *Неизвестный*, измените последнюю строку запроса. Например, для создания предупреждения *FailedCount* использовать: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Создание правила оповещения
Выполните следующие действия для создания оповещения журнала в Azure Monitor с помощью одного правила поиска журнала, указанном выше.  

>[!NOTE]
>Следующую процедуру, чтобы создать правило генерации оповещений для использования ресурсов контейнера, необходимо переключиться на новый журнал оповещения API, как описано в разделе [предпочтения переключения API для оповещений журнала](../platform/alerts-log-api-switch.md).
>

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **монитор** на панели слева. В разделе **Insights**выберите **контейнеры**.
3. На **мониторинг кластеров** выберите кластер из списка.
4. В области слева в разделе **мониторинг**выберите **журналы** чтобы открыть страницу журналы Azure Monitor. Эта страница для написания и выполнения запросов Azure Log Analytics.
5. На **журналы** выберите **+ новое правило генерации оповещений**.
6. В **условие** выберите **— всякий раз, когда пользовательский поиск по журналам \<логика не определена >** предварительно определенных условий пользовательского журнала. **Пользовательский поиск по журналам** тип сигнала выбирается автоматически, поскольку мы создаем правило генерации оповещений непосредственно со страницы журналов Azure Monitor.  
7. Вставьте один из [запросы](#resource-utilization-log-search-queries) ранее указанный в **поисковый запрос** поля.
8. Настройте оповещения следующим образом:

    1. В раскрывающемся списке **На основе** выберите **Измерение метрик**. Измерение метрик создает оповещение для каждого объекта в запросе, содержащем значение выше заданного порогового значения.
    1. Для **условие**выберите **больше, чем**и введите **75** как базовый план начальной **пороговое значение**. Или введите другое значение, которое соответствует указанным критериям.
    1. В **триггера оповещения на основе** выберите **последовательные бреши**. В раскрывающемся списке выберите **больше, чем**и введите **2**.
    1. Чтобы настроить оповещение для контейнера ЦП или использование памяти, в разделе **агрегация по**выберите **ContainerName**. 
    1. В **оценено, на основе** задайте **период** значение **60 минут**. Правило будет выполняться каждые 5 минут и возвращает записи, которые были созданы в течение последнего часа от текущего времени. Задание периода к учетным записям ширину окна потенциальные задержки при передаче данных. Это также гарантирует, что запрос возвращает данных во избежание ложный отрицательный результат, в котором никогда не срабатывает оповещение.

9. Выберите **сделать** для выполнения правила генерации оповещений.
10. Введите имя в **имя правила генерации оповещений** поля. Укажите **описание** , предоставляющий сведения о предупреждении. И выберите соответствующий уровень серьезности уровень в параметрах.
11. Сразу же активировать правило генерации оповещений, примите значение по умолчанию для **включить правило при создании**.
12. Выберите существующую **группы действий** или создать новую группу. Это действие гарантирует, что те же действия будут создаваться каждый раз, активируется оповещение. Настройка на основе о том, как ИТ-СПЕЦИАЛИСТЫ или рабочая группа DevOps управляет инцидентов.
13. Выберите **создать правило генерации оповещений** для выполнения правила генерации оповещений. Оно начнет выполняться немедленно.

## <a name="next-steps"></a>Дальнейшие действия

* Представление [журнала примеры запросов](container-insights-analyze.md#search-logs-to-analyze-data) Дополнительные сведения о предопределенных запросов и примеры для оценки и настройки для других оповещений сценариев.
* Дополнительные сведения об Azure Monitor и отслеживать другие аспекты кластера AKS, см. в разделе [службе Azure Kubernetes представление работоспособности](container-insights-analyze.md).
