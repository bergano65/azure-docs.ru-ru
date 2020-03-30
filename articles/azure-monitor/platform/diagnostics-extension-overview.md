---
title: Обзор расширения Azure Diagnostics
description: Диагностику Azure можно использовать для отладки, оценки производительности, мониторинга, а также анализа трафика в облачных службах, на виртуальных машинах и в Service Fabric.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672384"
---
# <a name="azure-diagnostics-extension-overview"></a>Обзор расширения Azure Diagnostics
Расширение Azure Diagnostics является [агентом Azure Monitor,](agents-overview.md) который собирает данные мониторинга с гостевой операционной системы ресурсов Azure, включая виртуальные машины. В этой статье представлен обзор расширения Azure Diagnostics, включая определенную функциональность, которую он поддерживает, и параметры для установки и конфигурации. 

> [!NOTE]
> Расширение Azure Diagnostics является одним из агентов, доступных для сбора данных мониторинга из гостевой операционной системы вычислительных ресурсов. [См. Обзор агентов Azure Monitor](agents-overview.md) для описания различных агентов и рекомендаций по выбору соответствующих агентов для ваших требований.

## <a name="comparison-to-log-analytics-agent"></a>Сравнение с агентом Log Analytics
Агент Log Analytics в Azure Monitor также может быть использован для сбора данных мониторинга с гостевой операционной системы виртуальных машин. Вы можете использовать один или оба в зависимости от ваших требований. Для детального сравнения агентов Azure Monitor смотрите [обзор агентов Azure Monitor.](agents-overview.md) 

Основные различия, которые следует учитывать:

