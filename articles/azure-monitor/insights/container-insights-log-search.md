---
title: Как войти в журналы запросов от Azure Monitor для контейнеров Документы Майкрософт
description: Azure Monitor для контейнеров собирает метрики и данные журналов, и эта статья описывает записи и включает в себя примерные запросы.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333480"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Как запросить журналы из Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает метрики производительности, данные о запасах и сведения о состоянии работоспособности из узлов и контейнеров контейнеров и направляет ее в рабочее пространство журнала Analytics в Azure Monitor. Эти данные собираются каждые три минуты. Эти данные доступны для [запроса](../../azure-monitor/log-query/log-query-overview.md) в Azure Monitor. Эти данные используются в различных сценариях, таких как планирование миграции, анализ емкости, обнаружение и устранение проблем с производительностью по требованию.

## <a name="container-records"></a>Записи контейнеров

В таблице ниже приведены примеры записей, собранных Azure Monitor для контейнеров, и типов данных, которые отображаются в результатах поиска по журналам.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Журнал контейнеров | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Список узлов контейнеров | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Список модулей pod в кластере Kubernetes | `KubePodInventory` | TimeGenerated, Компьютер, Clusterid, ContainerCreationTimeStamp, Poduid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Список узлов кластера Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| События Kubernetes. | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Службы в кластере Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Метрики производительности для узлов кластера Kubernetes | Perf &#124; где ObjectName -" K8SNode" | Компьютер, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRsSBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Метрики производительности контейнеров кластера Kubernetes | Perf &#124; где ObjectName -" K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Пользовательские метрики |`InsightsMetrics` | Компьютер, Имя, Название, Происхождение, SourceSystem, Теги<sup>1</sup>, TimeGenerated, Тип, Va, _ResourceId | 

<sup>1</sup> *Свойство тегов* представляет [несколько измерений](../platform/data-platform-metrics.md#multi-dimensional-metrics) для соответствующей метрики. Дополнительную информацию о метриках, `InsightsMetrics` собранных и хранящихся в таблице, и описание свойств записей можно получить, смотрите [обзор InsightsMetrics.](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)

## <a name="search-logs-to-analyze-data"></a>Поиск по журналам для анализа данных

Журналы Azure Monitor помогут вам найти тенденции, диагностировать узкие места, прогнозировать или соотносить данные, которые могут помочь определить оптимальную ли конфигурацию кластера. Вам доступны предопределенные запросы поиска по журналам, которые можно использовать без изменений или настроить для получения сведений нужным вам образом.

Вы можете выполнить интерактивный анализ данных в рабочей области, выбрав **журналы событий View Kubernetes** или опцию **журналов контейнеров View** в панели предварительного просмотра из списка **выпадающих аналитических** данных. Страница **поиска по журналам** появится в правой области страницы портала Azure, которую вы использовали.

![Анализ данных в Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Выход контейнерных журналов, переадресованный в рабочее пространство, - это STDOUT и STDERR. Так как Azure Monitor отслеживает среду Kubernetes (AKS) под управлением Azure, сейчас сведения о системе Kube не собираются ввиду большого объема создаваемых данных. 

### <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам

При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии со своими требованиями. Можно поэкспериментировать с приведенными ниже примерами запросов, чтобы научиться создавать более сложные запросы.

| Запрос | Описание | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Вывод всех сведений о жизненном цикле контейнера| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | События Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Инвентаризация образа | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Ресурсы ЦП контейнера. | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Память контейнера |
| ИнсайтсМетрики<br> &#124;, где имя q » "requests_count"<br> &#124; суммировать Вальеани (Валь) по TimeGenerated'bin (TimeGenerated, 1m)<br> &#124; сортировать по TimeGenerated asc<br> &#124; проект ЗапросыPerMinute - Val - prev (Val), TimeGenerated <br> &#124; визуализировать барчарт  | Запросы за минуту с пользовательскими метриками |

## <a name="query-prometheus-metrics-data"></a>Данные метрик запроса Prometheus

Следующим примером является запрос метрик Prometheus, показывающий считывание диска в секунду на узла хулит.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Для просмотра метрик Prometheus, отскрабленных Azure Monitor, отфильтрованным Namespace, укажите "prometheus". Вот пример запроса для просмотра метрик Prometheus из пространства имен `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Данные Prometheus также могут быть непосредственно запрошены по имени.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Ошибки конфигурации запроса или соскабливания

Чтобы исследовать любую конфигурацию или ошибки соскоб, `KubeMonAgentEvents` следующий пример запроса возвращает информационные события из таблицы.

```
KubeMonAgentEvents | where Level != "Info" 
```

На выходе будут показаны результаты, аналогичные следующим:

![Результаты регистрации запроса информационных событий от агента](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Дальнейшие действия

Azure Monitor для контейнеров не включает заранее определенный набор оповещений. Просмотрите [оповещения о производительности Create с помощью Azure Monitor для контейнеров,](container-insights-alerts.md) чтобы узнать, как создавать рекомендуемые оповещения для высокого процессора и использования памяти для поддержки DevOps или операционных процессов и процедур. 
