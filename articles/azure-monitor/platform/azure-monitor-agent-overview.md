---
title: Обзор агента Azure Monitor
description: Обзор агента Azure Monitor (АМА), который собирает данные мониторинга из гостевой операционной системы виртуальных машин.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ff70beef89f6db240db244de1e11e54193858be0
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705781"
---
# <a name="azure-monitor-agent-overview-preview"></a>Общие сведения об агенте Azure Monitor (Предварительная версия)
Агент Azure Monitor (АМА) собирает данные мониторинга из гостевой операционной системы виртуальных машин и доставляет их в Azure Monitor. В этой статье содержатся общие сведения об агенте Azure Monitor, включая инструкции по его установке и настройке сбора данных.

## <a name="relationship-to-other-agents"></a>Связь с другими агентами
Агент Azure Monitor заменяет следующие агенты, которые в настоящее время используются Azure Monitor для получения гостевых данных из виртуальных машин.

- [Агент log Analytics](log-analytics-agent.md) — отправляет данные в log Analytics рабочую область и поддерживает решения Azure Monitor для виртуальных машин и мониторинга.
- [Расширение диагностики](diagnostics-extension-overview.md) — отправляет данные в метрики Azure Monitor (только для Windows), концентраторы событий Azure и хранилище Azure.
- [Агент Telegraf](collect-custom-metrics-linux-telegraf.md) — отправляет данные в метрики Azure Monitor (только для Linux).

Помимо консолидации этой функции в одном агенте Azure Monitor агент предоставляет следующие преимущества по сравнению с существующими агентами:

- Область мониторинга. Централизованная настройка коллекции для различных наборов данных из разных наборов виртуальных машин.  
- Несколько сайтов Linux: отправка данных с виртуальных машин Linux в несколько рабочих областей.
- Фильтрация событий Windows: Используйте запросы XPATH для фильтрации собираемых событий Windows.
- Улучшенное управление расширениями: Azure Monitor агент использует новый метод обработки расширяемости, который более прозрачен и управляемый, чем пакеты управления и подключаемые модули Linux в текущих агентах Log Analytics.

### <a name="changes-in-data-collection"></a>Изменения в сборе данных
Методы определения сбора данных для существующих агентов отличаются друг от друга, и каждый из них имеет проблемы, решаемые с помощью агента Azure Monitor.

- Log Analytics агент получает свою конфигурацию из рабочей области Log Analytics. Это легко настроить, но сложно определить независимые определения для разных виртуальных машин. Он может передавать данные только в рабочую область Log Analytics.

- Диагностическое расширение имеет конфигурацию для каждой виртуальной машины. Это позволяет легко определить независимые определения для разных виртуальных машин, но сложно управлять ими. Он может передавать данные только Azure Monitorным метрикам, концентраторам событий Azure или службе хранилища Azure. Для агентов Linux агент Telegraf с открытым исходным кодом необходим для отправки данных в метрики Azure Monitor.

