---
title: Создание предупреждений о производительности для Azure Monitor для контейнеров Документы Майкрософт
description: В этой статье описывается, как создавать пользовательские оповещения на основе запросов журнала для памяти и использования процессора из Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730931"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>How to set up alerts for performance problems in Azure Monitor for containers (Сведения о настройке в Azure Monitor оповещений о проблемах с производительностью контейнеров)

Azure Monitor для контейнеров отслеживает производительность контейнерных нагрузок, развертываемых в контейнерных инстанциях Azure или управляемых кластерах Kubernetes, размещенных в службе Azure Kubernetes Service (AKS).

В этой статье описывается, как включить оповещения для следующих ситуаций:

- Когда использование процессора или памяти на кластерных узлах превышает порог
- Когда использование процессора или памяти на любом контейнере в контроллере превышает порог по сравнению с ограничением, установленным на соответствующем ресурсе
- *Неготовый* количество узлов состояния
- *Не удалось*, *В ожидании*, *Неизвестный*, *Запуск*, или *Успешно* под-фазы рассчитывает
- Когда свободное пространство диска на кластерных узлах превышает порог 

Чтобы предупредить о высоком использовании процессора или памяти или низком свободном пространстве диска на кластерных узлах, используйте запросы, которые предоставляются для создания предупреждения метрики или предупреждения измерения метрики. Метрические оповещения имеют более низкую задержку, чем оповещения о журнале. Но оповещения о журнале обеспечивают расширенный запрос и большую сложность. Запросы оповещения о журнале сравнивают время даты с настоящим, используя *теперь* оператора и возвращаясь один час назад. (Azure Monitor для контейнеров хранит все даты в формате Скоординированного универсального времени (UTC).)

Если вы не знакомы с оповещениями Azure Monitor, [см. Обзор оповещений в Microsoft Azure](../platform/alerts-overview.md) перед запуском. Чтобы узнать больше о оповещениях, которые используют запросы журналов, смотрите [оповещения журнала в Azure Monitor.](../platform/alerts-unified-log.md) Подробнее о метриках читайте [в данных Azure Monitor.](../platform/alerts-metric-overview.md)

## <a name="resource-utilization-log-search-queries"></a>Запросы поиска журнала использования ресурсов

