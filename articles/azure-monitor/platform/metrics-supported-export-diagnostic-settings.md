---
title: Метрики платформы Azure Monitor, экспортируемые через диагностические настройки
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661368"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Метрики платформы Azure Monitor, экспортируемые через диагностические настройки

Azure Monitor предоставляет [метрики платформы](data-platform-metrics.md) по умолчанию без конфигурации. Он предоставляет несколько способов взаимодействия с метриками платформы, включая их составление графиков на портале, доступ к ним через REST API или запрос с помощью PowerShell или CLI. Смотрите [метрики](metrics-supported.md) с поддержкой полного списка метрик платформы, доступных в настоящее время с консолидированным метрическим конвейером Azure Monitor. Для запроса и доступа к этим показателям, пожалуйста, используйте [api-версию 2018-01-01 api.](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий.

Метрики платформы можно экспортировать из конвейера мониторинга Azure в другие места одним из двух способов.
1. Использование [диагностических настроек](diagnostic-settings.md) для отправки в журналAnalytics, концентраторы событий или хранилище Azure.
2. Использование [метрик REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Из-за тонкостей в бэкэнде Azure Monitor не все метрики экспортируются с помощью диагностических настроек. В таблице ниже перечислены списки, которые можно экспортировать и не могут экспортировать с помощью диагностических настроек.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Изменение поведения для значений NUL и нулевой 
 
Для метрик платформы, которые могут быть экспортированы через диагностические настройки, есть несколько метрик, для которых Azure Monitor интерпретирует '0s' как 'Nulls'. Это вызвало некоторую путаницу между реальными '0s' (излучаемые ресурсом) и интерпретируемыми '0s' (Nulls). Начиная с **1 апреля 2020** года, метрики платформы, экспортируемой через диагностические настройки, больше не будут экспортировать '0s', если они действительно не были испущены базовым ресурсом. Обратите внимание на следующее:

1.  При удалении группы ресурсов или определенного ресурса метрические данные из эффективных ресурсов больше не будут отправляться в диагностические направления экспорта. То есть он больше не будет отображаться в концентрах событий, учетных записях хранения данных и рабочих областях аналитики журналов.
2.  Это улучшение будет доступно во всех общественных и частных облаках.
3.  Это изменение не повлияет на поведение любого из следующих опытов: 
   - Платформы Ресурс журналы экспортируются через диагностические настройки
   - Диаграмма в графике в исследователе метрик
   - Предупреждение о метриках платформы
 
## <a name="metrics-exportable-table"></a>Таблица показателей экспортируемой 

В таблице содержатся следующие столбцы. 
- Экспортируемый через диагностические настройки? 
- Действует по NULL / 0 
- ResourceType 
- Метрика 
- MetricDisplayName
- Единицы 
- AggregationType


> [!NOTE]
> В таблице ниже может быть горизонтальная панель прокрутки внизу. Если вы считаете, что вам не хватает информации, проверьте, что панель прокрутки находится влево.  


| Экспортируемый через диагностические настройки?  | Излучает НулЛС |  ResourceType  |  Метрика  |  MetricDisplayName  |  Единицы  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Память: текущая цена очистки  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Память: несжимаемая память очистки  |  Байты  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Память: сжимаемая память очистки  |  Байты  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Потоки: занятые потоки в пуле команд  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Потоки: бездействующие потоки в пуле команд  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Длина очереди заданий пула команд  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Подключение: текущие подключения  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Текущие пользовательские сеансы  |  Count  |  Среднее | 
| Да,  | нет |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Потоки: занятые потоки продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Потоки: бездействующие потоки продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Потоки: длина очереди заданий продолжительного анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Память подсистемы M  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M Двигатель Частные Байты  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU подсистемы M  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M Двигатель Виртуальный Байты  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Пробуксовка памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Память: твердый лимит памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Память: верхний предел памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Память: нижний предел памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Память: ограничение памяти VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Память: использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Потоки: занятые потоки ввода-вывода в пуле обработки заданий  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Потоки: длина очереди заданий ввода-вывода пула обработки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Длина очереди заданий пула обработки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Занятые потоки в пуле запросов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Потоки: бездействующие потоки в пуле запросов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Потоки: длина очереди заданий пула запросов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  Quota  |  Память: квота  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Память: заблокировано квот  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Обработка: преобразовано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Обработка: считано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Обработка: записано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Потоки: занятые потоки быстрого анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Потоки: бездействующие потоки быстрого анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Потоки: длина очереди заданий быстрого анализа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Число успешных подключений в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Общее число неудачных подключений  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Общее число запросов на подключение  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Память: размер нестраничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Память: размер страничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Байт виртуальной памяти  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  БэкендАсдлительно  |  Длительность запросов backend  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Duration  |  Общая длительность запросов шлюза  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Выпавные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Отклоненные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubУспешныеСобытия  |  Успешные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubThrottledСобытия  |  Затмили события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Приуроченные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesСент  |  Размер событий EventHub  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Всего событий EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedСобытия  |  Неудачные события EventHub  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Неудачные запросы шлюза (Обезумевший)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Другие запросы шлюза (Обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Успешные запросы шлюза (Обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Всего запросов шлюза (Обезвращено)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Несанкционированные запросы шлюза (обезвращенные)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  AppCpuUsageПроцент  |  Процент использования процессора app  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  AppMemoryЗавершенный  |  Назначена память приложения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  AppMemoryMax  |  App Memory Max  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  AppMemoryUsed  |  Используется память приложения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  GCPauseTotalCount  |  GC Пауза граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  GCPauseTotalTime  |  GC Пауза Общее время  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  MaxOldGenMemorypoolBytes  |  Макс Доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  OldGenMemorypoolBytes  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  OldGenPromotedBytes  |  Содействие старому размеру данных поколения  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  SystemCpuUsageПроцент  |  Процент использования процессора системы  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatОшибкаCount  |  Глобальная ошибка Tomcat  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  ТомктатАтПолучаетБайт  |  Томктат Всего получил байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatRequestMaxTime  |  Tomcat Запрос Макс Время  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatRequestTotalCount  |  Tomcat Запрос Всего Граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatRequestTotalTime  |  Tomcat Запрос Всего Таймс  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatResponseAvgTime  |  Tomcat Запрос Среднее время  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  ТомктатСентБайтс  |  Tomcat Всего отправлены байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionActiveCurrentCount  |  Томктат Сессия живой граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionActiveMaxCount  |  Томктат Сессия Макс Активный граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionLiveMaxTime  |  Томктат Сессия Макс живое время  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionCreatedCount  |  Томктат Сессия Создан граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionExpiredCount  |  Tomcat Сессия истек граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  TomcatSessionИзлученоеCount  |  Томктат Сессия Отклонено граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.AppPlatform/Весна  |  YoungGenPromotedBytes  |  Содействие молодому поколению размер данных  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Всего заданий  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentМашинаПробегRuns  |  Total Update Deployment Machine Runs (Общее количество запусков развертывания обновлений для компьютера)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentDeploymentRuns  |  Total Update Deployment Runs (Общее количество запусков развертывания обновлений)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedicated Core Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Creating Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Idle Node Count  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Job Delete Complete Events (События окончания удаления задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Job Delete Start Events (События начала удаления задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Job Disable Complete Events (События окончания отключения задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Job Disable Start Events (События начала отключения задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Job Start Events (События запуска задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Job Terminate Complete Events (События окончания завершения задания)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Job Terminate Start Events (События начала завершения задания)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Leaving Pool Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority Core Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Offline Node Count  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Pool Create Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Pool Delete Complete Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Pool Delete Start Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Pool Resize Complete Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Pool Resize Start Events  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Preempted Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Rebooting Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Reimaging Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Running Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Starting Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Start Task Failed Node Count  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Task Complete Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Task Fail Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Task Start Events  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Low-Priority Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Dedicated Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Unusable Node Count  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Waiting For Start Task Node Count  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Активные ядра  |  Активные ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Активные узлы  |  Активные узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Ядра холостого хода  |  Ядра холостого хода  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Узлы безделья  |  Узлы безделья  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Завершена работа  |  Завершена работа  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Задание, отправленное  |  Задание, отправленное  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Оставляя ядра  |  Оставляя ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Оставляя узлы  |  Оставляя узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Упреждаемые ядра  |  Упреждаемые ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Упреждаемые узлы  |  Упреждаемые узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Процент использования квот  |  Процент использования квот  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Всего ядер  |  Всего ядер  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Всего узлов  |  Всего узлов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Непригодные для угобы  |  Непригодные для угобы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.BatchAI/рабочие области  |  Непригодные узлы  |  Непригодные узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ПодключениеПринято  |  Принятые соединения  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ПодключениеОбработано  |  Обрабатываемые соединения  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  CpuUsageПроцентинвдвойнья  |  Процент использования процессора  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  IO Читать Байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  IO Написать байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Предел памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsageПроцентинваубл  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  Отложенные сделки  |  Отложенные транзакции  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  Обработанныеблоки  |  Обработанные блоки  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ОбработанныеСделки  |  Обработанные транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  Очередные сделки  |  Очередные транзакции  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ЗапросОбработано  |  Обработка запросов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Blockchain/blockchainMembers  |  ХранениеUsage данных  |  Использование хранилища  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits  |  Попаданий в кэш  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheLatency  |  Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses  |  Промахов в кэше  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead  |  Чтение из кэша  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead0  |  Cache Read (Shard 0)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead1  |  Cache Read (Shard 1)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead2  |  Cache Read (Shard 2)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead3  |  Cache Read (Shard 3)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead4  |  Cache Read (Shard 4)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead5  |  Cache Read (Shard 5)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead6  |  Cache Read (Shard 6)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead7  |  Cache Read (Shard 7)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead8  |  Cache Read (Shard 8)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheRead9  |  Cache Read (Shard 9)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite  |  Запись в кэш  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache Write (Shard 0)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache Write (Shard 1)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache Write (Shard 2)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache Write (Shard 3)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache Write (Shard 4)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache Write (Shard 5)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache Write (Shard 6)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache Write (Shard 7)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache Write (Shard 8)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache Write (Shard 9)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients  |  Подключенные клиенты  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients0  |  Connected Clients (Shard 0)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients1  |  Connected Clients (Shard 1)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients2  |  Connected Clients (Shard 2)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients3  |  Connected Clients (Shard 3)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients4  |  Connected Clients (Shard 4)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients5  |  Connected Clients (Shard 5)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients6  |  Connected Clients (Shard 6)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients7  |  Connected Clients (Shard 7)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients8  |  Connected Clients (Shard 8)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients9  |  Connected Clients (Shard 9)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  ошибки  |  ошибки  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys  |  Исключенные ключи  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys0  |  Evicted Keys (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys1  |  Evicted Keys (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys2  |  Evicted Keys (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys3  |  Evicted Keys (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys4  |  Evicted Keys (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys5  |  Evicted Keys (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys6  |  Evicted Keys (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys7  |  Evicted Keys (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys8  |  Evicted Keys (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys9  |  Evicted Keys (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys  |  Ключи с истекшим сроком действия  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys0  |  Expired Keys (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys1  |  Expired Keys (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys2  |  Expired Keys (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys3  |  Expired Keys (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys4  |  Expired Keys (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys5  |  Expired Keys (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys6  |  Expired Keys (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys7  |  Expired Keys (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys8  |  Expired Keys (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys9  |  Expired Keys (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands  |  Операций считывания  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond  |  Количество операций в секунду  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Количество операций в секунду (сегмент 0).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Количество операций в секунду (сегмент 1).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Количество операций в секунду (сегмент 2).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Количество операций в секунду (сегмент 3).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Количество операций в секунду (сегмент 4).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Количество операций в секунду (сегмент 5).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Количество операций в секунду (сегмент 6).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Количество операций в секунду (сегмент 7).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Количество операций в секунду (сегмент 8).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Количество операций в секунду (сегмент 9).  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime  |  ЦП  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad  |  Загрузка сервера  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad0  |  Server Load (Shard 0)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad1  |  Server Load (Shard 1)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad2  |  Server Load (Shard 2)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad3  |  Server Load (Shard 3)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad4  |  Server Load (Shard 4)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad5  |  Server Load (Shard 5)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad6  |  Server Load (Shard 6)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad7  |  Server Load (Shard 7)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad8  |  Server Load (Shard 8)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  serverLoad9  |  Server Load (Shard 9)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands  |  Наборы  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands1  |  Sets (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands2  |  Sets (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands5  |  Sets (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Всего операций  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total Operations (Shard 0)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total Operations (Shard 1)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total Operations (Shard 2)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total Operations (Shard 3)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total Operations (Shard 4)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total Operations (Shard 5)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total Operations (Shard 6)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total Operations (Shard 7)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total Operations (Shard 8)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total Operations (Shard 9)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys  |  Всего ключей  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys0  |  Total Keys (Shard 0)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys1  |  Total Keys (Shard 1)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys2  |  Total Keys (Shard 2)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys3  |  Total Keys (Shard 3)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys4  |  Total Keys (Shard 4)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys5  |  Total Keys (Shard 5)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys6  |  Total Keys (Shard 6)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys7  |  Total Keys (Shard 7)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys8  |  Total Keys (Shard 8)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys9  |  Total Keys (Shard 9)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory  |  Используемая память  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory0  |  Used Memory (Shard 0)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory1  |  Used Memory (Shard 1)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory2  |  Used Memory (Shard 2)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory3  |  Used Memory (Shard 3)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory4  |  Used Memory (Shard 4)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory5  |  Used Memory (Shard 5)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory6  |  Used Memory (Shard 6)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory7  |  Used Memory (Shard 7)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory8  |  Used Memory (Shard 8)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemory9  |  Used Memory (Shard 9)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Процент используемой памяти  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS используемой памяти  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Used Memory RSS (Shard 0)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Used Memory RSS (Shard 1)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Used Memory RSS (Shard 2)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Used Memory RSS (Shard 3)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Used Memory RSS (Shard 4)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Used Memory RSS (Shard 5)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Used Memory RSS (Shard 6)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Used Memory RSS (Shard 7)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Used Memory RSS (Shard 8)  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Used Memory RSS (Shard 9)  |  Байты  |  Максимальная | 
| нет  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareКвота  |  Размер квоты доли файла  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Количество снимков файла  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Размер снимка файла доля  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Blocked Calls  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  ХарактеристикиОбученные  |  Персонажи обучены  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Преобразованные символы  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Client Errors  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  Задержка  |  Задержка  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server Errors  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Длительность речи  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Successful Calls  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Total Calls  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Total Errors  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Всего вызовов токенов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Всего транзакций  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Диск данных Читайте Байты/сек  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операции чтения диска данных/Sec  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Диск данных Написать Байты / сек  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операции записи диска данных/Sec  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Входящие потоки Максимальная скорость создания  |  Входящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в билле (Унипраженные)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть в общей сложности  |  Сеть в общей сложности  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть out Billable (Унипрачено)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть Out Всего  |  Сеть Out Всего  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  OS Disk Read Bytes/sec  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операции чтения диска Os/Sec  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  OS Disk Написать Байты / сек  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операции записи диска OS/Sec  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди на диск ОС  |  ОС Диск ЗД (Депрекционированный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  OS Disk Read Bytes/Sec (Обезображено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операции чтения диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  OS Disk Написать Байты / Sec (Обезображенные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операции записи диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Исходящие потоки Максимальная скорость создания  |  Исходящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных QD (Обезвращенный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Диск данных Читайте Байты/Сек (Обезвращенный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операции чтения диска данных/Sec (Обезвращено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Диск данных Написать Байты / Sec (Обезвреженный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операции записи диска данных/Sec (Обезвреженные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Премиум кэш диска данных Читать Хит  |  Премиум кэш диска данных Читать хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Премиум данных диска Кэш Читать Мисс  |  Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Премиум OS диск кэш Читать Хит  |  Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Премиум OS диск кэш Читать Мисс  |  Премиум OS диск кэш Читать мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Диск данных Читайте Байты/сек  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операции чтения диска данных/Sec  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Диск данных Написать Байты / сек  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операции записи диска данных/Sec  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Входящие потоки Максимальная скорость создания  |  Входящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (входящий трафик)  |  Сеть в билле (Унипраженные)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть в общей сложности  |  Сеть в общей сложности  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (исходящий трафик)  |  Сеть out Billable (Унипрачено)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть Out Всего  |  Сеть Out Всего  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  OS Disk Read Bytes/sec  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операции чтения диска Os/Sec  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  OS Disk Написать Байты / сек  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операции записи диска OS/Sec  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди на диск ОС  |  ОС Диск ЗД (Депрекционированный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  OS Disk Read Bytes/Sec (Обезображено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (операций/с)  |  Операции чтения диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  OS Disk Написать Байты / Sec (Обезображенные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (операций/с)  |  Операции записи диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Исходящие потоки Максимальная скорость создания  |  Исходящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди в расчете на диск  |  Диск данных QD (Обезвращенный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (байт/с)  |  Диск данных Читайте Байты/Сек (Обезвращенный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (операций/с)  |  Операции чтения диска данных/Sec (Обезвращено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (байт/с)  |  Диск данных Написать Байты / Sec (Обезвреженный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (операций/с)  |  Операции записи диска данных/Sec (Обезвреженные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Премиум кэш диска данных Читать Хит  |  Премиум кэш диска данных Читать хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Премиум данных диска Кэш Читать Мисс  |  Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Премиум OS диск кэш Читать Хит  |  Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Премиум OS диск кэш Читать Мисс  |  Премиум OS диск кэш Читать мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Диск данных Читайте Байты/сек  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операции чтения диска данных/Sec  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Диск данных Написать Байты / сек  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операции записи диска данных/Sec  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Входящие потоки Максимальная скорость создания  |  Входящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в билле (Унипраженные)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть в общей сложности  |  Сеть в общей сложности  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть out Billable (Унипрачено)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть Out Всего  |  Сеть Out Всего  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительный просмотр)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Disk Read Bytes/sec  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операции чтения диска Os/Sec  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Disk Написать Байты / сек  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операции записи диска OS/Sec  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди на диск ОС  |  ОС Диск ЗД (Депрекционированный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  OS Disk Read Bytes/Sec (Обезображено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операции чтения диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  OS Disk Написать Байты / Sec (Обезображенные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операции записи диска OS/Sec (Унипрачено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Исходящие потоки Максимальная скорость создания  |  Исходящие потоки Максимальная скорость создания (Предварительный просмотр)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных QD (Обезвращенный)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Диск данных Читайте Байты/Сек (Обезвращенный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операции чтения диска данных/Sec (Обезвращено)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Диск данных Написать Байты / Sec (Обезвреженный)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операции записи диска данных/Sec (Обезвреженные)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Премиум кэш диска данных Читать Хит  |  Премиум кэш диска данных Читать хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Премиум данных диска Кэш Читать Мисс  |  Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Премиум OS диск кэш Читать Хит  |  Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Премиум OS диск кэш Читать Мисс  |  Премиум OS диск кэш Читать мисс (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Загрузка ЦП  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Получено байтов по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Передано байт по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Длительность выполнения  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  УспешныйPullCount  |  Успешный потяните графа  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  УспешныйPushCount  |  Успешный push Граф  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Общий подсчет потяните  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Всего push граф  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Общее количество доступных ядер ЦП в управляемом кластере  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Общий объем доступной памяти в управляемом кластере  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Состояния для различных условий узла  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Число модулей pod по фазам  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Число модулей pod в состоянии готовности  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  Доступнаяемкость  |  Доступная емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  БайтсЗагруженоОблако Облака  |  Облачные байты загружены (устройство)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  БайтыЗагруженыДляИКЛуперПоделитьь  |  Облачные байты загружены (доля)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  CloudReadThroughput  |  Пропускная часть облачной загрузки  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Прокрутка облачной загрузки (доля)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  ОблакоUploadThroughput  |  Пропускная часть облачной загрузки  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Пропускная часть облачной загрузки (доля)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  ГиперВПамятоутилизация  |  Край вычисления - Использование памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Край вычислить - Процентный процессор  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  NICReadThroughput  |  Проликвная пропускная готовность (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  NICWriteThroughput  |  Запись пропускной записи (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.DataBoxEdge/DataBoxEdgeDevices  |  Общая вместимость  |  Общая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/datafactories  |  FailedRuns и  |  циклы выполнения со сбоем;  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns.  |  успешные циклы выполнения.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  АктивностьОтмененныеБега  |  Отмененные действия выполняет метрики  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ActivityFailedRuns  |  Метрики неудачных выполнений действий.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ActivitySucceededRuns  |  Метрики успешных выполнений действий.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  FactorySizeingbunitsunits  |  Общий размер фабрики (единица ГБ)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeAvailableMemory  |  Доступная память для среды выполнения интеграции  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ИнтеграцияRuntimeAverageTaskUpDelay  |  Длительность очереди времени выполнения интеграции  |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeCpuPercentage  |  Использование ЦП средой выполнения интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ИнтеграцияВывремяКиДлина  |  Интеграция длины очереди выполнения  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  MaxAllowedFactorySizeingbunitsunits  |  Максимально допустимый размер фабрики (единица ГБ)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  MaxAllowedResourceCount  |  Максимально допустимые субъекты считаются  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ТрубопроводОтмененныеЗаистые  |  Отмененный конвейер выполняет метрики  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  PipelineFailedRuns  |  Метрики неудачных выполнений конвейеров.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  PipelineSucceededRuns  |  Метрики успешных выполнений конвейеров.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  Ресурсный счет  |  Общее количество сущностей  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  TriggerCancelledRuns  |  Отмененные триггерные запуски метрик  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  TriggerFailedRuns  |  Метрики неудачных запусков триггеров.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  TriggerSucceededRuns  |  Метрики успешных запусков триггеров.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Cancelled AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Failed AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Successful AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Cancelled Jobs  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Failed Jobs  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Successful Jobs  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Данных прочитано  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Записанные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Запросы на чтение  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Общий объем хранилища  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Запросы на запись  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Используется резервное хранилище  |  Байты  |  Среднее | 
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
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Используется резервное хранилище  |  Байты  |  Среднее | 
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
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Используется резервное хранилище  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Максимальная задержка между репликами  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Задержка реплики  |  Секунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  active_connections  |  Активные подключения  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  Iops  |  ОПЕРАЦИЙ ВВОДА-ВЫВОДА  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreS'L/serversv2  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Цифровой Близнецы Узла Телеметрия Заполнитель  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D сообщения отказались  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Поставки сообщений C2D завершены  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Сообщения C2D отклонены  |  Count  |  Итог | 
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
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  C2DСообщенияИ  |  C2D Сообщения истек (предварительный просмотр)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  конфигурации  |  Configuration Metrics (Метрики конфигурации)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Connected devices (preview) (Подключенных устройств (предварительная версия))  |  Count  |  Среднее | 
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
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Общее использование данных устройства  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Connected devices (deprecated) (Подключенные устройства (не рекомендуется))   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Total devices (deprecated) (Всего устройств (не рекомендуется))  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Доставка сетки событий (предварительный просмотр)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  EventGridЛатенция  |  Задержка сетки событий (предварительный просмотр)  |  Миллисекунды  |  Среднее | 
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
| нет  | нет |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total devices (preview) (Всего устройств (предварительная версия))  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Неудачные запросы двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Размер результатов запросов двойников.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Успешные запросы двойников.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Попытки аттестации  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Назначенные устройства  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Попытки регистрации  |  Count  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Доступная служба хранилища  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Отключение связи Cassandra  |  Count  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Расходы запросов по Cassandra  |  Count  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Запросы по Cassandra  |  Count  |  Count | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Использование данных  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DocumentDB/databaseAccounts  |  УдалитьВиртуальная сеть  |  УдалитьВиртуальная сеть  |  Count  |  Count | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Число документов  |  Count  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Квота на документы  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Использование индексов  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Запросы метаданных  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Запросить оплату Mongo  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Запросы Mongo  |  Count  |  Count | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Коэффициент запроса Mongo  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsУдалить  |  Коэффициент запроса на удаление Mongo  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Коэффициент запроса на вставку Mongo Insert  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  МонгоЗапросыКЕри  |  Коэффициент запроса запроса Mongo  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Коэффициент запроса на обновление Mongo  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Подготовленная пропускная способность  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Задержка репликации P99  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Доступность службы  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Общее количество запросов  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Общее количество единиц запросов  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EnterpriseKnowledgeGraph/услуги  |  ОтказCount  |  Число сбоев  |  Count  |  Count | 
| нет  | нет |  Microsoft.EnterpriseKnowledgeGraph/услуги  |  SuccessCount  |  Число успехов  |  Count  |  Count | 
| нет  | нет |  Microsoft.EnterpriseKnowledgeGraph/услуги  |  Успешноела  |  Задержка успеха  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.EnterpriseKnowledgeGraph/услуги  |  Транзакционный счет  |  Граф транзакций  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventGrid/домены  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventGrid/домены  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  DroppedEventCount  |  Удаленные события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  MatchedEventCount  |  Соответствующие события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  PublishFailCount  |  Публикация неудачных событий  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/домены  |  ПубликацияУспехАвайте  |  Публикация задержки успеха  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Удаленные события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Соответствующие события  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Публикация неудачных событий  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  ПубликацияУспехАвайте  |  Публикация задержки успеха  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Публикация неудачных событий  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  ПубликацияУспехАвайте  |  Публикация задержки успеха  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Доступная память  |  Процент  |  Максимальная | 
| нет  | нет |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Невыполненная работа записи.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Записанные сообщения.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ЦП  |  ЦП  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/clusters  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Невыполненная работа записи.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Записанные сообщения.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHABL  |  Архивные сообщения невыполненной работы (Депрекатированные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Пропускная пропускная часть архивного сообщения (Обезвращенная)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Архивные сообщения (обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Входящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Входящие байты (устаревшие) (Обезображенные)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Входящие сообщения (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Исходящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Исходящие байты (устаревшие) (Обезображенные)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Исходящие сообщения (не рекомендуется)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Неудачные запросы (обезумевший)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Входящие Сообщения (устаревшие) (Устаревшие)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INREQS  |  Входящие запросы (обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INTERR  |  Внутренние ошибки сервера (обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Другие ошибки (обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Исходящие Сообщения (устаревшие) (Устаревшие)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  Размер  |  Размер  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Успешные запросы (обезвращенные)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Ошибки сервера заняты (Deprecated)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.EventHub/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Запросы к шлюзу по категориям  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Запросы к шлюзу  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Количество активных работников  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.HDInsight/clusters  |  МасштабированиеЗапросы  |  Запросы масштабирования  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Пороговое значение метрики  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Наблюдаемая емкость  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Наблюдаемое значение метрики  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Инициированные действия масштабирования  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Insights/Components  |  доступностьРезультат/доступностьПроцент  |  Доступность  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Insights/Components  |  доступностьРезультаты/счет  |  Тесты доступности  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Продолжительность теста доступности  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Время подключения к сети при загрузке страницы  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Время обработки клиента  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Время получения ответа  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Время отправки запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Время загрузки страницы в браузере  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Insights/Components  |  dependencies/count  |  Вызовы зависимостей  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.Insights/Components  |  dependencies/duration  |  Длительность зависимости  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Insights/Components  |  dependencies/failed  |  Сбои вызовов зависимости  |  Count  |  Count | 
| нет  | нет |  Microsoft.Insights/Components  |  exceptions/browser  |  Исключения браузера  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  exceptions/count  |  Исключения  |  Count  |  Count | 
| нет  | нет |  Microsoft.Insights/Components  |  exceptions/server  |  Исключения сервера  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  pageViews/count  |  Просмотры страниц  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.Insights/Components  |  pageViews/duration  |  Время загрузки страницы для просмотра  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Частота исключений  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Объем доступной памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Обработка ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Скорость ввода-вывода процесса  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Процессорное время  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Количество байтов исключительного использования процессов  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Время выполнения HTTP-запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP requests in application queue (HTTP-запросы в очереди приложений)  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Скорость HTTP-запроса  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Insights/Components  |  requests/count  |  Запросы сервера  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.Insights/Components  |  requests/duration  |  Время ответа от сервера  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Insights/Components  |  requests/failed  |  Failed requests (Неудачные запросы)  |  Count  |  Count | 
| нет  | нет |  Microsoft.Insights/Components  |  запросы/тариф  |  Скорость запроса сервера  |  Число/с  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  traces/count  |  Трассировки  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Всего попаданий в API службы  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Общая задержка API службы  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Всего результатов для API службы  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  КахеУтилизация  |  Использование кэша  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  НепрерывныйэкспортМаксЛатнесСМинутами  |  Непрерывный экспорт Макс Опоздание Минуты  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Непрерывныйэкспортнизоборонэкспорт  |  Непрерывный экспорт - нюм экспортированных записей  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  НепрерывныйЭкспортВожиданиСчет  |  Непрерывный экспорт в ожидании графа  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  НепрерывныйЭкспортРезультат  |  Непрерывный результат экспорта  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ЦП  |  ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Обработка событий (для концентраторов событий/IoT)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Экспортутилизация  |  Утилизация экспорта  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ПроглатительностьЛаустьInSeconds  |  Задержка приема (в секундах)  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Результат приема  |  Count  |  Count | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Заглатываетустиюутилизацию  |  Использование приема  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ПроглатываниеVolumeInMB  |  Объем приема (в МБ)  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Keepalive  |  Сохраняйте жизнь  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Query duration (Длительность запросов)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Скорость потоковой передачи запроса  |  Count  |  RateRequestsPerSecond | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngdataRate  |  Скорость потоковой передачи данных  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Продолжительность потоковой передачи  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Результат потоковой передачи  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Actions Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Actions Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Actions Skipped   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Actions Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Actions Succeeded   |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Action Throttled Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ИнтеграцияServiceяСредаКоннекорПамятьИспользование  |  Использование памяти соединения для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ИнтеграцияServiceяСредаКоннекорProcessorUsage  |  Использование процессора соединения для среды интеграционного обслуживания  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ИнтеграцияServiceяОкружающаясредаПроцессПамятиИспользование  |  Использование памяти рабочего процесса для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  ИнтеграцияServiceяСредаПроцессОбработка  |  Использование процессора рабочего процесса для среды службы интеграции  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Запуски отменены  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Runs Completed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Runs Failed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Runs Started  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Runs Succeeded  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledСобытия  |  Выполнить события, затмеваемые  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Run Throttled Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Triggers Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Triggers Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Triggers Fired   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Triggers Skipped  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Triggers Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Triggers Succeeded   |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Actions Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Actions Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Actions Skipped   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Actions Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Actions Succeeded   |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
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
| **Да**  | нет |  Microsoft.Logic/workflows  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Запуски отменены  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Runs Completed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsFailed  |  Runs Failed  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsStarted  |  Runs Started  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Runs Succeeded  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunStartThrottledСобытия  |  Выполнить события, затмеваемые  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Run Throttled Events  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Total Billable Executions  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Triggers Completed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Triggers Failed   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFired  |  Triggers Fired   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Triggers Skipped  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Triggers Started   |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Triggers Succeeded   |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Активные ядра  |  Активные ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Активные узлы  |  Активные узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Завершено работает  |  Завершено работает  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  циклы выполнения со сбоем;  |  циклы выполнения со сбоем;  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Ядра холостого хода  |  Ядра холостого хода  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Узлы безделья  |  Узлы безделья  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Оставляя ядра  |  Оставляя ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Оставляя узлы  |  Оставляя узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Развертывание модели не удалось  |  Развертывание модели не удалось  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Модель развертывания запущен  |  Модель развертывания запущен  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Модель Развертывание Успешно  |  Модель Развертывание Успешно  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Модель Регистр не удалось  |  Модель Регистр не удалось  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Модель Регистр Аймон преуспел  |  Модель Регистр Аймон преуспел  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Упреждаемые ядра  |  Упреждаемые ядра  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Упреждаемые узлы  |  Упреждаемые узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Процент использования квот  |  Процент использования квот  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Запущенные запуски  |  Запущенные запуски  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Всего ядер  |  Всего ядер  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Всего узлов  |  Всего узлов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Непригодные для угобы  |  Непригодные для угобы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Непригодные узлы  |  Непригодные узлы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Maps/accounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Maps/accounts  |  Использование  |  Использование  |  Count  |  Count | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  АктивЫ  |  Подсчет активов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  Активная квокты  |  Квота активов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  АктивКваиспользованныйПроцент  |  Квота активов используется процент  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Количество ключевых политик содержимого  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  КонтентКейПолитикиКвота  |  Квота политики ключевой контента  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  ContentKeyPolicyИспользованныйПроцент  |  Квота политики ключевых содержимого используется в процентах  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Количество потоков политики  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  Потоковая политика  |  Квота политики потоковой передачи  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices  |  StreamingPolicy's  |  Квота политики потоковой передачи используется в процентах  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  Requests  |  Requests  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Успех до конца Задержки  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  GCPauseTotalCount  |  GC Пауза граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  GCPauseTotalTime  |  GC Пауза Общее время  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  MaxOldGenMemorypoolBytes  |  Макс Доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  OldGenMemorypoolBytes  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  OldGenPromotedBytes  |  Содействие старому размеру данных поколения  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ServiceCpuUsageПроцент  |  Процент использования процессора обслуживания  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ServiceMemoryЗавершено  |  Назначена служба памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ServiceMemoryMax  |  Сервисная память Макс  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ServiceMemoryUsed  |  Используется память службы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  SystemCpuUsageПроцент  |  Процент использования процессора системы  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatОшибкаCount  |  Глобальная ошибка Tomcat  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ТомктатАтПолучаетБайт  |  Томктат Всего получил байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatRequestMaxTime  |  Tomcat Запрос Макс Время  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatRequestTotalCount  |  Tomcat Запрос Всего Граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatRequestTotalTime  |  Tomcat Запрос Всего Таймс  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatResponseAvgTime  |  Tomcat Запрос Среднее время  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  ТомктатСентБайтс  |  Tomcat Всего отправлены байты  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionActiveCurrentCount  |  Томктат Сессия живой граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionActiveMaxCount  |  Томктат Сессия Макс Активный граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionLiveMaxTime  |  Томктат Сессия Макс живое время  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionCreatedCount  |  Томктат Сессия Создан граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionExpiredCount  |  Tomcat Сессия истек граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  TomcatSessionИзлученоеCount  |  Томктат Сессия Отклонено граф  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Microservices4Spring/appКластеры  |  YoungGenPromotedBytes  |  Содействие молодому поколению размер данных  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Volume pool allocated used (Используемый размер выделенного пула для тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Volume pool total logical size (Общий логический размер пула для тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Средняя задержка чтения  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Средняя задержка записи  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  Read iops (Число операций ввода-вывода в секунду при чтении)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Volume logical size (Логический размер тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Volume snapshot size (Размер моментальных снимков в томе)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  Write iops (Количество операций ввода-вывода в секунду при записи)  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  ПриложениеGatewayTotalTime  |  Приложение шлюз Общее время  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Запросы в минуту на здорового хозяина  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  БэкендКоннети  |  Бэкэнд Время подключения  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Бэкэнд Первый байт Время ответа  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Бэкэнд Последний байт Время ответа  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  БэкендОтветСтатус  |  Статус ответа бэкэнда  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  Заблокированный Счет  |  Веб-приложение Firewall заблокировал распределение правил запросов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  ЗаблокированныйReqCount  |  Веб-приложение Брандмауэр Заблокированные Запросы Отсчет  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  BytesSent  |  Отправлено байт  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  ЕмкостьЕдиницы  |  Единицы текущего емкости  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Клиент RTT  |  MilliSeconds  |  Среднее | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  Вычислительные единицы  |  Текущие вычислительные единицы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Текущие подключения.  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Невыполненные запросы  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Количество работоспособных узлов.  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Веб-приложение Firewall Полное распределение правил  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Состояние ответа.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Network/applicationGateways  |  Пропускная способность  |  Пропускная способность  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  TlsПротокол  |  Протокол клиента TLS  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Общее количество запросов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Количество неработоспособных узлов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  ПриложениеРулехит  |  Правила применения хит кол  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  ДанныеОбработанные данные  |  Обработанные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  БрандмауэрЗдоровье  |  Состояние здоровья брандмауэра  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Сетевые правила хит кол  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Использование порта SNAT  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/dnszones  |  QueryVolume  |  Объем запросов  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Использование емкости, доступной для наборов записей  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/dnszones  |  RecordSetCount  |  Количество наборов записей  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Доступность Arp  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits  |  BgpДоступность  |  Доступность Bgp  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInВторая  |  GlobalReachBitsInВторая  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  КросикаплиInPerSecond  |  DroppedInBitsPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteCircuits  |  КросидБитсOutPerSecond  |  ВыпавшийOutBitsPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| нет  | нет |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  Админгосударство  |  Админгосударство  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  Линейныйпротокол  |  Линейныйпротокол  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Работоспособность серверного компонента (в процентах)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Число запросов к серверному компоненту  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Backend Request Latency (Задержка запросов к серверным частям)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Размер оплачиваемого ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestCount  |  Число запросов  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestSize  |  Размер запроса  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Размер ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Total Latency (Общая задержка)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  ByteCount  |  Количество байтов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Health Probe Status (Состояние пробы работоспособности)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  PacketCount  |  Количество пакетов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Количество подключений SNAT  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  SYNCount  |  Количество пакетов SYN  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Отправлено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Получено пакетов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Отправлено пакетов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Avg. Время в оба конца (ms)  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ЧекиНейдПроцент  |  Проверки Не удалось Процент (Предварительный просмотр)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Доля проб с ошибками, в процентах  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTriptimeMs  |  Время в оба конца (ms) (Предварительный просмотр)  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Количество байтов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Удаленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Перенаправленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Входящие пакеты SYN для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Входящие пакеты TCP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Входящие пакеты UDP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Выполняется ли атака DDoS  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Количество пакетов  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Удаленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Перенаправленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Количество пакетов SYN  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Удаленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Перенаправленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Удаленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Удаленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Перенаправленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Удаленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Проверка состояния конечной точки с помощью конечной точки  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Запросы, выполняемые возвращаемой конечной точкой  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway S2S Bandwidth (Пропускная способность шлюза S2S)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S Bandwidth (Пропускная способность шлюза P2S)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S Connection Count (Количество подключений P2S)  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Пропускная способность туннеля  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Исходящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Удаленные входящие пакеты туннеля (несоответствие селектора трафика)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Исходящие пакеты туннеля  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Входящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Входящие пакеты туннеля  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Время в пути для Pings к VM  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Неудачные пинги для VM  |  Процент  |  Среднее | 
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
| **Да**  | нет |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Ошибки из-за поврежденного или просроченного канала  |  Count  |  Итог | 
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
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  Процент доступной памяти  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  Процент доступной области подкачки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  Использование выделенной памяти (в байтах), %  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  Процент времени DPC  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  Процент свободных индексных дескрипторов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  Процент времени простоя  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  Процент времени прерывания  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  Процент времени ожидания ввода-вывода  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  Процент времени работы с низким приоритетом  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  Процент времени работы в привилегированном режиме  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  Процент используемых индексных дескрипторов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  Процент используемой памяти  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  Процент используемого места  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  Процент используемой области подкачки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  Процент времени пользователя  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Доступный объем в МБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Доступный объем памяти в МБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Доступный объем подкачки в МБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Avg. Диск сек/ Читать  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Avg. Диск сек/ Читать  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время обращения к диску (с)  |  Avg. Диск сек/Передача  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Avg. Диск сек/ Запись  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Avg. Диск сек/ Запись  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Полученных байтов/с  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Отправленных байтов/с  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Всего байтов/с  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Текущая длина очереди диска  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Скорость чтения с диска (байт/с)   |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду   |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду   |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |   Скорость записи на диск (байт/с)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Операций записи на диск в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Операций записи на диск в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Объем свободной физической памяти  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Объем свободного места в файлах подкачки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Объем свободной виртуальной памяти  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Скорость обмена с логическим диском (байт/с)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Операций чтения со страницы в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Операций записи на страницу в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Страниц в секунду  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Процент времени работы в привилегированном режиме  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Процент времени пользователя  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Скорость обмена с физическим диском (байт/с)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Процессы  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  Длина очереди процессора  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Объем, сохраненный в файлах подкачки  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Всего байт  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Всего получено байт  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Всего передано байт  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Всего конфликтов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Всего получено пакетов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Всего передано пакетов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Всего ошибок Rx  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Всего ошибок Tx  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Время доступности  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Используемая области подкачки в МБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Используемая память в КБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Используемый объем памяти в МБ  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Пользователи  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Виртуальная общая память  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Событие  |  Событие  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.OperationalInsights/workspaces  |  Пульс  |  Пульс  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Update  |  Update  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Обмолота памяти (Данные)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Высокая загрузка QPU  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Длительность запроса (Данные)  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Длина очереди вакансий в пуле запроса (наборы данных)  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  SearchLatency  |  Search Latency  |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Search queries per second  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Throttled search queries percentage  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Число активных сообщений в очереди или разделе.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  Процессор (Обезображенный)  |  Процент  |  Максимальная | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  DeadletteredСообщения  |  Подсчет мертвых букв сообщений в очереди / Тема.  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Входящих запросов  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  Сообщения  |  Число сообщений в очереди или разделе.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  ЦП  |  Процент  |  Максимальная | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Использование памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ЗапланированныеСообщения  |  Подсчет запланированных сообщений в очереди/теме.  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  Размер  |  Размер  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Count  |  Итог | 
| нет  | нет |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Использование памяти (Обезвоболенно)  |  Процент  |  Максимальная | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ActualCpu  |  ActualCpu  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  Фактическаяпамять  |  Фактическаяпамять  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ВыделеноCpu  |  ВыделеноCpu  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ВыделеннаяПамять  |  ВыделеннаяПамять  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ПриложениеСтатус  |  ПриложениеСтатус  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  КонтейнерСтатус  |  КонтейнерСтатус  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  CpuUtilization  |  CpuUtilization  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ПамятьУтилизация  |  ПамятьУтилизация  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  ПерезапускCount  |  ПерезапускCount  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  СервисРепликАстампром  |  СервисРепликАстампром  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.ServiceFabricMesh/приложения  |  СервисСтатус  |  СервисСтатус  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Число подключений  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Inbound Traffic (Входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Количество сообщений  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Outbound Traffic (Исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Системные ошибки  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Ошибки пользователей  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Average CPU percentage (Средний процент использования ЦП)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Количество считанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Количество записанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_requests  |  IO requests count (Количество запросов ввода-вывода)  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Зарезервированное дисковое пространство  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Использованное дисковое пространство  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Virtual core count (Число виртуальных ядер)  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers  |  database_storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_used  |  DTU used  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers  |  storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  App Процессор выставлен счет  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Процент процессора приложения  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Процент памяти приложения  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Заблокировано брандмауэром  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Cache hit percentage (Процент попаданий в кэш)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Cache used percentage (Процент использования кэша)  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Неудачные подключения  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Успешные подключения  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Лимит процессора  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Используемый процессор  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  взаимоблокировка  |  Взаимоблокировки  |  Count  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Лимит DTU  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU used  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU percentage  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Лимит DWU  |  Count  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU used  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Local tempdb percentage (Процент использования локальной базы данных tempdb)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Процент памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  носителей.  |  Место, занятое данными  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  storage_percent  |  Используемое пространство данных  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Темпб данных файл размер килобайт  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb журнал файл размер килобайт  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Tempdb Процент Журнала Используется  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Пространство данных, выделенное в процентах  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Лимит процессора  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Используемый процессор  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Лимит процессора  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Используемый процессор  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU used  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU limit  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU used  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Максимальный размер данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Используемое пространство данных  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Темпб данных файл размер килобайт  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb журнал файл размер килобайт  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Tempdb Процент Журнала Используется  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| нет  | нет |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareКвота  |  Размер квоты доли файла  |  Байты  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Количество моментальных снимков файла  |  Count  |  Среднее | 
| нет  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Размер снимка файла общего обмена  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Транзакции  |  Транзакции  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  Клиентиопс  |  Всего клиентов IOPS  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  КлиентскаяЛапланция  |  Средняя задержка клиента  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  КлиентЛокИОСПС  |  Клиентская блокировка IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientMetadataReadIOPS  |  Метаданные клиента Читать IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientMetadataWriteIOPS  |  Метаданные клиента пишут IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientReadIOPS  |  Клиент Читать IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientReadThroughput  |  Средняя пропускная готовность кэша  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientWriteIOPS  |  Клиент Написать IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ClientWriteThroughput  |  Средняя пропускная запись кэша  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetAsyncWriteThroughput  |  ХранениеTarget Асинхронная Пропускная запись  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetFillThroughput  |  StorageTarget Заполнить пропускную емкость  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеЦелевоеЗдоровье  |  Целевое здоровье хранилища  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetIOPS  |  Всего храненияЦелевой IOPS  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetLatency  |  Задержка хранилищаTarget  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetMetadataReadIOPS  |  ХранениеТархик Метаданные Читать IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetMetadataWriteIOPS  |  Метаданные StorageTarget пишут IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetReadAheadThroughput  |  ХранениеTarget Читать впереди пропускной  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetReadIOPS  |  ХранениеЦелевой читать IOPS  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  StorageTargetSyncWriteThroughput  |  StorageTarget Синхронная пропускная запись  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetTotalReadPutPut  |  StorageTarget Всего Читать Пропускную часть  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetTotalWriteThroughput  |  StorageTarget Всего Написать Пропускная часть  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  ХранениеTargetWriteIOPS  |  ХранениеЦелевой Написать IOPS  |  Count  |  Среднее | 
| **Да**  | нет |  Microsoft.StorageCache/кэши  |  Время доступности  |  Время доступности  |  Count  |  Среднее | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Результат сеанса синхронизации  |  Count  |  Среднее | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  ХранениеSyncbatchTransferredBy  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBy-application  |  Размер облачного уровня отзыва по приложению  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  StorageRecalledRecalledTotalNetworkBytes  |  Размер облачного уровня отзыва  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  ХранениеSyncRecallIOTotalSizeBytes  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  ХранениеSyncrecallThroughputputPerperSecond  |  Пропускная пропускная часть облачного уровня отзыва  |  Байт/с  |  Среднее | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Состояние сервера онлайн  |  Count  |  Максимальная | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Синхронизация файлов  |  Count  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionItemОшибкиСчет  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/registeredServers  |  СерверHeartbeat  |  Состояние сервера онлайн  |  Count  |  Максимальная | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncgroupBatchTransferredBytes  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Синхронизация файлов  |  Count  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionItemОшибкИСчет  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  СерверEndpointBatchTransferredBytes  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSessionSessionAppliedFilesCount  |  Синхронизация файлов  |  Count  |  Итог | 
| **Да**  | нет |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSessionSessionItemОшибки  |  Несинхронизирующиеся файлы  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Неудачные запросы функций  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  События функций  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Запросы функций  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Ошибки преобразования данных  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Ошибки десериализации входа  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Неупорядоченные события  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Ранние входные события  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ошибки  |  Ошибки среды выполнения  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Байты входного события  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Входные события  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ВхотозентыИсточникиНазад  |  Необработанные входные события  |  Count  |  Максимальная | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ВводСобытийИсточникИВТорых  |  Полученные входные источники  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  События позднего ввода  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Выходные события  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Предельная задержка  |  Секунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Использование единиц потоковой передачи (%)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Задержка чтения с диска  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Операции чтения дисков  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Задержка записи на диск  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteОперации  |  Операции записи диска  |  Count  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Память Активная  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  ПамятьПредоставленная  |  Память предоставлена  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Используемая память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Сеть в байтах/Секе  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Сеть out Байты/Sec  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.VMwareCloudSimple/virtualMachines  |  ПроцентCpuReady  |  Процентный cPU Готов  |  Миллисекунды  |  Итог | 
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
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP Закрыть Ожидание  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Закрытие TCP  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpУстановлено  |  TCP Создан  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Фин Ожидание 1  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Фин Ожидание 2  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP Последний Ack  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP Syn Получено  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP Син отправлено  |  Count  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  TCP Время Ожидание  |  Count  |  Среднее | 
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