Агент Azure Monitor использует [правила сбора данных (ДКР)](data-collection-rule-overview.md) для настройки сбора данных из каждого агента. Правила сбора данных позволяют управлять параметрами коллекции в масштабе, сохраняя при этом уникальные конфигурации с заданной областью для подмножеств компьютеров. Они не зависят от рабочей области и не зависят от виртуальной машины, что позволяет определять их один раз и многократно использовать на компьютерах и в разных средах. См. раздел [Настройка сбора данных для агента Azure Monitor (Предварительная версия)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Текущие ограничения
При общедоступной предварительной версии агента Azure Monitor применяются следующие ограничения.

- Агент Azure Monitor не поддерживает решения и аналитические сведения, такие как Azure Monitor для виртуальных машин и центр безопасности Azure. В настоящее время единственным поддерживаемым сценарием является сбор данных с помощью настраиваемых правил сбора данных. 
- Правила сбора данных должны создаваться в том же регионе, что и любая Log Analytics Рабочая область, используемая в качестве назначения.
- В настоящее время поддерживаются только виртуальные машины Azure. Локальные виртуальные машины, масштабируемые наборы виртуальных машин, ARC для серверов, служба Azure Kubernetes и другие типы ресурсов вычислений в настоящее время не поддерживаются.
- Виртуальная машина должна иметь доступ к следующим конечным точкам HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Сосуществование с другими агентами
Агент Azure Monitor может сосуществовать с существующими агентами, чтобы можно было продолжать использовать существующие функции во время оценки или миграции. Это особенно важно в связи с ограничениями в общедоступной предварительной версии для поддержки существующих решений. Следует соблюдать осторожность при сборе дублирующихся данных, так как это может привести к отклонению результатов запросов и повлечь за собой дополнительную плату за прием и хранение данных.

Например, Azure Monitor для виртуальных машин использует агент Log Analytics для отправки данных о производительности в рабочую область Log Analytics. Кроме того, вы можете настроить рабочую область для получения событий Windows и событий системного журнала от агентов. Если установить агент Azure Monitor и создать правило сбора данных для этих же событий и данных о производительности, это приведет к дублированию данных.


## <a name="costs"></a>Затраты
Плата за Azure Monitor агент не взимается, но вы можете взимать плату за полученные данные. Дополнительные сведения о сборе и хранении данных и метриках клиентов см. Log Analytics в разделе [Azure Monitor цены](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Источники данных и назначения
В следующей таблице перечислены типы данных, которые в настоящее время можно собирать с помощью агента Azure Monitor, используя правила сбора данных, и где можно отправить эти данные. Просмотрите, [что отслеживается Azure Monitor?](../monitor-reference.md) для получения списка аналитических сведений, решений и других решений, использующих агент Azure Monitor для получения данных других типов.


Агент Azure Monitor отправляет данные в метрики Azure Monitor или в Log Analytics рабочую область, поддерживающую журналы Azure Monitor.

| Источник данных | Назначения | Описание |
|:---|:---|:---|
| Производительность        | Метрики Azure Monitor<br>Рабочая область Log Analytics | Числовые значения, представляющие собой оценки производительности разных элементов операционной системы и рабочих нагрузок. |
| Журналы событий Windows | Рабочая область Log Analytics | Информация, отправляемая системой ведения журналов событий Windows. |
| Системный журнал             | Рабочая область Log Analytics | Информация, отправляемая системой ведения журналов событий Linux. |


## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
В настоящее время агентом Azure Monitor поддерживаются следующие операционные системы.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14,04 LTS, 16,04 LTS, 18,04 LTS

> [!IMPORTANT]
> <sup>1</sup> Чтобы эти дистрибутивы отправляли данные системного журнала, необходимо удалить rsyslog и установить syslog-ng.


## <a name="security"></a>Безопасность
Агенту Azure Monitor не требуются ключи, но вместо этого требуется [управляемое системой удостоверение](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Перед развертыванием агента необходимо включить управляемое удостоверение, назначаемое системой на каждой виртуальной машине.


## <a name="install-the-azure-monitor-agent"></a>Установка агента Azure Monitor
Агент Azure Monitor реализуется как [расширение виртуальной машины Azure](../../virtual-machines/extensions/overview.md) и содержит сведения, приведенные в следующей таблице. 

| Свойство | Windows | Linux |
|:---|:---|:---|
| Издатель | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Тип      | азуремониторвиндовсажент | азуремониторлинуксажент  |
| TypeHandlerVersion  | 1.0 | 1.5 |

Установите агент Azure Monitor с помощью любого из методов установки агентов виртуальной машины, включая следующие с помощью PowerShell или CLI. Кроме того, можно установить агент и настроить сбор данных на виртуальных машинах в подписке Azure с помощью портала, как описано в разделе [Настройка сбора данных для агента Azure Monitor (Предварительная версия)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Дальнейшие действия

- [Создайте правило сбора данных](data-collection-rule-azure-monitor-agent.md) для сбора данных от агента и их отправки в Azure Monitor.
