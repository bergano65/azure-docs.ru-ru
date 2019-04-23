---
title: Как запрашивать данные журналов из Azure Monitor для контейнеров | Документация Майкрософт
description: Azure Monitor для контейнеров собирает метрики и данные журналов, и эта статья описывает записи и содержит примеры запросов.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014408"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Как запрашивать данные журналов из Azure Monitor для контейнеров
Azure Monitor для контейнеров собирает метрики производительности, данные инвентаризации и сведения о состоянии работоспособности из узлов контейнеров и контейнеров и перенаправляет его в рабочую область Log Analytics в Azure Monitor. Эти данные собираются каждые три минуты. Эти данные окажутся доступными для [запроса](../../azure-monitor/log-query/log-query-overview.md) в Azure Monitor. Эти данные используются в различных сценариях, таких как планирование миграции, анализ емкости, обнаружение и устранение проблем с производительностью по требованию.

## <a name="container-records"></a>Записи контейнеров

В таблице ниже приведены примеры записей, собранных Azure Monitor для контейнеров, и типов данных, которые отображаются в результатах поиска по журналам.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Журнал контейнеров | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Список узлов контейнеров | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Список модулей pod в кластере Kubernetes | `KubePodInventory` | TimeGenerated, компьютер, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Имя_контроллера, Состояние_контейнера,  ContainerStatusReason, ContainerID, ContainerName, имя, PodLabel, пространство имен, PodStatus, имя кластера, IP-адресом модуля, SourceSystem |
| Список узлов кластера Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| События Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Службы в кластере Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Метрики производительности для узлов кластера Kubernetes | Perf &#124; where ObjectName == "K8SNode" | Компьютер, ObjectName, CounterName &#40;cpuAllocatableBytes memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Метрики производительности контейнеров кластера Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath и SourceSystem | 
| InsightsMetrics | Компьютер, имя, пространство имен, тип источника, SourceSystem, теги, TimeGenerated, значение |

## <a name="search-logs-to-analyze-data"></a>Поиск по журналам для анализа данных
Журналы Azure Monitor может помочь найти тренды, диагностировать узкие места, прогноза или сопоставлять данные, которые помогут вам определить ли в текущей конфигурации кластера оптимально. Вам доступны предопределенные запросы поиска по журналам, которые можно использовать без изменений или настроить для получения сведений нужным вам образом. 

Можно выполнить интерактивный анализ данных в рабочей области, выбрав параметр **просмотра журналов событий Kubernetes** или **просмотра журналов контейнера**, доступный в области предварительного просмотра. Страница **поиска по журналам** появится в правой области страницы портала Azure, которую вы использовали.

![Анализ данных в Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Выходные данные журналов контейнера, будут перенаправлены в рабочей области, STDOUT и STDERR. Так как Azure Monitor отслеживает среду Kubernetes (AKS) под управлением Azure, сейчас сведения о системе Kube не собираются ввиду большого объема создаваемых данных. 

### <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам
При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии со своими требованиями. Можно поэкспериментировать с приведенными ниже примерами запросов, чтобы научиться создавать более сложные запросы.

| Запрос | ОПИСАНИЕ | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Вывод всех сведений о жизненном цикле контейнера| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | События Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Инвентаризация образа | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Ресурсы ЦП контейнера. | 
| **Выберите вариант отображения графика**.<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Память контейнера |

## <a name="next-steps"></a>Дальнейшие действия
Azure Monitor для контейнеров не поддерживает набор стандартных предупреждений. Просмотрите [Создание предупреждения о производительности с помощью Azure Monitor для контейнеров](container-insights-alerts.md) вы научитесь создавать рекомендуемые оповещения для высокой загрузки ЦП и памяти для поддержки DevOps или рабочие процессы и процедуры. 