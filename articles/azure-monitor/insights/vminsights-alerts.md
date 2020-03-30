---
title: Оповещения от Azure Monitor для VMs
description: Описывает, как создавать правила оповещения из данных о производительности, собранных Azure Monitor для VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289606"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Как создать оповещения от Azure Monitor для VMs
[Оповещения в Azure Monitor](../platform/alerts-overview.md) упреждающе уведомляют вас об интересных данных и шаблонах в данных мониторинга. Azure Monitor для виртуальных вывешиней не включает предварительно настроенные правила оповещения, но вы можете создать свои собственные данные, которые он собирает. В этой статье содержатся рекомендации по созданию правил оповещения, включая набор выборочных запросов.


## <a name="alert-rule-types"></a>Типы правил оповещения
Azure Monitor имеет [различные типы правил оповещения,](../platform/alerts-overview.md#what-you-can-alert-on) основанные на данных, используемых для создания оповещения. Все данные, собранные Azure Monitor для ВМ, хранятся в журналах Azure Monitor, [поддерживающих оповещения о журналах.](../platform/alerts-log.md) В настоящее время нельзя использовать [метрические оповещения](../platform/alerts-log.md) с данными о производительности, собранными из Azure Monitor для ВМ, поскольку данные не собираются в azure Monitor Metrics. Для сбора данных для метрических оповещений установите [расширение диагностики](../platform/diagnostics-extension-overview.md) для Windows VMs или [агента Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) для Linux VMs для сбора данных о производительности в метрики.

В Azure Monitor есть два типа оповещений о журналах:

- [Количество оповещений о результатах](../platform/alerts-unified-log.md#number-of-results-alert-rules) создает одно оповещение, когда запрос возвращает по крайней мере определенное количество записей. Они идеально подходят для нецифровых данных, таких как и windows и Syslog событий, собранных [агентом log Analytics](../platform/log-analytics-agent.md) или для анализа тенденций производительности на нескольких компьютерах.
- [Предупреждения о измерениях](../platform/alerts-unified-log.md#metric-measurement-alert-rules) метриксоздают отдельное оповещение для каждой записи в запросе, которое имеет значение, превышающем порог, определенный в правиле оповещения. Эти правила оповещения идеально подходят для данных о производительности, собранных Azure Monitor для вс-бай-инов, поскольку они могут создавать индивидуальные оповещения для каждого компьютера.


## <a name="alert-rule-walkthrough"></a>Пошагза правила оповещения
В этом разделе используется правило предупреждения измерения метрики с использованием данных о производительности из Azure Monitor для VMs. Этот базовый процесс можно использовать с различными запросами журналов для оповещения на различных счетчиках производительности.

Начните с создания нового правила оповещения после процедуры в [Create, view and manage log alerts с помощью Azure Monitor.](../platform/alerts-log.md) Для **ресурса**выберите рабочее пространство для аналитики журналов, которое используют в подписке vMs Azure Monitor. Поскольку целевой ресурс для правил оповещения о журнале всегда является рабочим пространством Log Analytics, запрос журнала должен включать любой фильтр для определенных виртуальных машин или виртуальных наборов масштабов машин. 

Для правила **«Состояние** оповещения» используйте один из запросов в [разделе ниже](#sample-alert-queries) в качестве **поискового запроса.** Запрос должен вернуть числовое свойство под названием *AggregatedValue.* Он должен обобщить данные с помощью компьютера, чтобы можно было создать отдельное оповещение для каждой виртуальной машины, превышающей порог.

В **логике оповещения**выберите **метрическое измерение,** а затем предоставьте значение **Порога.** В **триггерном оповещении на основе**, укажите, сколько раз порог должен быть превышен до создания оповещения. Например, вам, вероятно, все равно, если процессор превышает порог один раз, а затем возвращается к нормальной жизни, но вы заботитесь, если он продолжает превышать порог в течение нескольких последовательных измерений.

**Оценка на основе** раздела определяет частоту выполнения запроса и временное окно для запроса. В приведенном ниже примере запрос будет работать каждые 15 минут и оценивать значения производительности, собранные за предыдущие 15 минут.


![Правило предупреждения измерения измерения](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Примеры запросов оповещения
Следующие запросы могут быть использованы с правилом предупреждения измерения метрики с использованием данных о производительности, собранных Azure Monitor для VMs. Каждый суммирует данные с помощью компьютера, так что оповещение создается для каждого компьютера со значением, превышающее порог.

### <a name="cpu-utilization"></a>загрузка ЦП;

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Доступная память в МБ

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Доступная память в процентах

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Логический используемый диск - все диски на каждом компьютере

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Логический используемый диск - индивидуальные диски

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Логический диск IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Скорость обработки логических дисков

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Сетевые интерфейсы байты получены - все интерфейсы

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Сетевые интерфейсы байты получили - индивидуальные интерфейсы

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Сетевые интерфейсы байты отправлены - все интерфейсы

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Сетевые интерфейсы байты отправлены - индивидуальные интерфейсы

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Набор масштабирования виртуальных машин
Измените с помощью идентификатора подписки, группы ресурсов и названия виртуальной шкалы машин.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Конкретная виртуальная машина
Измените с помощью идентификатора подписки, группы ресурсов и имени VM.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Использование процессора для всех вычислительных ресурсов в подписке
Измените с идентификатором подписки.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Использование процессора для всех вычислительных ресурсов в группе ресурсов
Измените с помощью идентификатора подписки и группы ресурсов.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о [предупреждениях в Azure Monitor](../platform/alerts-overview.md).
- Узнайте больше о [запросах журналов, используя данные Azure Monitor для vMs.](vminsights-log-search.md)
