---
title: Метрики платформы Azure Monitor, которые можно экспортировать с помощью параметров диагностики
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: f0a8fd186862cf95ebdbb2d5bd92d8ff860b3ba1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515484"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Метрики платформы Azure Monitor, которые можно экспортировать с помощью параметров диагностики

Azure Monitor предоставляет [метрики платформы](data-platform-metrics.md) по умолчанию без конфигурации. Она обеспечивает несколько способов взаимодействия с метриками платформы, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Полный список метрик платформы, доступных в настоящее время с помощью конвейера консолидированной метрики Azure Monitor, см. в разделе [метрики — поддерживаемые](metrics-supported.md). Чтобы запросить эти метрики и получить к ним доступ, воспользуйтесь [версией API 2018-01-01](/rest/api/monitor/metricdefinitions). Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий.

Вы можете экспортировать метрики платформы из конвейера Azure Monitor в другие расположения одним из двух способов.
1. Пользуясь [параметрами диагностики](diagnostic-settings.md), отправить метрики в Log Analytics, концентраторы событий или службу хранилища Azure.
2. Использовать [REST API для метрик](/rest/api/monitor/metrics/list)

Из-за особенностей в серверной части Azure Monitor не все метрики могут быть экспортированы с помощью параметров диагностики. В таблице ниже перечислено, что можно и что нельзя экспортировать с помощью параметров диагностики.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Изменение поведения в отношении значений NULL и нулевых значений 
 
Среди метрик платформы, которые можно экспортировать с помощью параметров диагностики, существует несколько метрик, для которых Azure Monitor интерпретирует "0" как NULL. Это привело к путанице между реальным "0" (который выдан ресурсом) и значением NULL, интерпретированным как "0". Вскоре произойдет изменение, и метрики платформы, экспортированные с помощью параметров диагностики, будут экспортировать "0", только если он действительно был выдан базовым ресурсом. 

> [!CAUTION]
> Изменение описанного выше поведения запланировано на 1 июня 2020 г.

Обратите внимание на следующее:

1.  При удалении группы ресурсов или определенного ресурса данные метрик из затронутых ресурсов больше не будут отправляться в места назначения экспорта параметров диагностики. Это значит, что они больше не будут отображаться в концентраторах событий, учетных записях хранения и рабочих областях Log Analytics.
2.  Это улучшение будет доступно во всех общедоступных и частных облаках.
3.  Это изменение не повлияет на: 
   - журналы ресурсов платформы, экспортированные с помощью параметров диагностики;
   - создание диаграмм метрик в обозревателе метрик;
   - предупреждения о метриках платформы.
 
## <a name="metrics-exportable-table"></a>Таблица метрик, доступных для экспорта 

Таблица содержит следующие столбцы. 
- Экспортируется с помощью параметров диагностики? 
- Затронута проблемой NULL/0? 
- ResourceType 
- Метрика 
- MetricDisplayName
- Единицы 
- AggregationType


> [!NOTE]
> Внизу следующей таблицы может быть горизонтальная полоса прокрутки. Если вы не видите каких-то данных, скорее всего, полоса прокрутки находится у левого края таблицы.  