- Расширение Azure Diagnostics может использоваться только с помощью виртуальных машин Azure. Агент Log Analytics можно использовать с виртуальными машинами в Azure, других облаках и на территории.
- Расширение Azure Diagnostics отправляет данные в Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (только Windows) и концентраторы событий. Агент Log Analytics собирает данные в [журналы мониторинга Azure.](data-platform-logs.md)
- Агент Log Analytics необходим для [решений,](../monitor-reference.md#insights-and-core-solutions) [Azure Monitor для вс-ми- и](../insights/vminsights-overview.md)других служб, таких как [Центр безопасности Azure.](/azure/security-center/)

## <a name="costs"></a>Затраты
Диагностическое расширение Azure не взимается, но за данные могут взиматься расходы. Проверьте [цены Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) в пункте назначения, где вы собираете данные.

## <a name="data-collected"></a>Собираемые данные
В следующих таблицах перечисляется данные, которые могут быть собраны расширением диагностики Windows и Linux.

### <a name="windows-diagnostics-extension-wad"></a>Расширение диагностики Windows (WAD)

| Источник данных | Описание |
| --- | --- |
| Журналы событий Windows   | События из журнала событий Windows. |
| Счетчики производительности | Численные значения, измеряющие производительность различных аспектов операционной системы и рабочих нагрузок. |
| Журналы IIS             | Информация об использовании для веб-сайтов IIS, работающих на гостевой операционной системе. |
| Журналы приложений     | Отслеживайте сообщения, написанные вашим приложением. |
| Журналы .NET EventSource |События записи кода с использованием класса .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Журналы трассировки событий Windows на основе манифеста](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Отслеживание событий для событий Windows, генерируемых любым процессом. |
| Аварийные дампы (журналы)   | Информация о состоянии процесса при сбое приложения. |
| Файл на основе журналов    | Журналы, созданные вашим приложением или службой. |
| Агент диагностические журналы | Информация о самой Azure Diagnostics. |


### <a name="linux-diagnostics-extension-lad"></a>Расширение диагностики Linux (LAD)

| Источник данных | Описание |
| --- | --- |
| Системный журнал | События, отправленные в систему регистрации событий Linux.   |
| Счетчики производительности  | Численные значения, измеряющие производительность различных аспектов операционной системы и рабочих нагрузок. |
| Файлы журналов | Записи, отправленные в журнал на основе файлов.  |

## <a name="data-destinations"></a>Целевое расположение данных
Диагностическое расширение Azure для Windows и Linux всегда собирает данные в учетную запись хранения Azure. Смотрите [установить и настроить расширение диагностики Windows Azure (WAD)](diagnostics-extension-windows-install.md) и [использовать Linux Диагностическое расширение для мониторинга метрик и журналов](../../virtual-machines/extensions/diagnostics-linux.md) для списка конкретных таблиц и капли, где эти данные собираются.

Налажить один или несколько *топов данных* для отправки данных в другие дополнительные пункты назначения. В следующих разделах перечисляются приемники, доступные для расширения диагностики Windows и Linux.

### <a name="windows-diagnostics-extension-wad"></a>Расширение диагностики Windows (WAD)

| Назначение | Описание |
|:---|:---|
| Метрика azure Monitor | Сбор данных о производительности в Azure Monitor Metrics. Смотрите [присылайте метрики гостевой ОС в метрическую базу данных Azure Monitor.](collect-custom-metrics-guestos-resource-manager-vm.md)  |
| Центры событий | Используйте концентраторы событий Azure для отправки данных за пределами Azure. Просматривайте [данные потоковой передачи Azure Diagnostics в концентрах событий](diagnostics-extension-stream-event-hubs.md) |
| Большие двоичные объекты службы хранилища Azure | Помимо таблиц, напишите данные на капли в Azure Storage. |
| Application Insights | Сбор данных из приложений, работающих в вашем VM в Application Insights, для интеграции с другим мониторингом приложений. Смотрите [Отправить диагностические данные в Application Insights](diagnostics-extension-to-application-insights.md). |

Вы также можете собирать данные WAD из хранилища в рабочее пространство Log Analytics, чтобы проанализировать их с помощью журналов Azure Monitor, хотя для этой функциональности обычно используется агент Log Analytics. Он может отправлять данные непосредственно в рабочее пространство Log Analytics и поддерживает решения и идеи, которые обеспечивают дополнительную функциональность.  Смотрите [диагностические журналы «Сбор azure» из хранилища Azure.](diagnostics-extension-logs.md) 


### <a name="linux-diagnostics-extension-lad"></a>Расширение диагностики Linux (LAD)
LAD записывает данные в таблицы в Хранилище Azure. Он поддерживает раковины в следующем таблице.

| Назначение | Описание |
|:---|:---|
| Центры событий | Используйте концентраторы событий Azure для отправки данных за пределами Azure. |
| Большие двоичные объекты службы хранилища Azure | Помимо таблиц, напишите данные на капли в Azure Storage. |
| Метрика azure Monitor | Установите агент Телеграф в дополнение к LAD. Смотрите [Коллекционные пользовательские метрики для Linux VM с агентом InfluxData Telegraf.](collect-custom-metrics-linux-telegraf.md)


## <a name="installation-and-configuration"></a>Установка и настройка
Диагностическое расширение реализовано как [виртуальное расширение машины](../../virtual-machines/extensions/overview.md) в Azure, поэтому оно поддерживает те же параметры установки, используя шаблоны Resource Manager, PowerShell и CLI. Смотрите [виртуальные расширения машины и функции для Windows](../../virtual-machines/extensions/features-windows.md) и [виртуальных машин расширений и функций для Linux](../../virtual-machines/extensions/features-linux.md) для общих деталей по установке и поддержанию виртуальных расширений машины.

Вы также можете установить и настроить диагностическое расширение Windows и Linux на портале Azure под **настройками диагностики** в разделе **Мониторинг** меню виртуальной машины.

Ниже приведены подробные сведения об установке и настройке расширения диагностики для Windows и Linux.

- [Установка и настройка расширения диагностики Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Отслеживание метрик и журналов с помощью диагностического расширения Linux](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Прочая документация

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Облачные службы Azure (классические) веб-роли и рабочие
- [Введение в мониторинг облачных служб](../../cloud-services/cloud-services-how-to-monitor.md)
- [Включение системы диагностики Azure в облачных службах Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Анализ приложений для облачных служб Azure](../app/cloudservices.md)<br>[Трассировка потока в приложении облачных служб с помощью системы диагностики Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Мониторинг и диагностика состояния служб в локальной среде разработки](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Дальнейшие действия


* Узнайте, как [использовать счетчики производительности в Диагностике Azure](../../cloud-services/diagnostics-performance-counters.md).
* Если возникнут проблемы с запуском диагностики или поиском данных в таблицах хранилища Azure, см. статью [Устранение неполадок с помощью системы диагностики Azure](diagnostics-extension-troubleshooting.md).

