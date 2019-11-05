---
title: Запрос журналов из Azure Monitor для контейнеров | Документация Майкрософт
description: Azure Monitor для контейнеров собирает метрики и данные журнала. в этой статье описываются записи и примеры запросов.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 787e9e6d0ae86568e1af74b4d67fb716841a02df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477098"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Запрос журналов из Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает метрики производительности, данные инвентаризации и сведения о состоянии работоспособности из узлов контейнеров и контейнеров и пересылает их в рабочую область Log Analytics в Azure Monitor. Эти данные собираются каждые три минуты. Эти данные доступны для [запроса](../../azure-monitor/log-query/log-query-overview.md) в Azure Monitor. Эти данные используются в различных сценариях, таких как планирование миграции, анализ емкости, обнаружение и устранение проблем с производительностью по требованию.

## <a name="container-records"></a>Записи контейнеров

В таблице ниже приведены примеры записей, собранных Azure Monitor для контейнеров, и типов данных, которые отображаются в результатах поиска по журналам.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Журнал контейнеров | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Список узлов контейнеров | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Список модулей pod в кластере Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, Контаинеркреатионтиместамп, Подуид, Подкреатионтиместамп, Контаинеррестарткаунт, Подрестарткаунт, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  Контаинерстатусреасон, ContainerID, ContainerName, Name, Подлабел, Namespace, Подстатус, имя_кластера, модуля, Sourcesystem имеет значение |
| Список узлов кластера Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| События Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Службы в кластере Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Метрики производительности для узлов кластера Kubernetes | Perf &#124; where ObjectName == "K8SNode" | Computer, ObjectName, CounterName &#40;Кпуаллокатаблебитес, Меморяллокатаблебитес, КпукапаЦитинанокорес, МеморикапаЦитибитес, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, Каунтерпас, Sourcesystem имеет значение | 
| Метрики производительности контейнеров кластера Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; Кпурекуестнанокорес, Меморирекуестбитес, Кпулимитнанокорес, Мемориворкингсетбитес, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, timegenerated, CounterPath, sourcesystem имеет значение | 
| Пользовательские метрики |`InsightsMetrics` | Компьютер, имя, пространство имен, источник, Sourcesystem имеет значение, теги<sup>1</sup>, timegenerated, тип, ва, _ResourceId | 

<sup>1</sup> свойство *Tags* представляет [несколько измерений](../platform/data-platform-metrics.md#multi-dimensional-metrics) для соответствующей метрики. Дополнительные сведения о метриках, собираемых и хранимых в `InsightsMetrics` таблице, а также описание свойств записи см. в разделе [инсигхтсметрикс Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>В настоящее время поддержка Prometheus является функцией в общедоступной предварительной версии.
>

## <a name="search-logs-to-analyze-data"></a>Поиск по журналам для анализа данных

Журналы Azure Monitor могут помочь в поиске тенденций, диагностике узких мест, прогнозировании или корреляции данных, которые помогут определить, оптимально ли выполняется текущая конфигурация кластера. Вам доступны предопределенные запросы поиска по журналам, которые можно использовать без изменений или настроить для получения сведений нужным вам образом.

Интерактивный анализ данных в рабочей области можно выполнить, выбрав пункт **Просмотр журналов событий Kubernetes** или **Просмотр журналов контейнеров** в области просмотра в раскрывающемся списке **Просмотр в аналитике** . Страница **поиска по журналам** появится в правой области страницы портала Azure, которую вы использовали.

![Анализ данных в Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Контейнеры выводят журналы, перенаправляемые в рабочую область — STDOUT и STDERR. Так как Azure Monitor отслеживает среду Kubernetes (AKS) под управлением Azure, сейчас сведения о системе Kube не собираются ввиду большого объема создаваемых данных. 

### <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам

При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии со своими требованиями. Можно поэкспериментировать с приведенными ниже примерами запросов, чтобы научиться создавать более сложные запросы.

| Запрос | Description (Описание) | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Вывод всех сведений о жизненном цикле контейнера| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | События Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Инвентаризация образа | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Ресурсы ЦП контейнера. | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Память контейнера |
| инсигхтсметрикс<br> &#124;где name = = "requests_count"<br> &#124;суммировать Val = Any (Val) по TimeGenerated = bin (TimeGenerated, 1м)<br> &#124;Сортировать по TimeGenerated ASC<br> &#124;Project Рекуестсперминуте = Val-prev (Val), TimeGenerated <br> &#124;барчарт отрисовки  | Количество запросов в минуту с пользовательскими метриками |

## <a name="query-prometheus-metrics-data"></a>Запрос данных метрик Prometheus

В следующем примере показан запрос метрик Prometheus, отображающий операции чтения с диска в секунду на диск для каждого узла.

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

Чтобы просмотреть метрики Prometheus, которые Azure Monitor отфильтрованы по пространству имен, укажите "Prometheus". Ниже приведен пример запроса для просмотра метрик Prometheus из пространства имен `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Данные Prometheus также можно запрашивать напрямую по имени.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Настройка запроса или ошибки брака

Чтобы исследовать ошибки конфигурации или потери данных, следующий пример запроса возвращает информационные события из таблицы `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

В выходных данных отобразятся результаты, аналогичные приведенным ниже.

![Регистрация результатов запроса информационных событий от агента](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Дальнейшие действия

Azure Monitor для контейнеров не включает предопределенный набор предупреждений. Ознакомьтесь с разработкой [оповещений о производительности с помощью Azure Monitor для контейнеров](container-insights-alerts.md) , чтобы узнать, как создавать Рекомендуемые оповещения для высокой загрузки ЦП и памяти для поддержки DevOps или рабочих процессов и процедур. 