| Экспортируется с помощью параметров диагностики?  | Уже выдает значения NULL |  ResourceType  |  Метрика  |  MetricDisplayName  |  Единицы  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Память: текущая цена очистки  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Память: несжимаемая память очистки  |  Байты  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Память: сжимаемая память очистки  |  Байты  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Потоки: занятые потоки в пуле команд  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Потоки: бездействующие потоки в пуле команд  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Длина очереди заданий пула команд  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Подключение: Текущие подключения  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Текущие пользовательские сеансы  |  Count  |  Среднее | 
| ****Да****  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Потоки: занятые потоки продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Потоки: бездействующие потоки продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Потоки: длина очереди заданий продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Память подсистемы M  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Байты исключительного пользования подсистемы M  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU подсистемы M  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Байты виртуальной памяти подсистемы M  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Пробуксовка памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Память: твердый лимит памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Память: верхний предел памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Память: нижний предел памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Память: ограничение памяти VertiPaq  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Память: Использование памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Потоки: занятые потоки ввода-вывода в пуле обработки заданий  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Потоки: длина очереди заданий ввода-вывода пула обработки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Длина очереди заданий пула обработки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Занятые потоки в пуле запросов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Потоки: бездействующие потоки в пуле запросов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Потоки: длина очереди заданий пула запросов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  Quota  |  Память: Quota  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Память: заблокировано квот  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Обработка: преобразовано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Обработка: считано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Обработка: записано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Потоки: занятые потоки быстрого анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Потоки: бездействующие потоки быстрого анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Потоки: длина очереди заданий быстрого анализа  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Число успешных подключений в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Общее число неудачных подключений  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Общее число запросов на подключение  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Память: размер нестраничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Память: размер страничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Байт виртуальной памяти  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Длительность внутренних запросов  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Duration  |  Общая длительность запросов шлюза  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Удаленные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Отклоненные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Успешные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  События регулировки EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  События истекшего времени ожидания EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Размер событий EventHub  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Всего событий EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  События EventHub с ошибками  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Неудачные запросы к шлюзу (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Прочие запросы к шлюзу (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Успешные запросы к шлюзу (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Всего запросов к шлюзу (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Неавторизованные запросы к шлюзу (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Процент использования ЦП приложением  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  Назначенная память для приложения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  Максимальная память для приложения  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  Используемая память для приложения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  Число приостановок сборки мусора  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  Общее время приостановок сборки мусора  |  Миллисекунды  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Максимальный доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Размер данных для продвижения в старое поколение  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage  |  Процент использования ЦП системой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Глобальная ошибка Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Всего полученных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Максимальное время запроса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Всего запросов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Общее время запросов Tomcat  |  Миллисекунды  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Среднее время запроса Tomcat  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Всего отправленных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Число активных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Максимальное число активных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Максимальное время активных сеансов Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Число созданных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Число истекших сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Число отклоненных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  Размер данных для продвижения в молодое поколение  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Всего заданий  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Общее количество запусков развертывания обновлений для компьютера  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Общее количество запусков развертывания обновлений  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedicated Core Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Creating Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Idle Node Count  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Job Delete Complete Events (События окончания удаления задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Job Delete Start Events (События начала удаления задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Job Disable Complete Events (События окончания отключения задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Job Disable Start Events (События начала отключения задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Job Start Events (События запуска задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Job Terminate Complete Events (События окончания завершения задания)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Job Terminate Start Events (События начала завершения задания)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Leaving Pool Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority Core Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Offline Node Count  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Pool Create Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Pool Delete Complete Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Pool Delete Start Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Pool Resize Complete Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Pool Resize Start Events  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Preempted Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Rebooting Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Reimaging Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Running Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Starting Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Start Task Failed Node Count  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Task Complete Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Task Fail Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Task Start Events  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Low-Priority Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Dedicated Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Unusable Node Count  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Waiting For Start Task Node Count  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Активные ядра  |  Активные ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Активные узлы  |  Активные узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Бездействующие ядра  |  Бездействующие ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Бездействующие узлы  |  Бездействующие узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Выполненные задания  |  Выполненные задания  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Отправленные задания  |  Отправленные задания  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Освобождаемые ядра  |  Освобождаемые ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Освобождаемые узлы  |  Освобождаемые узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Замещенные ядра  |  Замещенные ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Замещенные узлы  |  Замещенные узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Процент использования квоты  |  Процент использования квоты  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Всего ядер  |  Всего ядер  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Всего узлов  |  Всего узлов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Недоступные для использования ядра  |  Недоступные для использования ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.BatchAI/workspaces  |  Недоступные для использования узлы  |  Недоступные для использования узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Принятые подключения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Обработанные подключения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Процент использования ЦП  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  Считанные байты ввода-вывода  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  Записанные байты ввода-вывода  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Предельный объем памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Отложенные транзакции  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Обработанные блоки  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Обработанные транзакции  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Транзакции в очереди  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Обработанные запросы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Использование хранилища  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits  |  Попаданий в кэш  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheLatency  |  Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses  |  Промахов в кэше  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead  |  Чтение из кэша  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead0  |  Cache Read (Shard 0)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead1  |  Cache Read (Shard 1)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead2  |  Cache Read (Shard 2)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead3  |  Cache Read (Shard 3)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead4  |  Cache Read (Shard 4)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead5  |  Cache Read (Shard 5)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead6  |  Cache Read (Shard 6)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead7  |  Cache Read (Shard 7)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead8  |  Cache Read (Shard 8)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead9  |  Cache Read (Shard 9)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite  |  Запись в кэш  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache Write (Shard 0)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache Write (Shard 1)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache Write (Shard 2)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache Write (Shard 3)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache Write (Shard 4)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache Write (Shard 5)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache Write (Shard 6)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache Write (Shard 7)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache Write (Shard 8)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache Write (Shard 9)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients  |  Подключенные клиенты  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients0  |  Connected Clients (Shard 0)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients1  |  Connected Clients (Shard 1)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients2  |  Connected Clients (Shard 2)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients3  |  Connected Clients (Shard 3)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients4  |  Connected Clients (Shard 4)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients5  |  Connected Clients (Shard 5)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients6  |  Connected Clients (Shard 6)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients7  |  Connected Clients (Shard 7)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients8  |  Connected Clients (Shard 8)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients9  |  Connected Clients (Shard 9)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  ошибки  |  ошибки  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys  |  Исключенные ключи  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys0  |  Evicted Keys (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys1  |  Evicted Keys (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys2  |  Evicted Keys (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys3  |  Evicted Keys (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys4  |  Evicted Keys (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys5  |  Evicted Keys (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys6  |  Evicted Keys (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys7  |  Evicted Keys (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys8  |  Evicted Keys (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys9  |  Evicted Keys (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys  |  Ключи с истекшим сроком действия  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys0  |  Expired Keys (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys1  |  Expired Keys (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys2  |  Expired Keys (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys3  |  Expired Keys (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys4  |  Expired Keys (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys5  |  Expired Keys (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys6  |  Expired Keys (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys7  |  Expired Keys (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys8  |  Expired Keys (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys9  |  Expired Keys (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands  |  Операций считывания  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond  |  Количество операций в секунду  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Количество операций в секунду (сегмент 0).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Количество операций в секунду (сегмент 1).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Количество операций в секунду (сегмент 2).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Количество операций в секунду (сегмент 3).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Количество операций в секунду (сегмент 4).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Количество операций в секунду (сегмент 5).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Количество операций в секунду (сегмент 6).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Количество операций в секунду (сегмент 7).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Количество операций в секунду (сегмент 8).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Количество операций в секунду (сегмент 9).  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime  |  ЦП  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad  |  Загрузка сервера  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad0  |  Server Load (Shard 0)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad1  |  Server Load (Shard 1)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad2  |  Server Load (Shard 2)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad3  |  Server Load (Shard 3)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad4  |  Server Load (Shard 4)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad5  |  Server Load (Shard 5)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad6  |  Server Load (Shard 6)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad7  |  Server Load (Shard 7)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad8  |  Server Load (Shard 8)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad9  |  Server Load (Shard 9)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands  |  Наборы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands1  |  Sets (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands2  |  Sets (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands5  |  Sets (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Всего операций  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total Operations (Shard 0)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total Operations (Shard 1)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total Operations (Shard 2)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total Operations (Shard 3)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total Operations (Shard 4)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total Operations (Shard 5)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total Operations (Shard 6)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total Operations (Shard 7)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total Operations (Shard 8)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total Operations (Shard 9)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys  |  Всего ключей  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys0  |  Total Keys (Shard 0)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys1  |  Total Keys (Shard 1)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys2  |  Total Keys (Shard 2)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys3  |  Total Keys (Shard 3)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys4  |  Total Keys (Shard 4)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys5  |  Total Keys (Shard 5)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys6  |  Total Keys (Shard 6)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys7  |  Total Keys (Shard 7)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys8  |  Total Keys (Shard 8)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys9  |  Total Keys (Shard 9)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory  |  Используемая память  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory0  |  Used Memory (Shard 0)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory1  |  Used Memory (Shard 1)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory2  |  Used Memory (Shard 2)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory3  |  Used Memory (Shard 3)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory4  |  Used Memory (Shard 4)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory5  |  Used Memory (Shard 5)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory6  |  Used Memory (Shard 6)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory7  |  Used Memory (Shard 7)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory8  |  Used Memory (Shard 8)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory9  |  Used Memory (Shard 9)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Процент используемой памяти  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS используемой памяти  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Used Memory RSS (Shard 0)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Used Memory RSS (Shard 1)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Used Memory RSS (Shard 2)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Used Memory RSS (Shard 3)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Used Memory RSS (Shard 4)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Used Memory RSS (Shard 5)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Used Memory RSS (Shard 6)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Used Memory RSS (Shard 7)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Used Memory RSS (Shard 8)  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Used Memory RSS (Shard 9)  |  Байты  |  Максимальная | 
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Transactions  |  Transactions  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Размер квоты файлового ресурса  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Число моментальных снимков файлового ресурса  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Размер моментального снимка файлового ресурса  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Blocked Calls  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Обученные символы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Преобразованные символы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Client Errors  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  Задержка  |  Задержка  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server Errors  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Длительность речи  |  Секунды  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Successful Calls  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Total Calls  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Total Errors  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Всего вызовов токенов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Всего транзакций  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска данных  |  Длина очереди диска данных (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска данных (операций/с)  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск данных (операций/с)  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Сеть (входящий трафик)  |  Оплачиваемый входящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Суммарный входящий трафик  |  Суммарный входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Сеть (исходящий трафик)  |  Оплачиваемый исходящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Суммарный исходящий трафик  |  Суммарный исходящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска ОС  |  Длина очереди диска ОС (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди на диск ОС  |  Длина очереди диска ОС (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди в расчете на диск  |  Длина очереди диска данных (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Скорость чтения с диска данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Скорость записи на диск данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Попадание при чтении в кэше для диска данных категории "Премиум"  |  Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Промах чтения в кэше для диска данных категории "Премиум"  |  Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Попадание при чтении в кэше для диска ОС категории "Премиум"  |  Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Промах чтения в кэше для диска ОС категории "Премиум"  |  Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска данных  |  Длина очереди диска данных (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска данных (операций/с)  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск данных (операций/с)  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (входящий трафик)  |  Оплачиваемый входящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Суммарный входящий трафик  |  Суммарный входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (исходящий трафик)  |  Оплачиваемый исходящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Суммарный исходящий трафик  |  Суммарный исходящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска ОС  |  Длина очереди диска ОС (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди на диск ОС  |  Длина очереди диска ОС (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди в расчете на диск  |  Длина очереди диска данных (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (операций/с)  |  Скорость чтения с диска данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (операций/с)  |  Скорость записи на диск данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание при чтении в кэше для диска данных категории "Премиум"  |  Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения в кэше для диска данных категории "Премиум"  |  Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание при чтении в кэше для диска ОС категории "Премиум"  |  Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения в кэше для диска ОС категории "Премиум"  |  Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска данных  |  Длина очереди диска данных (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска данных (операций/с)  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск данных (операций/с)  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (входящий трафик)  |  Оплачиваемый входящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Суммарный входящий трафик  |  Суммарный входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (исходящий трафик)  |  Оплачиваемый исходящий трафик (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Суммарный исходящий трафик  |  Суммарный исходящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска ОС  |  Длина очереди диска ОС (предварительная версия)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди на диск ОС  |  Длина очереди диска ОС (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Скорость чтения с диска ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Скорость записи на диск ОС (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди в расчете на диск  |  Длина очереди диска данных (не рекомендуется)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Скорость чтения с диска данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Скорость записи на диск данных (операций/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание при чтении в кэше для диска данных категории "Премиум"  |  Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения в кэше для диска данных категории "Премиум"  |  Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание при чтении в кэше для диска ОС категории "Премиум"  |  Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения в кэше для диска ОС категории "Премиум"  |  Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Загрузка ЦП  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Получено байтов по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Передано байт по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Длительность выполнения  |  Миллисекунды  |  Итог | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  SuccessfulPullCount  |  Число успешных операций извлечения  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  SuccessfulPushCount  |  Число успешных операций отправки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Всего операций извлечения  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Всего операций отправки  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Общее количество доступных ядер ЦП в управляемом кластере  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Общий объем доступной памяти в управляемом кластере  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Состояния для различных условий узла  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Число модулей pod по фазам  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Число модулей pod в состоянии готовности  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  Доступная емкость  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Отправлено байт в облако (устройство)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Отправлено байт в облако (общий ресурс)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Пропускная способность скачивания из облака  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Пропускная способность скачивания из облака (общий ресурс)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Пропускная способность отправки в облако  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Пропускная способность отправки в облако (общий ресурс)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Пограничные вычисления — использование памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Пограничные вычисления — процент загрузки ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Пропускная способность чтения (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Пропускная способность записи (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Общая емкость  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/datafactories  |  FailedRuns и  |  циклы выполнения со сбоем;  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns.  |  успешные циклы выполнения.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ActivityCancelledRuns  |  Метрики отмененных выполнений действий  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ActivityFailedRuns  |  Метрики неудачных выполнений действий.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ActivitySucceededRuns  |  Метрики успешных выполнений действий.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  FactorySizeInGbUnits  |  Общий размер фабрики (в ГБ)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeAvailableMemory  |  Доступная память для среды выполнения интеграции  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeAverageTaskPickupDelay  |  Длительность очереди среды выполнения интеграции  |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeCpuPercentage  |  Использование ЦП средой выполнения интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeQueueLength  |  Длина очереди среды выполнения интеграции  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  MaxAllowedFactorySizeInGbUnits  |  Максимально допустимый размер фабрики (в GB)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  MaxAllowedResourceCount  |  Максимальное допустимое число сущностей  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  PipelineCancelledRuns  |  Метрики отмененных выполнений конвейеров  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  PipelineFailedRuns  |  Метрики неудачных выполнений конвейеров.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  PipelineSucceededRuns  |  Метрики успешных выполнений конвейеров.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ResourceCount  |  Всего сущностей  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  TriggerCancelledRuns  |  Метрики отмененных запусков триггеров  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  TriggerFailedRuns  |  Метрики неудачных запусков триггеров.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  TriggerSucceededRuns  |  Метрики успешных запусков триггеров.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Cancelled AU Time  |  Секунды  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Failed AU Time  |  Секунды  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Successful AU Time  |  Секунды  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Cancelled Jobs  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Failed Jobs  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Successful Jobs  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Данных прочитано  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Записанные данные  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Запросы на чтение  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Общий объем хранилища  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Запросы на запись  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Максимальная задержка между репликами  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Задержка реплики  |  Секунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  ОПЕРАЦИЙ ВВОДА-ВЫВОДА  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Заполнитель данных телеметрии для узла Digital Twins  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Отброшенные сообщения из облака на устройство (C2D)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Доставленные сообщения из облака на устройство (C2D)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Отклоненные сообщения из облака на устройство (C2D)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Неудачные вызовы прямых методов.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Размер запроса вызовов прямых методов.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Размер ответа вызовов прямых методов.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Успешные вызовы прямых методов.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные из серверной части.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных из серверной части.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Успешные операции чтения с двойников, инициированные из серверной части.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Неудачные обновления двойников, инициированные из серверной части.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Размер обновлений двойников, инициированных из серверной части.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Успешные обновления двойников, инициированные из серверной части.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Сообщения из облака на устройство (C2D) с истекшим сроком действия (предварительная версия)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  конфигурации  |  Configuration Metrics (Метрики конфигурации)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Connected devices (preview) (Подключенных устройств (предварительная версия))  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage  |  Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage.BLOBs  |  Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.latency.Storage  |  Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии)   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии)   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Число предпринятых попыток отправки сообщений телеметрии.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Количество ошибок регулирования  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Число отправленных сообщений телеметрии.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные устройством.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных устройством.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Успешные операции чтения с двойников, инициированные устройством.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Неудачные обновления двойников, инициированные устройством.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Размер обновлений двойников, инициированных устройством.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Успешные обновления двойников, инициированные устройством.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Общее количество используемых сообщений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Общий объем использования данных устройствами  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Connected devices (deprecated) (Подключенные устройства (не рекомендуется))   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Total devices (deprecated) (Всего устройств (не рекомендуется))  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Доставки сетки событий (предварительная версия)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Задержка сетки событий (предварительная версия)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Неудачные отмены заданий.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Успешные отмены заданий.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Завершенные задания  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Неудачные операции создания заданий вызова методов.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Успешные операции создания заданий вызова методов.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Неудачные операции создания заданий обновления двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Успешные операции создания заданий обновления двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Неудачные задания.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Неудачные вызовы получения списка заданий.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Успешные вызовы получения списка заданий.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Неудачные запросы заданий.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Успешные запросы заданий.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total devices (preview) (Всего устройств (предварительная версия))  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Неудачные запросы двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Размер результатов запросов двойников.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Успешные запросы двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Попытки аттестации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Назначенные устройства  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Попытки регистрации  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Доступная служба хранилища  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Отключение связи Cassandra  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Расходы запросов по Cassandra  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Запросы по Cassandra  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Использование данных  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Число документов  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Квота на документы  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Использование индексов  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Запросы метаданных  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Запросить оплату Mongo  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Запросы Mongo  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Частота запросов Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Частота запросов Mongo на удаление  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Частота запросов Mongo на вставку  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Частота запросов Mongo на чтение  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Частота запросов Mongo на обновление  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Подготовленная пропускная способность  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Задержка репликации P99  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Доступность службы  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Общее количество запросов  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Общее количество единиц запросов  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EnterpriseKnowledgeGraph/services  |  FailureCount  |  Количество сбоев  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessCount  |  Количество успешных событий  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessLatency  |  Задержка успешных событий  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransactionCount  |  Число транзакций  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/domains  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/domains  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  DroppedEventCount  |  Удаленные события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  MatchedEventCount  |  Соответствующие события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  PublishFailCount  |  События с ошибками публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/domains  |  PublishSuccessLatencyInMs  |  Задержка успешной публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Удаленные события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Соответствующие события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  События с ошибками публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Задержка успешной публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  События с ошибками публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishSuccessLatencyInMs  |  Задержка успешной публикации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Доступная память  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Невыполненная работа записи.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Записанные сообщения.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ЦП  |  ЦП  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Невыполненная работа записи.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Записанные сообщения.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHABL  |  Архивные сообщения невыполненной работы (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Пропускная способность архивных сообщений (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Архивные сообщения (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Входящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Входящие байты (устаревшие) (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Входящие сообщения (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Исходящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Исходящие байты (устаревшие) (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Исходящие сообщения (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Неудачные запросы (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Входящие сообщения (устаревшие) (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INREQS  |  Входящие запросы (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INTERR  |  Внутренние ошибки сервера (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Прочие ошибки (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Исходящие сообщения (устаревшие) (не рекомендуется)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  Размер  |  Размер  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Успешные запросы (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Ошибки из-за занятости сервера (не рекомендуется)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Запросы к шлюзу по категориям  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Запросы к шлюзу  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Число активных рабочих ролей  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  Запросы на масштабирование  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Пороговое значение метрики  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Наблюдаемая емкость  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Наблюдаемое значение метрики  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Инициированные действия масштабирования  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  availabilityResults/availabilityPercentage  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Тесты доступности  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Длительность теста доступности  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Время подключения к сети при загрузке страницы  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Время обработки клиента  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Время получения ответа  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Время отправки запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Время загрузки страницы в браузере  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/count  |  Вызовы зависимостей  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  dependencies/duration  |  Длительность зависимости  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/failed  |  Сбои при вызове зависимости  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/browser  |  Исключения браузера  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  exceptions/count  |  Исключения  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/server  |  Исключения сервера  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  pageViews/count  |  Просмотры страниц  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  pageViews/duration  |  Время загрузки страницы для просмотра  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Частота исключений  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Объем доступной памяти  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Обработка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Скорость ввода-вывода процесса  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Процессорное время  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Количество байтов исключительного использования процессов  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Время выполнения HTTP-запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP requests in application queue (HTTP-запросы в очереди приложений)  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Скорость HTTP-запроса  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/count  |  Запросы сервера  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  requests/duration  |  Время ответа от сервера  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/failed  |  Failed requests (Неудачные запросы)  |  Count  |  Count | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/rate  |  Частота запросов к серверу  |  Число/с  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  traces/count  |  Трассировки  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Всего попаданий в API службы  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Общая задержка API службы  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Всего результатов для API службы  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  Использование кэша  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Максимальная просрочка непрерывного экспорта в минутах  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Непрерывный экспорт — число экспортированных записей  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  Число ожидающих непрерывного экспорта  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  Результат непрерывного экспорта  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ЦП  |  ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Обработанные события (для концентраторов событий и Центров Интернета вещей)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  Использование экспорта  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Задержка приема (в секундах)  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Результат приема  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  Использование приема  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Объем приема (в МБ)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Проверка активности  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Query duration (Длительность запросов)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Частота запросов потокового приема  |  Count  |  RateRequestsPerSecond | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Скорость данных потокового приема  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Длительность потокового приема  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Результат потокового приема  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Actions Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Actions Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Actions Skipped   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Actions Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Actions Succeeded   |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Action Throttled Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Использование памяти соединителя для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Загрузка процессора соединителя для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Использование памяти рабочего процесса для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Загрузка процессора рабочего процесса для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Отмененные запуски  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Runs Completed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Runs Failed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Runs Started  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Runs Succeeded  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  События регулировки начала запуска  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Run Throttled Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Triggers Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Triggers Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Triggers Fired   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Triggers Skipped  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Triggers Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Triggers Succeeded   |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Actions Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Actions Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Actions Skipped   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Actions Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Actions Succeeded   |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Action Throttled Events  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Billable Action Executions  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Billable Trigger Executions  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Отмененные запуски  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Runs Completed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsFailed  |  Runs Failed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsStarted  |  Runs Started  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Runs Succeeded  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  События регулировки начала запуска  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Run Throttled Events  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Total Billable Executions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Triggers Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Triggers Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFired  |  Triggers Fired   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Triggers Skipped  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Triggers Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Triggers Succeeded   |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Активные ядра  |  Активные ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Активные узлы  |  Активные узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Завершенные запуски  |  Завершенные запуски  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  циклы выполнения со сбоем;  |  циклы выполнения со сбоем;  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие ядра  |  Бездействующие ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие узлы  |  Бездействующие узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Освобождаемые ядра  |  Освобождаемые ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Освобождаемые узлы  |  Освобождаемые узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Неудачные развертывания модели  |  Неудачные развертывания модели  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Начатые развертывания модели  |  Начатые развертывания модели  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Успешные развертывания модели  |  Успешные развертывания модели  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Неудачные регистрации модели  |  Неудачные регистрации модели  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Успешные регистрации модели  |  Успешные регистрации модели  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Замещенные ядра  |  Замещенные ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Замещенные узлы  |  Замещенные узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Процент использования квоты  |  Процент использования квоты  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Начатые запуски  |  Начатые запуски  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Всего ядер  |  Всего ядер  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Всего узлов  |  Всего узлов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Недоступные для использования ядра  |  Недоступные для использования ядра  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Недоступные для использования узлы  |  Недоступные для использования узлы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Maps/accounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Maps/accounts  |  Использование  |  Использование  |  Count  |  Count | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  AssetCount  |  Число ресурсов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  AssetQuota  |  Квота ресурсов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Процент используемой квоты ресурсов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Число политик ключей содержимого  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Квота политик ключей содержимого  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Процент использования квоты политик ключей содержимого  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Число политик потоковой передачи  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Квота политик потоковой передачи  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Процент использования квоты политик потоковой передачи  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Сквозная задержка успешного запроса  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Число приостановок сборки мусора  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Общее время приостановок сборки мусора  |  Миллисекунды  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Максимальный доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Размер данных для продвижения в старое поколение  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Процент использования ЦП службой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Назначенная память для службы  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Максимальная память для службы  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Используемая память для службы  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Процент использования ЦП системой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Глобальная ошибка Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Всего полученных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Максимальное время запроса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Всего запросов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Общее время запросов Tomcat  |  Миллисекунды  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Среднее время запроса Tomcat  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Всего отправленных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Число активных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Максимальное число активных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Максимальное время активных сеансов Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Число созданных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Число истекших сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Число отклоненных сеансов Tomcat  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Размер данных для продвижения в молодое поколение  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Volume pool allocated used (Используемый размер выделенного пула для тома)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Volume pool total logical size (Общий логический размер пула для тома)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Средняя задержка чтения  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Средняя задержка записи  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  Read iops (Число операций ввода-вывода в секунду при чтении)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Volume logical size (Логический размер тома)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Volume snapshot size (Размер моментальных снимков в томе)  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  Write iops (Количество операций ввода-вывода в секунду при записи)  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Общее время шлюза приложений  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Количество запросов в минуту на исправный узел  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Время соединения с серверной частью  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Время получения первого байта ответа от серверной части  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Время получения последнего байта ответа от серверной части  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Состояние ответа серверной части  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Распространение правил заблокированных запросов к брандмауэру веб-приложения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Число заблокированных запросов к брандмауэру веб-приложения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BytesSent  |  Отправлено байт  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Текущее число единиц емкости  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Время кругового пути (RTT) клиента  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Текущее число единиц вычислений  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Текущие подключения.  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Невыполненные запросы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Количество работоспособных узлов.  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Общее распространение правил брандмауэра веб-приложения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Состояние ответа.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  Пропускная способность  |  Пропускная способность  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Клиентский протокол TLS  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Общее количество запросов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Количество неработоспособных узлов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Число попаданий в правила приложения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  Обработанные данные  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Состояние работоспособности брандмауэра  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Число попаданий в сетевые правила  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Использование портов SNAT  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/dnszones  |  QueryVolume  |  Объем запросов  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Использование емкости, доступной для наборов записей  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/dnszones  |  RecordSetCount  |  Количество наборов записей  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Доступность ARP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  Доступность BGP  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Работоспособность серверного компонента (в процентах)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Число запросов к серверному компоненту  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Backend Request Latency (Задержка запросов к серверным частям)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Оплачиваемый размер ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestCount  |  Число запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestSize  |  Размер запроса  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Размер ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Total Latency (Общая задержка)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  ByteCount  |  Количество байтов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Health Probe Status (Состояние пробы работоспособности)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  PacketCount  |  Количество пакетов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Количество подключений SNAT  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  SYNCount  |  Количество пакетов SYN  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Отправлено байт  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Получено пакетов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Отправлено пакетов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Среднее Время приема-передачи (мс)  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Процент неудачных проверок (предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Доля проб с ошибками, в процентах  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Время кругового пути (мс) (предварительная версия)  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Количество байтов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Удаленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Перенаправленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Входящие пакеты SYN для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Входящие пакеты TCP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Входящие пакеты UDP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Выполняется ли атака DDoS  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Количество пакетов  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Удаленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Перенаправленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Количество пакетов SYN  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Удаленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Перенаправленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Удаленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Удаленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Перенаправленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Удаленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Проверка состояния конечной точки с помощью конечной точки  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Запросы, выполняемые возвращаемой конечной точкой  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway S2S Bandwidth (Пропускная способность шлюза S2S)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S Bandwidth (Пропускная способность шлюза P2S)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S Connection Count (Количество подключений P2S)  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Пропускная способность туннеля  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Исходящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Удаленные входящие пакеты туннеля (несоответствие селектора трафика)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Исходящие пакеты туннеля  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Входящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Входящие пакеты туннеля  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Время кругового пути для проверки связи с виртуальной машиной  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Неудачные проверки связи с виртуальной машиной  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Incoming.all.failedrequests  |  Все неудачные входящие запросы  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Все входящие запросы  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Отправлено запланированных push-уведомлений  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Отменено запланированных push-уведомлений  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Операции управления установкой  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Операции удаления установки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Операции получения установки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Операции исправления установки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Операции создания или обновления установки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Все исходящие уведомления  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Ошибки из-за поврежденного или просроченного канала  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Ошибки канала  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Ошибки полезных данных  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Ошибки внешней системы уведомлений  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Успешные уведомления  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Ошибка из-за поврежденного канала APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Ошибка из-за просроченного канала APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Ошибки авторизации APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Ошибки APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Успешные уведомления APNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Ошибки проверки подлинности GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Ошибка из-за поврежденного канала GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Ошибка из-за просроченного канала GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Ошибки авторизации GCM (недопустимые учетные данные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Недопустимый формат уведомления GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Ошибки GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Успешные уведомления GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Регулируемые уведомления GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Ошибка из-за неправильного канала GCM  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Ошибки проверки подлинности MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Ошибка из-за поврежденного канала MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Канал MPNS отсоединен  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  Пропущенные уведомления MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Недопустимые учетные данные MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Недопустимый формат уведомления MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Ошибки MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Успешные уведомления MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Регулируемые уведомления MPNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Ошибки проверки подлинности WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Ошибка из-за поврежденного канала WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Канал WNS отсоединен  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Канал WNS регулируется  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  Пропущенные уведомления WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Ошибка из-за просроченного канала WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Ошибки авторизации WNS (недопустимые учетные данные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Недопустимый формат уведомления WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Ошибки авторизации WNS (недопустимый маркер)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Ошибки WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Успешные уведомления WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Регулируемые уведомления WNS  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Ошибки авторизации WNS (нет доступа)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Ошибки авторизации WNS (неправильный маркер)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  Операции регистрации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Операции создания регистрации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Операции удаления регистрации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Операции чтения регистрации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Операции обновления регистрации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Запланированных уведомлений в состоянии ожидания  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  Процент доступной памяти  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  Процент доступной области подкачки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  Использование выделенной памяти (в байтах), %  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  Процент времени DPC  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  Процент свободных индексных дескрипторов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  Процент времени простоя  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  Процент времени прерывания  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  Процент времени ожидания ввода-вывода  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  Процент времени работы с низким приоритетом  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  Процент времени работы в привилегированном режиме  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  Процент используемых индексных дескрипторов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  Процент используемой памяти  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  Процент используемого места  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  Процент используемой области подкачки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  Процент времени пользователя  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Доступный объем в МБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Доступный объем памяти в МБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Доступный объем подкачки в МБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Среднее время чтения с диска (с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Среднее время чтения с диска (с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время обращения к диску (с)  |  Среднее время обращения к диску (с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Среднее время записи на диск (с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Среднее время записи на диск (с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Полученных байтов/с  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Отправленных байтов/с  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Всего байтов/с  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Текущая длина очереди диска  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Скорость чтения с диска (байт/с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  Скорость записи на диск (байт/с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Операций записи на диск в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Операций записи на диск в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Объем свободной физической памяти  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Объем свободного места в файлах подкачки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Объем свободной виртуальной памяти  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Скорость обмена с логическим диском (байт/с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Операций чтения со страницы в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Операций записи на страницу в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Страниц в секунду  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Процент времени работы в привилегированном режиме  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Процент времени пользователя  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Скорость обмена с физическим диском (байт/с)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Процессы  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  Длина очереди процессора  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Объем, сохраненный в файлах подкачки  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Всего байт  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Всего получено байт  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Всего передано байт  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Всего конфликтов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Всего получено пакетов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Всего передано пакетов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Всего ошибок Rx  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Всего ошибок Tx  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Время доступности  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Используемая области подкачки в МБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Используемая память в КБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Используемый объем памяти в МБ  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Пользователи  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Виртуальная общая память  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Событие  |  Событие  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.OperationalInsights/workspaces  |  Пульс  |  Пульс  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Update  |  Update  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Пробуксовка памяти (наборы данных)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Высокая загрузка QPU  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Длительность запросов (наборы данных)  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Длина очереди заданий пула запросов (наборы данных)  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  SearchLatency  |  Search Latency  |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Search queries per second  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Throttled search queries percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Число активных сообщений в очереди или разделе.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  ЦП (не рекомендуется)  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Число недоставленных сообщений в очереди или разделе.  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Сообщения  |  Число сообщений в очереди или разделе.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  ЦП  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Использование памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Число запланированных сообщений в очереди или разделе.  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Размер  |  Размер  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Использование памяти (не рекомендуется)  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ContainerStatus  |  ContainerStatus  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Число подключений  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Inbound Traffic (Входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Количество сообщений  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Outbound Traffic (Исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Системные ошибки  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Ошибки пользователей  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Average CPU percentage (Средний процент использования ЦП)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Количество считанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Количество записанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_requests  |  IO requests count (Количество запросов ввода-вывода)  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Зарезервированное дисковое пространство  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Использованное дисковое пространство  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Virtual core count (Число виртуальных ядер)  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_storage_used  |  Используемое пространство данных  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_used  |  DTU used  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers  |  storage_used  |  Используемое пространство данных  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Выставлен счет за ЦП для приложения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Процент загрузки ЦП приложением  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Процент памяти приложения  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Заблокировано брандмауэром  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Cache hit percentage (Процент попаданий в кэш)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Cache used percentage (Процент использования кэша)  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Успешные подключения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Ограничение загрузки ЦП  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Загрузка ЦП  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  взаимоблокировка  |  Взаимоблокировки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Лимит DTU  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU used  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU percentage  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Лимит DWU  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU used  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Local tempdb percentage (Процент использования локальной базы данных tempdb)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Процент использования памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  носителей.  |  Используемое пространство данных  |  Байты  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Размер файла данных tempdb в КБ  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Размер файла журнала tempdb в КБ  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Процент использования журнала tempdb  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Выделенное пространство данных в процентах  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Ограничение загрузки ЦП  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Загрузка ЦП  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Ограничение загрузки ЦП  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Загрузка ЦП  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU used  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Используемое пространство данных  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU limit  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU used  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Максимальный размер данных  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Используемое пространство данных  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Размер файла данных tempdb в КБ  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Размер файла журнала tempdb в КБ  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Процент использования журнала tempdb  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Transactions  |  Transactions  |  Count  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Размер квоты файлового ресурса  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Число моментальных снимков файлового ресурса  |  Count  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Размер моментального снимка файлового ресурса  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientIOPS  |  Всего операций ввода-вывода в секунду для клиента  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientLatency  |  Средняя задержка клиента  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  Операций ввода-вывода в секунду для блокировок клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  Операций ввода-вывода в секунду для чтения метаданных клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  Операций ввода-вывода в секунду для записи метаданных клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  Операций ввода-вывода в секунду при чтении для клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Средняя пропускная способность чтения кэша  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  Операций ввода-вывода в секунду при записи для клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Средняя пропускная способность записи кэша  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Пропускная способность асинхронной записи StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  Пропускная способность заполнения StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Работоспособность целевого расположения хранилища  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  Всего операций ввода-вывода в секунду для StorageTarget  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetLatency  |  Задержка StorageTarget  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  Операций ввода-вывода в секунду при чтении метаданных StorageTarget  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  Операций ввода-вывода в секунду при записи метаданных StorageTarget  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  Пропускная способность упреждающего чтения StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  Операций ввода-вывода в секунду при чтении StorageTarget  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Пропускная способность синхронной записи StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Общая пропускная способность чтения StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Общая пропускная способность записи StorageTarget  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  Операций ввода-вывода в секунду при записи StorageTarget  |  Count  |  Среднее | 
| **Да**  | Нет |  Microsoft.StorageCache/caches  |  Время доступности  |  Время доступности  |  Count  |  Среднее | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Результат сеанса синхронизации  |  Count  |  Среднее | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Размер отзыва уровней в облаке по приложениям  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Размер отзыва уровней в облаке  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Пропускная способность отзыва уровней в облаке  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Состояние сервера в сети  |  Count  |  Максимальная | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Синхронизировано файлов  |  Count  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Состояние сервера в сети  |  Count  |  Максимальная | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Синхронизировано файлов  |  Count  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Синхронизировано файлов  |  Count  |  Итог | 
| **Да**  | Нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Неудачные запросы функций  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  События функций  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Запросы функций  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Ошибки преобразования данных  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Ошибки десериализации входа  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Неупорядоченные события  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Ранние входные события  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  ошибки  |  Ошибки среды выполнения  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Байты входного события  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Входные события  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Необработанные входные события  |  Count  |  Максимальная | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Полученные входные источники  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  События позднего ввода  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Выходные события  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Предельная задержка  |  Секунды  |  Максимальная | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Использование единиц потоковой передачи (%)  |  Процент  |  Максимальная | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Задержка чтения с диска  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Операции чтения с диска  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Задержка записи на диск  |  Миллисекунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Операции записи на диск  |  Count  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Активная память  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Предоставленная память  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Используемая память  |  Байты  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Сетевых входящих байт/с  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Сетевых исходящих байт/с  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Состояние готовности ЦП (CPU Ready)  |  Миллисекунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Активные запросы  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Длина дисковой очереди  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  HTTP 2xx  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP 3xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  HTTP 406:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  HTTP 4xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Ошибки HTTP-сервера:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Длина очереди HTTP:  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Крупный"  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Средний"  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Рабочие роли плана службы приложений уровня "Малый"  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Общее число внешних интерфейсов  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Доступные рабочие процессы  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Общее количество рабочих процессов  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Использованные рабочие процессы  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Длина дисковой очереди  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Длина очереди HTTP:  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Состояние TCP CLOSE-WAIT  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Состояние TCP CLOSING  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  Состояние TCP ESTABLISHED  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Состояние TCP FIN-WAIT-1  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Состояние TCP FIN-WAIT-2  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  Состояние TCP LAST-ACK  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  Состояние TCP SYN-RECEIVED  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  Состояние TCP SYN-SENT  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Состояние TCP TIME-WAIT  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AppConnections  |  Соединения  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CpuTime  |  Время ЦП:  |  Секунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Текущее число сборок  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Function Execution Count  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Function Execution Units  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen0Collections  |  Сборок мусора поколения 0  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen1Collections  |  Сборок мусора поколения 1  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen2Collections  |  Сборок мусора поколения 2  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Маркеры  |  Счетчик дескрипторов  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Состояние проверки работоспособности  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http2xx  |  HTTP 2xx  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http3xx  |  HTTP 3xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http401  |  HTTP 401:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http403  |  HTTP 403:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http404  |  HTTP 404:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http406  |  HTTP 406:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http4xx  |  HTTP 4xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http5xx  |  Ошибки HTTP-сервера:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  HttpResponseTime  |  Время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  PrivateBytes  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Потоки  |  Счетчик потоков  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomains  |  Всего доменов приложений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AppConnections  |  Соединения  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CpuTime  |  Время ЦП:  |  Секунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Текущее число сборок  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Function Execution Count  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Function Execution Units  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Сборок мусора поколения 0  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Сборок мусора поколения 1  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Сборок мусора поколения 2  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Маркеры  |  Счетчик дескрипторов  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Состояние проверки работоспособности  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http2xx  |  HTTP 2xx  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http3xx  |  HTTP 3xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http404  |  HTTP 404:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http406  |  HTTP 406:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http4xx  |  HTTP 4xx:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http5xx  |  Ошибки HTTP-сервера:  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Потоки  |  Счетчик потоков  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Всего доменов приложений  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Count  |  Среднее | 