Запросы в этом разделе поддерживают каждый сценарий оповещения. Они используются в шаге 7 раздела [оповещения создания](#create-an-alert-rule) этой статьи.

Следующий запрос вычисляет среднее использование процессора как среднее использование процессора узлов члена каждую минуту.  

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

Следующий запрос вычисляет среднее использование памяти как среднее использование памяти узлов элемента каждую минуту.

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
>В следующих запросах используются \<значения заполнителя \<> и>-контроллер-имя для представления кластера и контроллера. Замените их значениями, специфичными для среды при настройке оповещений.

Следующий запрос вычисляет среднее использование процессора всех контейнеров в контроллере как среднее использование процессора каждого экземпляра контейнера в контроллере каждую минуту. Измерение представляет собой процент от лимита, установленного для контейнера.

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

Следующий запрос вычисляет среднее использование памяти всех контейнеров в контроллере как среднее использование памяти каждого экземпляра контейнера в контроллере каждую минуту. Измерение представляет собой процент от лимита, установленного для контейнера.

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

Следующий запрос возвращает все узлы и счета, которые имеют статус *Ready* and *NotReady.*

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
Следующий запрос возвращает стручок фазы рассчитывает на основе всех фаз: *Не удалось*, *В ожидании*, *Неизвестный*, *Запуск*, или *Успешно*.  

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
>Чтобы предупредить на определенных фазах стручка, таких как *Pending,* *Failed*или *Unknown,* измените последнюю строку запроса. Например, чтобы предупредить об использовании *FailedCount:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Следующий запрос возвращает диски кластерных узлов, которые превышают 90% свободного свободного пространства. Чтобы получить идентификатор кластера, сначала запустите `ClusterId` следующий запрос и скопируйте значение из свойства:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Создание правила оповещения

Выполните эти действия, чтобы создать оповещение о журнале в Azure Monitor, используя одно из правил поиска журналов, которое было предоставлено ранее. Для создания с помощью шаблона ARM см. [Создание оповещения «Образец журнала» с помощью шаблона ресурсов Azure.](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)

>[!NOTE]
>Следующая процедура создания правила оповещения для использования ресурсов контейнера требует переключения на новый API оповещений о журнале, как описано в [предпочтении Switch API для оповещений о журнале.](../platform/alerts-log-api-switch.md)
>

1. Войдите на [портал Azure](https://portal.azure.com).
2. На портале Azure поиск и выбор **рабочих областей анализа журналов.**
3. В списке рабочих областей анализа журналов выберите рабочее пространство, поддерживающее Azure Monitor для контейнеров. 
4. В панели на левой стороне выберите **журналы,** чтобы открыть страницу журналов Azure Monitor. Вы используете эту страницу для записи и выполнения запросов Analytics журналов Azure.
5. На странице **журналов** вставьте один из [запросов, предоставленных](#resource-utilization-log-search-queries) ранее, в поле **поиска запросов,** а затем выберите **Run** для проверки результатов. Если вы не выполняете этот шаг, опция **оповещения «Новый»** недоступна для выбора.
6. Выберите **«Новое оповещение»** для создания оповещения о журнале.
7. В разделе **Состояние** выберите **Всякий раз, \<когда пользовательский поиск журнала является логикой неопределенный>** заранее определенные пользовательские условия журнала. **Пользовательский** тип сигнала поиска журнала автоматически выбирается, потому что мы создаем правило оповещения непосредственно со страницы журналов Azure Monitor.  
8. Вставьте один из [запросов, представленных](#resource-utilization-log-search-queries) ранее в поле **поиска запроса.**
9. Наверстой оповещения следующим образом:

    1. Из **списка** выпадающих списков выберите **метрическое измерение.** Измерение метрики создает оповещение для каждого объекта в запросе, которое имеет значение выше указанного порога.
    1. Для **условия**, выберите **больше, чем**, и введите **75** в качестве исходного **порога** для процессора и оповещения об использовании памяти. Для предупреждения о низком пространстве диска введите **90**. Или введите другое значение, которое соответствует вашим критериям.
    1. В **оповещении триггера на основе** раздела выберите **последовательные нарушения.** Из списка выпадающих вниз, выберите **больше, чем**, и введите **2**.
    1. Для настройки оповещения для использования процессора контейнера или использования памяти, под **агрегатом на**, выберите **ContainerName.** Для настройки для кластерного узла низкого диска оповещения, выберите **ClusterId.**
    1. В **разделе Оценка на основе** раздела установите значение **периода** до **60 минут.** Правило будет работать каждые 5 минут и возвращать записи, которые были созданы в течение последнего часа с текущего времени. Установка периода времени в широком окне учитывает потенциальную задержку данных. Он также гарантирует, что запрос возвращает данные, чтобы избежать ложного негатива, в котором оповещение никогда не срабатывается.

10. Выберите **«Сделано»** для выполнения правила оповещения.
11. Введите имя в поле **имени правила оповещения.** Укажите **описание,** которое содержит подробную информацию о предупреждении. И выберите соответствующий уровень серьезности из предусмотренных вариантов.
12. Чтобы немедленно активировать правило оповещения, примите значение по умолчанию для **правила Enable при создании.**
13. Выберите существующую **Группу действий** или создайте новую группу. Этот шаг гарантирует, что одни и те же действия принимаются каждый раз при срабатывании оповещения. Наосновение на основе того, как ваша иТ-группа или команда devOps управляет инцидентами.
14. Выберите **правило оповещения«Создать** для завершения правила оповещения. Оно начнет выполняться немедленно.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотр [примеров запросов журналов](container-insights-log-search.md#search-logs-to-analyze-data) для просмотра заранее определенных запросов и примеров для оценки или настройки для оповещения, визуализации или анализа кластеров.
- Чтобы узнать больше о Azure Monitor и о том, как отслеживать другие аспекты кластера Kubernetes, смотрите [производительность кластера Kubernetes](container-insights-analyze.md) и [view Kubernetes кластерного состояния.](container-insights-health.md)
