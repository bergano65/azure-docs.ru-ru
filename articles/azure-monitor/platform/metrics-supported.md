---
title: Поддерживаемые метрики Azure Monitor, доступные для каждого типа ресурса
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484225"
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor

Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor. Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Чтобы запросить метрики с измерениями и получить к ним доступ, воспользуйтесь версией [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Количество|Среднее|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Среднее|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|private_bytes_metric|Байты исключительного пользования |Байты|Среднее|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Байт виртуальной памяти |Байты|Среднее|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Количество|Среднее|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Среднее|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Количество|Среднее|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Количество|Среднее|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Количество|Среднее|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Количество|Среднее|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Количество|Среднее|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: текущие подключения|Количество|Среднее|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Количество|Среднее|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: использование памяти|Байты|Среднее|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Среднее|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Среднее|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Среднее|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Среднее|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: квота|Байты|Среднее|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Количество|Среднее|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Среднее|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Среднее|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Среднее|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Количество|Среднее|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Количество|Среднее|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Количество|Среднее|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Количество|Среднее|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Количество|Среднее|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Количество|Среднее|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Количество|Среднее|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Количество|Среднее|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Количество|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Количество|Среднее|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Количество|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Количество|Среднее|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Количество|Среднее|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Количество|Среднее|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Количество|Среднее|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Количество|Среднее|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Среднее|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Байты|Среднее|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Байты|Среднее|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Requests (Запросы)|Requests (Запросы)|Количество|Итого|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Количество|Итого|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Количество|Итого|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Количество|Итого| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Количество|Итого|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Другие запросы к шлюзу|Количество|Итого|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Среднее|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Ориентированное на объем|Ориентированное на объем|Процент|Среднее|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Location|
|EventHubTotalEvents|Total EventHub Events|Количество|Итого|The total number of events sent to EventHub from API Management in a given period.|Location|
|EventHubSuccessfulEvents|Successful EventHub Events|Количество|Итого|The total number of successful EventHub events in a given period.|Location|
|EventHubTotalFailedEvents|Failed EventHub Events|Количество|Итого|The total number of failed EventHub events in a given period.|Location|
|EventHubRejectedEvents|Rejected EventHub Events|Количество|Итого|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Location|
|EventHubThrottledEvents|Throttled EventHub Events|Количество|Итого|The total number of throttled EventHub events in a given period.|Location|
|EventHubTimedoutEvents|Timed Out EventHub Events|Количество|Итого|The total number of timed out EventHub events in a given period.|Location|
|EventHubDroppedEvents|Dropped EventHub Events|Количество|Итого|The total number of events skipped because of queue size limit reached in a given period.|Location|
|EventHubTotalBytesSent|Size of EventHub Events|Байты|Итого|The total size of EventHub events in bytes in a given period.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Количество|Итого|Общее количество заданий|Состояние модуля Runbook|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Количество|Итого|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Количество|Итого|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Количество|Итого|Общее число выделенных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalNodeCount|Dedicated Node Count|Количество|Итого|Общее число выделенных узлов в учетной записи пакетной службы.|Нет измерений|
|LowPriorityCoreCount|LowPriority Core Count|Количество|Итого|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Количество|Итого|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Нет измерений|
|CreatingNodeCount|Creating Node Count|Количество|Итого|Количество создаваемых узлов.|Нет измерений|
|StartingNodeCount|Starting Node Count|Количество|Итого|Число узлов, которые запускаются.|Нет измерений|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Количество|Итого|Число узлов, ожидающих завершения задачи запуска.|Нет измерений|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Количество|Итого|Число узлов, на которых произошел сбой задачи запуска.|Нет измерений|
|IdleNodeCount|Idle Node Count|Количество|Итого|Количество узлов в неактивном состоянии.|Нет измерений|
|OfflineNodeCount|Offline Node Count|Количество|Итого|Количество узлов не в сети.|Нет измерений|
|RebootingNodeCount|Rebooting Node Count|Количество|Итого|Количество перезапускаемых узлов.|Нет измерений|
|ReimagingNodeCount|Reimaging Node Count|Количество|Итого|Число узлов, для которых пересоздается образ.|Нет измерений|
|RunningNodeCount|Running Node Count|Количество|Итого|Число работающих узлов.|Нет измерений|
|LeavingPoolNodeCount|Leaving Pool Node Count|Количество|Итого|Количество узлов, покидающих пул.|Нет измерений|
|UnusableNodeCount|Unusable Node Count|Количество|Итого|Число узлов, непригодных для использования.|Нет измерений|
|PreemptedNodeCount|Preempted Node Count|Количество|Итого|Количество замещенных узлов.|Нет измерений|
|TaskStartEvent|Task Start Events|Количество|Итого|Общее число запущенных задач.|Нет измерений|
|TaskCompleteEvent|Task Complete Events|Количество|Итого|Общее число завершенных задач.|Нет измерений|
|TaskFailEvent|Task Fail Events|Количество|Итого|Общее число задач, завершившихся сбоем.|Нет измерений|
|PoolCreateEvent|Pool Create Events|Количество|Итого|Общее число созданных пулов.|Нет измерений|
|PoolResizeStartEvent|Pool Resize Start Events|Количество|Итого|Общее число запущенных задач изменения размера пула.|Нет измерений|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Количество|Итого|Общее число завершенных задач изменения размера пула.|Нет измерений|
|PoolDeleteStartEvent|Pool Delete Start Events|Количество|Итого|Общее число запущенных задач удаления пула.|Нет измерений|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Количество|Итого|Общее число завершенных задач удаления пула.|Нет измерений|
|JobDeleteCompleteEvent|Job Delete Complete Events (События окончания удаления задания)|Количество|Итого|Общее количество заданий, которые были успешно удалены.|Нет измерений|
|JobDeleteStartEvent|Job Delete Start Events (События начала удаления задания)|Количество|Итого|Общее количество заданий, для которых запрошено удаление.|Нет измерений|
|JobDisableCompleteEvent|Job Disable Complete Events (События окончания отключения задания)|Количество|Итого|Общее количество заданий, которые были успешно отключены.|Нет измерений|
|JobDisableStartEvent|Job Disable Start Events (События начала отключения задания)|Количество|Итого|Общее количество заданий, для которых запрошено отключение.|Нет измерений|
|JobStartEvent|Job Start Events (События запуска задания)|Количество|Итого|Общее количество заданий, которые были успешно запущены.|Нет измерений|
|JobTerminateCompleteEvent|Job Terminate Complete Events (События окончания завершения задания)|Количество|Итого|Общее количество заданий, которые были успешно завершены.|Нет измерений|
|JobTerminateStartEvent|Job Terminate Start Events (События начала завершения задания)|Количество|Итого|Общее количество заданий, для которых запрошено завершение.|Нет измерений|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Количество|Максимальная||ShardId|
|totalcommandsprocessed|Всего операций|Количество|Итого||ShardId|
|cachehits|Попаданий в кэш|Количество|Итого||ShardId|
|cachemisses|Промахов в кэше|Количество|Итого||ShardId|
|getcommands|Операций считывания|Количество|Итого||ShardId|
|setcommands|Операций записи|Количество|Итого||ShardId|
|operationsPerSecond|Количество операций в секунду|Количество|Максимальная||ShardId|
|evictedkeys|Исключенные ключи|Количество|Итого||ShardId|
|totalkeys|Всего ключей|Количество|Максимальная||ShardId|
|expiredkeys|Ключи с истекшим сроком действия|Количество|Итого||ShardId|
|usedmemory|Используемая память|Байты|Максимальная||ShardId|
|usedmemorypercentage|Процент используемой памяти|Процент|Максимальная||ShardId|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||ShardId|
|serverLoad|Загрузка сервера|Процент|Максимальная||ShardId|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||ShardId|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||ShardId|
|percentProcessorTime|ЦП|Процент|Максимальная||ShardId|
|cacheLatency|Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))|Количество|Среднее||ShardId, SampleType|
|errors|Errors|Количество|Максимальная||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed0|Total Operations (Shard 0)|Количество|Итого||Нет измерений|
|cachehits0|Cache Hits (Shard 0)|Количество|Итого||Нет измерений|
|cachemisses0|Cache Misses (Shard 0)|Количество|Итого||Нет измерений|
|getcommands0|Gets (Shard 0)|Количество|Итого||Нет измерений|
|setcommands0|Sets (Shard 0)|Количество|Итого||Нет измерений|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Количество|Максимальная||Нет измерений|
|evictedkeys0|Evicted Keys (Shard 0)|Количество|Итого||Нет измерений|
|totalkeys0|Total Keys (Shard 0)|Количество|Максимальная||Нет измерений|
|expiredkeys0|Expired Keys (Shard 0)|Количество|Итого||Нет измерений|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||Нет измерений|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||Нет измерений|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||Нет измерений|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||Нет измерений|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||Нет измерений|
|connectedclients1|Connected Clients (Shard 1)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed1|Total Operations (Shard 1)|Количество|Итого||Нет измерений|
|cachehits1|Cache Hits (Shard 1)|Количество|Итого||Нет измерений|
|cachemisses1|Cache Misses (Shard 1)|Количество|Итого||Нет измерений|
|getcommands1|Gets (Shard 1)|Количество|Итого||Нет измерений|
|getcommands1|Sets (Shard 1)|Количество|Итого||Нет измерений|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Количество|Максимальная||Нет измерений|
|evictedkeys1|Evicted Keys (Shard 1)|Количество|Итого||Нет измерений|
|totalkeys1|Total Keys (Shard 1)|Количество|Максимальная||Нет измерений|
|expiredkeys1|Expired Keys (Shard 1)|Количество|Итого||Нет измерений|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||Нет измерений|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||Нет измерений|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||Нет измерений|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||Нет измерений|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||Нет измерений|
|connectedclients2|Connected Clients (Shard 2)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed2|Total Operations (Shard 2)|Количество|Итого||Нет измерений|
|cachehits2|Cache Hits (Shard 2)|Количество|Итого||Нет измерений|
|cachemisses2|Cache Misses (Shard 2)|Количество|Итого||Нет измерений|
|getcommands2|Gets (Shard 2)|Количество|Итого||Нет измерений|
|getcommands2|Sets (Shard 2)|Количество|Итого||Нет измерений|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Количество|Максимальная||Нет измерений|
|evictedkeys2|Evicted Keys (Shard 2)|Количество|Итого||Нет измерений|
|totalkeys2|Total Keys (Shard 2)|Количество|Максимальная||Нет измерений|
|expiredkeys2|Expired Keys (Shard 2)|Количество|Итого||Нет измерений|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||Нет измерений|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||Нет измерений|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||Нет измерений|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||Нет измерений|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||Нет измерений|
|connectedclients3|Connected Clients (Shard 3)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed3|Total Operations (Shard 3)|Количество|Итого||Нет измерений|
|cachehits3|Cache Hits (Shard 3)|Количество|Итого||Нет измерений|
|cachemisses3|Cache Misses (Shard 3)|Количество|Итого||Нет измерений|
|getcommands3|Gets (Shard 3)|Количество|Итого||Нет измерений|
|setcommands3|Sets (Shard 3)|Количество|Итого||Нет измерений|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Количество|Максимальная||Нет измерений|
|evictedkeys3|Evicted Keys (Shard 3)|Количество|Итого||Нет измерений|
|totalkeys3|Total Keys (Shard 3)|Количество|Максимальная||Нет измерений|
|expiredkeys3|Expired Keys (Shard 3)|Количество|Итого||Нет измерений|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||Нет измерений|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||Нет измерений|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||Нет измерений|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||Нет измерений|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||Нет измерений|
|connectedclients4|Connected Clients (Shard 4)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed4|Total Operations (Shard 4)|Количество|Итого||Нет измерений|
|cachehits4|Cache Hits (Shard 4)|Количество|Итого||Нет измерений|
|cachemisses4|Cache Misses (Shard 4)|Количество|Итого||Нет измерений|
|getcommands4|Gets (Shard 4)|Количество|Итого||Нет измерений|
|setcommands4|Sets (Shard 4)|Количество|Итого||Нет измерений|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Количество|Максимальная||Нет измерений|
|evictedkeys4|Evicted Keys (Shard 4)|Количество|Итого||Нет измерений|
|totalkeys4|Total Keys (Shard 4)|Количество|Максимальная||Нет измерений|
|expiredkeys4|Expired Keys (Shard 4)|Количество|Итого||Нет измерений|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||Нет измерений|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||Нет измерений|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||Нет измерений|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||Нет измерений|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||Нет измерений|
|connectedclients5|Connected Clients (Shard 5)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed5|Total Operations (Shard 5)|Количество|Итого||Нет измерений|
|cachehits5|Cache Hits (Shard 5)|Количество|Итого||Нет измерений|
|cachemisses5|Cache Misses (Shard 5)|Количество|Итого||Нет измерений|
|getcommands5|Gets (Shard 5)|Количество|Итого||Нет измерений|
|getcommands5|Sets (Shard 5)|Количество|Итого||Нет измерений|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Количество|Максимальная||Нет измерений|
|evictedkeys5|Evicted Keys (Shard 5)|Количество|Итого||Нет измерений|
|totalkeys5|Total Keys (Shard 5)|Количество|Максимальная||Нет измерений|
|expiredkeys5|Expired Keys (Shard 5)|Количество|Итого||Нет измерений|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||Нет измерений|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||Нет измерений|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||Нет измерений|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||Нет измерений|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||Нет измерений|
|connectedclients6|Connected Clients (Shard 6)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed6|Total Operations (Shard 6)|Количество|Итого||Нет измерений|
|cachehits6|Cache Hits (Shard 6)|Количество|Итого||Нет измерений|
|cachemisses6|Cache Misses (Shard 6)|Количество|Итого||Нет измерений|
|getcommands6|Gets (Shard 6)|Количество|Итого||Нет измерений|
|setcommands6|Sets (Shard 6)|Количество|Итого||Нет измерений|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Количество|Максимальная||Нет измерений|
|evictedkeys6|Evicted Keys (Shard 6)|Количество|Итого||Нет измерений|
|totalkeys6|Total Keys (Shard 6)|Количество|Максимальная||Нет измерений|
|expiredkeys6|Expired Keys (Shard 6)|Количество|Итого||Нет измерений|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||Нет измерений|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||Нет измерений|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||Нет измерений|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||Нет измерений|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||Нет измерений|
|connectedclients7|Connected Clients (Shard 7)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed7|Total Operations (Shard 7)|Количество|Итого||Нет измерений|
|cachehits7|Cache Hits (Shard 7)|Количество|Итого||Нет измерений|
|cachemisses7|Cache Misses (Shard 7)|Количество|Итого||Нет измерений|
|getcommands7|Gets (Shard 7)|Количество|Итого||Нет измерений|
|setcommands7|Sets (Shard 7)|Количество|Итого||Нет измерений|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Количество|Максимальная||Нет измерений|
|evictedkeys7|Evicted Keys (Shard 7)|Количество|Итого||Нет измерений|
|totalkeys7|Total Keys (Shard 7)|Количество|Максимальная||Нет измерений|
|expiredkeys7|Expired Keys (Shard 7)|Количество|Итого||Нет измерений|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||Нет измерений|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||Нет измерений|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||Нет измерений|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||Нет измерений|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||Нет измерений|
|connectedclients8|Connected Clients (Shard 8)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed8|Total Operations (Shard 8)|Количество|Итого||Нет измерений|
|cachehits8|Cache Hits (Shard 8)|Количество|Итого||Нет измерений|
|cachemisses8|Cache Misses (Shard 8)|Количество|Итого||Нет измерений|
|getcommands8|Gets (Shard 8)|Количество|Итого||Нет измерений|
|setcommands8|Sets (Shard 8)|Количество|Итого||Нет измерений|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Количество|Максимальная||Нет измерений|
|evictedkeys8|Evicted Keys (Shard 8)|Количество|Итого||Нет измерений|
|totalkeys8|Total Keys (Shard 8)|Количество|Максимальная||Нет измерений|
|expiredkeys8|Expired Keys (Shard 8)|Количество|Итого||Нет измерений|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||Нет измерений|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||Нет измерений|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||Нет измерений|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||Нет измерений|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||Нет измерений|
|connectedclients9|Connected Clients (Shard 9)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed9|Total Operations (Shard 9)|Количество|Итого||Нет измерений|
|cachehits9|Cache Hits (Shard 9)|Количество|Итого||Нет измерений|
|cachemisses9|Cache Misses (Shard 9)|Количество|Итого||Нет измерений|
|getcommands9|Gets (Shard 9)|Количество|Итого||Нет измерений|
|setcommands9|Sets (Shard 9)|Количество|Итого||Нет измерений|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Количество|Максимальная||Нет измерений|
|evictedkeys9|Evicted Keys (Shard 9)|Количество|Итого||Нет измерений|
|totalkeys9|Total Keys (Shard 9)|Количество|Максимальная||Нет измерений|
|expiredkeys9|Expired Keys (Shard 9)|Количество|Итого||Нет измерений|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||Нет измерений|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||Нет измерений|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||Нет измерений|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||Нет измерений|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||Нет измерений|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Чтение с дисков (операций/с)|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Запись на диски (операций/с)|Запись на диски (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Чтение с дисков (операций/с)|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Запись на диски (операций/с)|Запись на диски (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Количество|Итого|Общее число вызовов.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Количество|Итого|Число успешных вызовов.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Количество|Итого|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Количество|Итого|Число вызовов, превысивших ограничение скорости или квоты.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Количество|Итого|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Количество|Итого|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Входящие данные:|Байты|Итого|Размер входящих данных в байтах.|ApiName, OperationName, Region|
|DataOut|Выходные данные|Байты|Итого|Размер исходящих данных в байтах.|ApiName, OperationName, Region|
|Задержка|Задержка|MilliSeconds|Среднее|Время задержки в миллисекундах.|ApiName, OperationName, Region|
|CharactersTranslated|Преобразованные символы|Количество|Итого|Общее количество символов во входящем текстовом запросе.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Количество|Итого|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Длительность речи|Секунды|Итого|Общая длительность речи в секундах.|ApiName, OperationName, Region|
|TotalTransactions|Всего транзакций|Количество|Итого|Общее число транзакций.|Нет измерений|
|TotalTokenCalls|Всего вызовов токенов|Количество|Итого|Общее число вызовов токенов.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Network In Billable|Байты|Итого|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|Нет измерений|
|Сеть (исходящий трафик)|Network Out Billable|Байты|Итого|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Bytes read from disk during monitoring period|Нет измерений|
|Скорость записи на диск|Скорость записи на диск|Байты|Итого|Bytes written to disk during monitoring period|Нет измерений|
|Чтение с дисков (операций/с)|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Запись на диски (операций/с)|Запись на диски (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Количество|Среднее|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Количество|Среднее|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Data Disk Read Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Data Disk Write Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Data Disk Read Operations/Sec (Deprecated)|Число/с|Среднее|Read IOPS from a single disk during monitoring period|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Data Disk Write Operations/Sec (Deprecated)|Число/с|Среднее|Write IOPS from a single disk during monitoring period|ИД слота|
|Длина очереди в расчете на диск|Data Disk QD (Deprecated)|Количество|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|OS Disk Read Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period for OS disk|Нет измерений|
|Скорость записи на диск ОС (байт/с)|OS Disk Write Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period for OS disk|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|OS Disk Read Operations/Sec (Deprecated)|Число/с|Среднее|Read IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|Скорость записи на диск ОС (операций/с)|OS Disk Write Operations/Sec (Deprecated)|Число/с|Среднее|Write IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|Длина очереди на диск ОС|OS Disk QD (Deprecated)|Количество|Среднее|Длина очереди дисков ОС|Нет измерений|
|Data Disk Read Bytes/sec|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Количество|Среднее|Длина очереди дисков данных|LUN|
|OS Disk Read Bytes/sec|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period for OS disk|Нет измерений|
|OS Disk Write Bytes/sec|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period for OS disk|Нет измерений|
|OS Disk Read Operations/Sec|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Read IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|OS Disk Write Operations/Sec|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Write IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Количество|Среднее|Длина очереди дисков ОС|Нет измерений|
|Inbound Flows|Inbound Flows (Preview)|Количество|Среднее|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|Нет измерений|
|Outbound Flows|Outbound Flows (Preview)|Количество|Среднее|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|Нет измерений|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|Число/с|Среднее|The maximum creation rate of inbound flows (traffic going into the VM)|Нет измерений|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|Число/с|Среднее|The maximum creation rate of outbound flows (traffic going out of the VM)|Нет измерений|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Процент|Среднее|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Процент|Среднее|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Процент|Среднее|Premium OS Disk Cache Read Hit|Нет измерений|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Процент|Среднее|Premium OS Disk Cache Read Miss|Нет измерений|
|Network In Total|Network In Total|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Network Out Total|Network Out Total|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Сеть (входящий трафик)|Network In Billable|Байты|Итого|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Сеть (исходящий трафик)|Network Out Billable|Байты|Итого|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Bytes read from disk during monitoring period|VMName|
|Скорость записи на диск|Скорость записи на диск|Байты|Итого|Bytes written to disk during monitoring period|VMName|
|Чтение с дисков (операций/с)|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|VMName|
|Запись на диски (операций/с)|Запись на диски (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Количество|Среднее|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Количество|Среднее|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Data Disk Read Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Data Disk Write Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Data Disk Read Operations/Sec (Deprecated)|Число/с|Среднее|Read IOPS from a single disk during monitoring period|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Data Disk Write Operations/Sec (Deprecated)|Число/с|Среднее|Write IOPS from a single disk during monitoring period|ИД слота|
|Длина очереди в расчете на диск|Data Disk QD (Deprecated)|Количество|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|OS Disk Read Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period for OS disk|Нет измерений|
|Скорость записи на диск ОС (байт/с)|OS Disk Write Bytes/Sec (Deprecated)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period for OS disk|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|OS Disk Read Operations/Sec (Deprecated)|Число/с|Среднее|Read IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|Скорость записи на диск ОС (операций/с)|OS Disk Write Operations/Sec (Deprecated)|Число/с|Среднее|Write IOPS from a single disk during monitoring period for OS disk|Нет измерений|
|Длина очереди на диск ОС|OS Disk QD (Deprecated)|Количество|Среднее|Длина очереди дисков ОС|Нет измерений|
|Data Disk Read Bytes/sec|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Количество|Среднее|Длина очереди дисков данных|LUN, VMName|
|OS Disk Read Bytes/sec|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Количество|Среднее|Длина очереди дисков ОС|VMName|
|Inbound Flows|Inbound Flows (Preview)|Количество|Среднее|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Количество|Среднее|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|Число/с|Среднее|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|Число/с|Среднее|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Процент|Среднее|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Процент|Среднее|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Процент|Среднее|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Процент|Среднее|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Network Out Total|Network Out Total|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Количество|Среднее|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Среднее|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байты|Среднее|Количество байт, полученных по сети в секунду.|Нет измерений|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байты|Среднее|Количество байт, переданных по сети в секунду.|Нет измерений|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Количество|Среднее|Number of image pulls in total|Нет измерений|
|SuccessfulPullCount|Successful Pull Count|Количество|Среднее|Number of successful image pulls|Нет измерений|
|TotalPushCount|Total Push Count|Количество|Среднее|Number of image pushes in total|Нет измерений|
|SuccessfulPushCount|Successful Push Count|Количество|Среднее|Number of successful image pushes|Нет измерений|
|RunDuration|Run Duration|Миллисекунды|Итого|Run Duration in milliseconds|Нет измерений|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Количество|Итого|Общее количество доступных ядер ЦП в управляемом кластере|Нет измерений|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байты|Итого|Общий объем доступной памяти в управляемом кластере|Нет измерений|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Количество|Итого|Число модулей pod в состоянии готовности|пространство имен, pod|
|kube_node_status_condition|Состояния для различных условий узла|Количество|Итого|Состояния для различных условий узла|condition, status, status2, node|
|kube_pod_status_phase|Число модулей pod по фазам|Количество|Итого|Число модулей pod по фазам|фаза, пространство имен, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Количество|Итого||Нет измерений|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Итого||Нет измерений|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Количество|Итого||Нет измерений|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Количество|Итого||Нет измерений|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Итого||Нет измерений|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Количество|Итого||Нет измерений|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Количество|Итого||Нет измерений|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Секунды|Итого||Нет измерений|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Секунды|Итого||Нет измерений|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Секунды|Итого||Нет измерений|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Секунды|Итого||Нет измерений|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Секунды|Итого||Нет измерений|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Секунды|Итого||Нет измерений|
|ImportASAValuesFailed|Import ASA Values Failed Count|Количество|Итого||Нет измерений|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Количество|Итого||Нет измерений|
|DCIProfilesCount|Количество экземпляров профиля|Количество|Последний||Нет измерений|
|DCIInteractionsPerMonthCount|Количество взаимодействий в месяц|Количество|Последний||Нет измерений|
|DCIKpisCount|Количество ключевых показателей эффективности|Количество|Последний||Нет измерений|
|DCISegmentsCount|Количество сегментов|Количество|Последний||Нет измерений|
|DCIPredictiveMatchPoliciesCount|Количество операций прогнозного сопоставления|Количество|Последний||Нет измерений|
|DCIPredictionsCount|Количество прогнозов|Количество|Последний||Нет измерений|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|Байт/с|Среднее|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|Байт/с|Среднее|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|Байт/с|Среднее|The download throughput to Azure from a share during the reporting period.|Распространение|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|Байт/с|Среднее|The upload throughput to Azure from a share during the reporting period.|Распространение|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Байты|Среднее|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Распространение|
|TotalCapacity|Total Capacity|Байты|Среднее|Total Capacity|Нет измерений|
|AvailableCapacity|Available Capacity|Байты|Среднее|The available capacity in bytes during the reporting period.|Нет измерений|
|CloudUploadThroughput|Cloud Upload Throughput|Байт/с|Среднее|The cloud upload throughput  to Azure during the reporting period.|Нет измерений|
|CloudReadThroughput|Cloud Download Throughput|Байт/с|Среднее|The cloud download throughput to Azure during the reporting period.|Нет измерений|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Байты|Среднее|The total number of bytes that is uploaded to Azure from a device during the reporting period.|Нет измерений|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Процент|Среднее|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Процент|Среднее|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Количество|Итого||pipelineName, activityName|
|SuccessfulRuns.|успешные циклы выполнения.|Количество|Итого||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Количество|Итого||FailureType, Name|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Количество|Итого||FailureType, Name|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Количество|Итого||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Количество|Итого||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Количество|Итого||Name, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Количество|Итого||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Процент|Среднее||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Среднее||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Количество|Максимальная||Нет измерений|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Количество|Максимальная||Нет измерений|
|ResourceCount|Total entities count|Количество|Максимальная||Нет измерений|
|FactorySizeInGbUnits|Total factory size (GB unit)|Количество|Максимальная||Нет измерений|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Количество|Итого|Количество успешно выполненных заданий.|Нет измерений|
|JobEndedFailure|Failed Jobs|Количество|Итого|Количество невыполненных заданий.|Нет измерений|
|JobEndedCancelled|Canceled Jobs|Количество|Итого|Count of canceled jobs.|Нет измерений|
|JobAUEndedSuccess|Successful AU Time|Секунды|Итого|Общее время AU успешно выполненных заданий.|Нет измерений|
|JobAUEndedFailure|Failed AU Time|Секунды|Итого|Общее время AU невыполненных заданий.|Нет измерений|
|JobAUEndedCancelled|Canceled AU Time|Секунды|Итого|Total AU time for canceled jobs.|Нет измерений|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|Нет измерений|
|DataWritten|Записанные данные|Байты|Итого|Общий объем данных, записанных в учетной записи.|Нет измерений|
|DataRead|Данных прочитано|Байты|Итого|Общий объем данных, прочитанных в учетной записи.|Нет измерений|
|WriteRequests|Запросы на запись|Количество|Итого|Количество запросов на запись данных в учетную запись.|Нет измерений|
|ReadRequests|Запросы на чтение|Количество|Итого|Количество запросов на чтение данных для учетной записи.|Нет измерений|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байты|Среднее|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байты|Среднее|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Среднее|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Количество|Среднее|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Количество|Итого|Неудачные подключения|Нет измерений|
|seconds_behind_master|Replication lag in seconds (Задержка репликации в секундах)|Количество|Среднее|Replication lag in seconds (Задержка репликации в секундах)|Нет измерений|
|backup_storage_used|Backup Storage used|Байты|Среднее|Backup Storage used|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байты|Среднее|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байты|Среднее|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Среднее|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Количество|Среднее|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Количество|Итого|Неудачные подключения|Нет измерений|
|seconds_behind_master|Replication lag in seconds (Задержка репликации в секундах)|Количество|Среднее|Replication lag in seconds (Задержка репликации в секундах)|Нет измерений|
|backup_storage_used|Backup Storage used|Байты|Среднее|Backup Storage used|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байты|Среднее|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байты|Среднее|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Среднее|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Количество|Среднее|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Количество|Итого|Неудачные подключения|Нет измерений|
|backup_storage_used|Backup Storage used|Байты|Среднее|Backup Storage used|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимальная|Replica lag in seconds|Нет измерений|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|Максимальная|Lag in bytes of the most lagging replica|Нет измерений|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Нет измерений|
|iops|IOPS|Количество|Среднее|IO Operations per second|Нет измерений|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байты|Среднее|Используемое хранилище|Нет измерений|
|active_connections|Активные подключения|Количество|Среднее|Активные подключения|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Количество|Итого|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Количество|Итого|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Количество|Итого|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Количество|Итого|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Количество|Итого|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Количество|Итого|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Количество|Итого|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Количество|Итого|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|Нет измерений|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Количество|Итого|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|Нет измерений|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Количество|Итого|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |Нет измерений|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Количество|Итого|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|Нет измерений|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|Нет измерений|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байты|Итого|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Количество|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|Нет измерений|
|EventGridDeliveries|Event Grid deliveries (preview)|Количество|Итого|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Количество|Итого|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Количество|Итого|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Количество|Итого|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Количество|Итого|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Количество|Итого|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Среднее|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Количество|Итого|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Количество|Итого|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Количество|Итого|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Количество|Итого|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Количество|Итого|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Количество|Итого|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Количество|Итого|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Количество|Итого|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Количество|Итого|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Количество|Итого|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Количество|Итого|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Количество|Итого|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Количество|Итого|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Количество|Среднее|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|
|deviceDataUsage|Total device data usage|Байты|Итого|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Нет измерений|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Количество|Среднее|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Количество|Среднее|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Количество|Итого|Метрики для операций конфигурации|Нет измерений|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Количество|Итого|Количество попыток регистрации устройств|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Назначенные устройства|Количество|Итого|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Попытки аттестации|Количество|Итого|Количество попыток аттестации устройств|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AvailableStorage|Доступная служба хранилища|Байты|Итого|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Отключение связи Cassandra|Количество|Итого|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Расходы запросов по Cassandra|Количество|Итого|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Запросы по Cassandra|Количество|Количество|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Использование данных|Байты|Итого|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Число документов|Количество|Итого|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Квота на документы|Байты|Итого|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Использование индексов|Байты|Итого|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Запросы метаданных|Количество|Количество|Количество запросов метаданных. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Запросить оплату Mongo|Количество|Итого|Использованные единицы запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Запросы Mongo|Количество|Количество|Количество выполненных запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Подготовленная пропускная способность|Количество|Максимальная|Подготовленная пропускная способность|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Среднее|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|SourceRegion, TargetRegion|
|ServiceAvailability|Доступность службы|Процент|Среднее|Account requests availability at one hour, day or month granularity|Нет измерений|
|TotalRequestUnits|Общее количество единиц запросов|Количество|Итого|Использованные единицы запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Общее количество запросов|Количество|Количество|Количество выполненных запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Количество|Итого|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|Publish Failed Events|Количество|Итого|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Количество|Итого|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|PublishSuccessLatencyInMs|Publish Success Latency|Количество|Итого|Publish success latency in milliseconds|Нет измерений|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Количество|Итого|Всего событий, которые соответствуют этой подписке на события|Нет измерений|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Количество|Итого|Всего событий, которые не удалось доставить в эту подписку на события|Error, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Количество|Итого|Всего событий, доставленных в эту подписку на события|Нет измерений|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Нет измерений|
|DroppedEventCount|Удаленные события|Количество|Итого|Всего удаленных событий, которые соответствуют этой подписке на события|DropReason|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Количество|Итого|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Количество|Итого|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|События с ошибками|Количество|Итого|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Количество|Итого|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|PublishSuccessLatencyInMs|Publish Success Latency|Количество|Итого|Publish success latency in milliseconds|Нет измерений|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Количество|Итого|Выполненные запросы для Microsoft.EventHub.|EntityName, |
|ServerErrors|Ошибки сервера.|Количество|Итого|Ошибки на сервере для Microsoft.EventHub.|EntityName, |
|UserErrors|Ошибки пользователей.|Количество|Итого|Ошибки пользователей для Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Ошибки превышения квоты.|Количество|Итого|Ошибки превышения квоты для Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Регулируемые запросы.|Количество|Итого|Регулируемые запросы для Microsoft.EventHub.|EntityName, |
|IncomingRequests|Входящих запросов|Количество|Итого|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Количество|Итого|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Количество|Итого|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байты|Итого|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байты|Итого|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Количество|Среднее|Число активных подключений для Microsoft.EventHub.|Нет измерений|
|ConnectionsOpened|Открытые подключения.|Количество|Среднее|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Количество|Среднее|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Количество|Итого|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Количество|Итого|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байты|Итого|Записанные байты для Microsoft.EventHub.|EntityName|
|Размер|Размер|Байты|Среднее|Размер EventHub в байтах.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Количество|Итого|Total incoming send requests for a namespace (Deprecated)|Нет измерений|
|SUCCREQ|Successful Requests (Deprecated)|Количество|Итого|Total successful requests for a namespace (Deprecated)|Нет измерений|
|FAILREQ|Failed Requests (Deprecated)|Количество|Итого|Total failed requests for a namespace (Deprecated)|Нет измерений|
|SVRBSY|Server Busy Errors (Deprecated)|Количество|Итого|Total server busy errors for a namespace (Deprecated)|Нет измерений|
|INTERR|Internal Server Errors (Deprecated)|Количество|Итого|Total internal server errors for a namespace (Deprecated)|Нет измерений|
|MISCERR|Other Errors (Deprecated)|Количество|Итого|Total failed requests for a namespace (Deprecated)|Нет измерений|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Количество|Итого|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Please use Incoming Messages metric instead (Deprecated)|Нет измерений|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Количество|Итого|Total incoming messages for a namespace (Deprecated)|Нет измерений|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Количество|Итого|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Please use Outgoing Messages metric instead (Deprecated)|Нет измерений|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Количество|Итого|Total outgoing messages for a namespace (Deprecated)|Нет измерений|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Байты|Итого|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Please use Incoming bytes metric instead (Deprecated)|Нет измерений|
|EHINBYTES|Входящие байты (не рекомендуется)|Байты|Итого|Event Hub incoming message throughput for a namespace (Deprecated)|Нет измерений|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Байты|Итого|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Please use Outgoing bytes metric instead (Deprecated)|Нет измерений|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байты|Итого|Event Hub outgoing message throughput for a namespace (Deprecated)|Нет измерений|
|EHABL|Archive backlog messages (Deprecated)|Количество|Итого|Event Hub archive messages in backlog for a namespace (Deprecated)|Нет измерений|
|EHAMSGS|Archive messages (Deprecated)|Количество|Итого|Event Hub archived messages in a namespace (Deprecated)|Нет измерений|
|EHAMBS|Archive message throughput (Deprecated)|Байты|Итого|Event Hub archived message throughput in a namespace (Deprecated)|Нет измерений|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Количество|Итого|Выполненные запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ServerErrors|Ошибки сервера. (предварительная версия)|Количество|Итого|Ошибки на сервере для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|UserErrors|Ошибки пользователей. (предварительная версия)|Количество|Итого|Ошибки пользователей для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|QuotaExceededErrors|Ошибки превышения квоты. (предварительная версия)|Количество|Итого|Ошибки превышения квоты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Количество|Итого|Регулируемые запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingRequests|Входящие запросы (предварительная версия)|Количество|Итого|Входящие запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingMessages|Входящие сообщения (предварительная версия)|Количество|Итого|Входящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Количество|Итого|Исходящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingBytes|Входящие байты. (предварительная версия)|Байты|Итого|Входящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingBytes|Исходящие байты. (предварительная версия)|Байты|Итого|Исходящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ActiveConnections|Активные подключения (предварительная версия)|Количество|Среднее|Число активных подключений для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Количество|Среднее|Открытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Закрытые подключения. (предварительная версия)|Количество|Среднее|Закрытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CaptureBacklog|Невыполненная работа записи. (предварительная версия)|Количество|Итого|Невыполненная работа записи для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedMessages|Записанные сообщения. (предварительная версия)|Количество|Итого|Записанные сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedBytes|Записанные байты. (предварительная версия)|Байты|Итого|Записанные байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ЦП|CPU (Preview) (ЦП (предварительная версия))|Процент|Максимальная|Использование ЦП для кластера концентратора событий в процентах|Роль|
|AvailableMemory|Available Memory (Preview) (Доступная память (предварительная версия))|Количество|Максимальная|Объем доступной памяти для кластера концентратора событий в байтах|Роль|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Количество|Итого|Число запросов к шлюзу|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Количество|Итого|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Количество|Максимальная|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Количество|Среднее|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Количество|Среднее|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Количество|Среднее|Емкость, передаваемая для автомасштабирования при выполнении.|Нет измерений|
|ScaleActionsInitiated|Инициированные действия масштабирования|Количество|Итого|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Общедоступная предварительная версия)

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Доступность|Процент|Среднее|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Тесты доступности|Количество|Количество|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Среднее|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Время подключения к сети при загрузке страницы|MilliSeconds|Среднее|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|Нет измерений|
|browserTimings/processingDuration|Время обработки клиента|MilliSeconds|Среднее|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|Нет измерений|
|browserTimings/receiveDuration|Время получения ответа|MilliSeconds|Среднее|Время от первого до последнего байта или до отключения.|Нет измерений|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Среднее|Время от установки сетевого подключения до получения первого байта.|Нет измерений|
|browserTimings/totalDuration|Время загрузки страницы в браузере|MilliSeconds|Среднее|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|Нет измерений|
|dependencies/count|Вызовы зависимостей|Количество|Количество|Число вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Длительность зависимости|MilliSeconds|Среднее|Длительность вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Количество|Количество|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Просмотры страниц|Количество|Количество|Число просмотров страниц.|operation/synthetic|
|pageViews/duration|Время загрузки страницы|MilliSeconds|Среднее|Время загрузки страницы|operation/synthetic|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Среднее|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue (HTTP-запросы в очереди приложений)|Количество|Среднее|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|Число/с|Среднее|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|Число/с|Среднее|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|Байт/с|Среднее|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Обработка ЦП|Процент|Среднее|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Процессорное время|Процент|Среднее|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Объем доступной памяти|Байты|Среднее|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Количество байтов исключительного использования процессов|Байты|Среднее|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|Время ответа от сервера|MilliSeconds|Среднее|Время с момента получения HTTP-запроса до завершения отправки ответа.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Запросы сервера|Количество|Количество|Число выполненных запросов HTTP.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Failed requests (Неудачные запросы)|Количество|Количество|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|Число/с|Среднее|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Исключения|Количество|Количество|Общее число всех неперехваченных исключений.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Исключения браузера|Количество|Количество|Число необработанных исключений в браузере.|Нет измерений|
|exceptions/server|Исключения сервера|Количество|Количество|Число неперехваченных исключений, созданных в серверном приложении.|cloud/roleName, cloud/roleInstance|
|traces/count|Трассировки|Количество|Количество|Число документов трассировки|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Количество|Количество|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунды|Среднее|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Всего результатов для API службы|Количество|Количество|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization (Использование кэша)|Процент|Среднее|Уровень загрузки в области кластера|Нет|
|QueryDuration|Длительность запросов|Миллисекунды|Среднее|Длительность запросов в секундах|Состояние запросов|
|IngestionUtilization|Использование приема данных|Процент|Среднее|Доля использованных слотов приема данных в кластере|Нет|
|KeepAlive|Проверка активности (Keep Alive)|Количество|Среднее|Проверка работоспособности показывает, что кластер отвечает на запросы|Нет|
|IngestionVolumeInMB|Ingestion Volume (In MB) (Объем приема данных (в МБ))|Количество|Итого|Общий объем данных, принятых в кластере (в МБ)|База данных|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds) (Задержка приема данных (в секундах))|Секунды|Среднее|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|Нет|
|EventProcessedForEventHubs|Events Processed (for Event Hubs) (Обработанные события (для концентраторов событий))|Количество|Итого|Число событий, обрабатываемых кластером при приеме данных из концентратора событий|Нет|
|IngestionResult|Ingestion Result (Результат приема данных)|Количество|Количество|Число операций приема данных|Status|
|ЦП|ЦП|Процент|Среднее|Уровень загрузки ЦП|Нет|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Количество | Итого | Number of records exported for every storage artifact written during the export operation  | Нет |
| ExportUtilization | Export Utilization | Процент | Максимальная | Export utilization | Нет |
| ContinuousExportPendingCount | Continuous Export Pending Count | Количество | Максимальная | The number of pending continuous export jobs ready for execution | Нет |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Количество | Максимальная | The max time in minutes of all continuous exports which are pending and ready for execution | Нет |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Количество|Количество|Число вызовов API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Количество|Итого|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Количество|Итого|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Количество|Итого|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Количество|Итого|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Runs Canceled|Количество|Итого|Number of workflow runs canceled.|Нет измерений|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Количество|Итого|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Процент|Итого|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Количество|Итого|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Количество|Итого|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Количество|Итого|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed|Количество|Итого|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Количество|Итого|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Количество|Итого|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Количество|Итого|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Количество|Итого|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Количество|Итого|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Количество|Итого|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Количество|Итого|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Количество|Итого|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Количество|Итого|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|BillableActionExecutions|Billable Action Executions|Количество|Итого|Число выполненных действий рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillableTriggerExecutions|Billable Trigger Executions|Количество|Итого|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Нет измерений|
|TotalBillableExecutions|Total Billable Executions|Количество|Итого|Число выполнений рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Количество|Итого|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Количество|Итого|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Количество|Итого|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Количество|Итого|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Количество|Итого|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Количество|Итого|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Количество|Итого|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Количество|Итого|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Количество|Итого|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Количество|Итого|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Runs Canceled|Количество|Итого|Number of workflow runs canceled.|Нет измерений|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Количество|Итого|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunStartThrottledEvents|Run Start Throttled Events|Количество|Итого|Number of workflow run start throttled events.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Процент|Итого|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Количество|Итого|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Количество|Итого|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Количество|Итого|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed |Количество|Итого|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Количество|Итого|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Количество|Итого|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Количество|Итого|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Количество|Итого|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Количество|Итого|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Количество|Итого|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Количество|Итого|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Количество|Итого|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Количество|Итого|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Процент|Среднее|Workflow processor usage for integration service environment.|Нет измерений|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Процент|Среднее|Workflow memory usage for integration service environment.|Нет измерений|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Процент|Среднее|Connector processor usage for integration service environment.|Нет измерений|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Процент|Среднее|Connector memory usage for integration service environment.|Нет измерений|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Количество|Итого|Number of runs completed successfully for this workspace|Сценарий|
|Started Runs|Started Runs|Количество|Итого|Number of runs started for this workspace|Сценарий|
|циклы выполнения со сбоем;|циклы выполнения со сбоем;|Количество|Итого|Number of runs failed for this workspace|Сценарий|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Количество|Количество|Число вызовов API|ApiCategory, ApiName, ResultType, ResponseCode|
|Доступность|Доступность|Процент|Среднее|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency (Среднее время прочей задержки)|мс/операцию|Среднее|Среднее время прочей задержки (кроме задержки на чтение или запись) в миллисекундах на операцию|Нет измерений|
|AverageReadLatency|Средняя задержка чтения|мс/операцию|Среднее|Средняя задержка чтения в миллисекундах на операцию|Нет измерений|
|AverageTotalLatency|Average total latency (Средняя общая задержка)|мс/операцию|Среднее|Средняя общая задержка в миллисекундах на операцию|Нет измерений|
|AverageWriteLatency|Средняя задержка записи|мс/операцию|Среднее|Средняя задержка записи в миллисекундах на операцию|Нет измерений|
|FilesystemOtherOps|Filesystem other ops (Прочие операции файловой системы)|операции|Среднее|Число прочих операций файловой системы (кроме операций чтения и записи)|Нет измерений|
|FilesystemReadOps|Filesystem read ops (Операции чтения файловой системы)|операции|Среднее|Число операций чтения файловой системы|Нет измерений|
|FilesystemTotalOps|Filesystem total ops (Общее число операций файловой системы)|операции|Среднее|Сумма всех операций файловой системы|Нет измерений|
|FilesystemWriteOps|Filesystem write ops (Операций записи файловой системы)|операции|Среднее|Число операций записи файловой системы|Нет измерений|
|IoBytesPerOtherOps|Io bytes per other ops (Байты ввода-вывода на прочие операции)|байт/операцию|Среднее|Число байт ввода-вывода на прочие операции (кроме операций чтения и записи)|Нет измерений|
|IoBytesPerReadOps|Io bytes per read ops (Байты ввода-вывода на операцию чтения)|байт/операцию|Среднее|Число байт ввода-вывода на операцию чтения|Нет измерений|
|IoBytesPerTotalOps|Io bytes per op across all operations (Байты ввода-вывода на операцию по всем операциям)|байт/операцию|Среднее|Сумма байт ввода-вывода по всем операциям|Нет измерений|
|IoBytesPerWriteOps|Io bytes per write ops (Байты ввода-вывода на операцию записи)|байт/операцию|Среднее|Число байт ввода-вывода на операцию записи|Нет измерений|
|OtherIops|Other iops (Прочие операции ввода-вывода в секунду)|операций/с|Среднее|Прочие операции ввода-вывода в секунду|Нет измерений|
|OtherThroughput|Other throughput (Прочая пропускная способность)|МB/с|Среднее|Прочая пропускная способность (кроме операций чтения и записи) в мегабайтах в секунду|Нет измерений|
|ReadIops|Read iops (Число операций ввода-вывода в секунду при чтении)|операций/с|Среднее|Число операций ввода-вывода в секунду при чтении|Нет измерений|
|ReadThroughput|Read throughput (Пропускная способность при чтении)|МB/с|Среднее|Пропускная способность при чтении (в мегабайтах в секунду)|Нет измерений|
|TotalIops|Общее число операций ввода-вывода|операций/с|Среднее|Сумма всех операций ввода-вывода в секунду|Нет измерений|
|TotalThroughput|Общая пропускная способность|МB/с|Среднее|Сумма всей пропускной способности в мегабайтах в секунду|Нет измерений|
|VolumeAllocatedSize|Volume allocated size (Выделенный объем тома)|Байты|Среднее|Выделенный размер тома (не фактически используемые байты)|Нет измерений|
|VolumeLogicalSize|Volume logical size (Логический размер тома)|Байты|Среднее|Логический размер тома (используемые байты)|Нет измерений|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|Байты|Среднее|Размер всех моментальных снимков в томе|Нет измерений|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|операций/с|Среднее|Число операций ввода-вывода в секунду при записи|Нет измерений|
|WriteThroughput|Write throughput (Пропускная способность операций записи)|МB/с|Среднее|Пропускная способность операций записи в мегабайтах в секунду|Нет измерений|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size (Выделенный размер пула для тома)|Байты|Среднее|Выделенный размер пула (не фактически используемые байты)|Нет измерений|
|VolumePoolAllocatedUsed|Volume pool allocated used (Используемый размер выделенного пула для тома)|Байты|Среднее|Используемый выделенный размер пула|Нет измерений|
|VolumePoolTotalLogicalSize|Volume pool total logical size (Общий логический размер пула для тома)|Байты|Среднее|Сумма логического размера всех томов, входящих в пул|Нет измерений|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size (Общий размер моментальных снимков пула для тома)|Байты|Среднее|Сумма всех моментальных снимков в пуле|Нет измерений|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Количество|Итого|Число байт, отправленных сетевым интерфейсом|Нет измерений|
|BytesReceivedRate|Получено байт|Количество|Итого|Число байт, полученных сетевым интерфейсом|Нет измерений|
|PacketsSentRate|Отправлено пакетов|Количество|Итого|Число пакетов, отправленных сетевым интерфейсом|Нет измерений|
|PacketsReceivedRate|Получено пакетов|Количество|Итого|Число пакетов, полученных сетевым интерфейсом|Нет измерений|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Количество|Среднее|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Количество|Среднее|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Количество байтов|Количество|Итого|Общее количество байтов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Количество пакетов|Количество|Итого|Общее количество пакетов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Количество пакетов SYN|Количество|Итого|Общее количество пакетов SYN, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Количество подключений SNAT|Количество|Итого|Общее количество подключений SNAT, созданных за период времени|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Количество|Итого|Общее число портов SNAT, выделенных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Количество|Итого|Общее количество портов SNAT, использованных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Количество|Итого|Количество запросов, выполненных для зоны DNS|Нет измерений|
|RecordSetCount|Количество наборов записей|Количество|Максимальная|Количество наборов записей в зоне DNS|Нет измерений|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|Нет измерений|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PacketsInDDoS|Входящие пакеты (атака DDoS)|Число/с|Максимальная|Входящие пакеты (атака DDoS)|Нет измерений|
|PacketsDroppedDDoS|Удаленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты (атака DDoS)|Нет измерений|
|PacketsForwardedDDoS|Перенаправленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты (атака DDoS)|Нет измерений|
|TCPPacketsInDDoS|Входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsDroppedDDoS|Удаленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsForwardedDDoS|Перенаправленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|UDPPacketsInDDoS|Входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsDroppedDDoS|Удаленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsForwardedDDoS|Перенаправленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|BytesInDDoS|Входящие байты (атака DDoS)|Байт/с|Максимальная|Входящие байты (атака DDoS)|Нет измерений|
|BytesDroppedDDoS|Удаленные входящие байты (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты (атака DDoS)|Нет измерений|
|BytesForwardedDDoS|Перенаправленные входящие байты (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты (атака DDoS)|Нет измерений|
|TCPBytesInDDoS|Входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesDroppedDDoS|Удаленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesForwardedDDoS|Перенаправленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты TCP (атака DDoS)|Нет измерений|
|UDPBytesInDDoS|Входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesDroppedDDoS|Удаленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesForwardedDDoS|Перенаправленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты UDP (атака DDoS)|Нет измерений|
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Количество|Максимальная|Выполняется ли атака DDoS|Нет измерений|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|Нет измерений|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Количество|Среднее|Средняя доступность IP-адреса за период времени|Port|
|ByteCount|Количество байтов|Количество|Итого|Общее количество байтов, переданных за период времени|Port, Direction|
|PacketCount|Количество пакетов|Количество|Итого|Общее количество пакетов, переданных за период времени|Port, Direction|
|SynCount|Количество пакетов SYN|Количество|Итого|Общее количество пакетов SYN, переданных за период времени|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Количество|Итого|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Количество|Итого|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Итого|Количество байтов в секунду, обрабатываемых шлюзом приложений|Нет измерений|
|UnhealthyHostCount|Количество неработоспособных узлов|Количество|Среднее|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов|Количество|Среднее|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Количество|Итого|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Количество|Итого|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа|Количество|Итого|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения|Количество|Итого|Число текущих установленных подключений к шлюзу приложений|Нет измерений|
|CapacityUnits|Current Capacity Units|Количество|Среднее|Capacity Units consumed|Нет измерений|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|Байт/с|Среднее|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|Байт/с|Среднее|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Количество|Максимальная|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Среднее|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Итого|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Итого|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Количество|Итого|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Количество|Итого|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Количество|Итого|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Количество|Итого|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Количество|Итого|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Количество|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Процент|Среднее|Доля проб мониторинга подключения с ошибками, в процентах|Нет измерений|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Среднее|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|Нет измерений|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Количество|Итого|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Размер запроса|Байты|Итого|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Размер ответа|Байты|Итого|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Число запросов к серверному компоненту|Количество|Итого|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Среднее|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Серверная часть|
|TotalLatency|Общая задержка|MilliSeconds|Среднее|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Процент|Среднее|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Количество|Итого|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Количество|Итого|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Нет измерений|
|registration.create|Операции создания регистрации|Количество|Итого|Число всех успешных операций создания регистраций.|Нет измерений|
|registration.update|Операции обновления регистрации|Количество|Итого|Число всех успешных операций обновления регистраций.|Нет измерений|
|registration.get|Операции чтения регистрации|Количество|Итого|Число всех успешных запросов регистрации.|Нет измерений|
|registration.delete|Операции удаления регистрации|Количество|Итого|Число всех успешных операций удаления регистраций.|Нет измерений|
|incoming|Входящие сообщения|Количество|Итого|Число всех успешно отправленных вызовов API. |Нет измерений|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Количество|Итого|Scheduled Push Notifications Canceled|Нет измерений|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Количество|Итого|Scheduled Push Notifications Canceled|Нет измерений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Количество|Итого|Запланированных уведомлений в состоянии ожидания|Нет измерений|
|installation.all|Операции управления установкой|Количество|Итого|Операции управления установкой|Нет измерений|
|installation.get|Операции получения установки|Количество|Итого|Операции получения установки|Нет измерений|
|installation.upsert|Операции создания или обновления установки|Количество|Итого|Операции создания или обновления установки|Нет измерений|
|installation.patch|Операции исправления установки|Количество|Итого|Операции исправления установки|Нет измерений|
|installation.delete|Операции удаления установки|Количество|Итого|Операции удаления установки|Нет измерений|
|outgoing.allpns.success|Успешные уведомления|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Количество|Итого|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Нет измерений|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Количество|Итого|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Нет измерений|
|outgoing.allpns.channelerror|Ошибки канала|Количество|Итого|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Нет измерений|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Количество|Итого|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Нет измерений|
|outgoing.wns.success|Успешные уведомления WNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Нет измерений|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Нет измерений|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Количество|Итого|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Нет измерений|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Нет измерений|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Количество|Итого|Windows Live недоступна.|Нет измерений|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Количество|Итого|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Нет измерений|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Количество|Итого|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Нет измерений|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Количество|Итого|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Нет измерений|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Количество|Итого|Полезные данные уведомления слишком большие (состояние WNS: 413).|Нет измерений|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Нет измерений|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|Нет измерений|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Нет измерений|
|outgoing.wns.pnserror|Ошибки WNS|Количество|Итого|Уведомление не доставлено из-за ошибок связи с WNS.|Нет измерений|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Количество|Итого|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Нет измерений|
|outgoing.apns.success|Успешные уведомления APNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Количество|Итого|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|Нет измерений|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Количество|Итого|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Нет измерений|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Количество|Итого|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|Нет измерений|
|outgoing.apns.pnserror|Ошибки APNS|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с APNS.|Нет измерений|
|outgoing.gcm.success|Успешные уведомления GCM|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Нет измерений|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Количество|Итого|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Нет измерений|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Нет измерений|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Нет измерений|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Нет измерений|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Количество|Итого|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Нет измерений|
|outgoing.gcm.pnserror|Ошибки GCM|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с GCM.|Нет измерений|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Нет измерений|
|outgoing.mpns.success|Успешные уведомления MPNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Нет измерений|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Нет измерений|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Количество|Итого|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Нет измерений|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Количество|Итого|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Нет измерений|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Количество|Итого|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Нет измерений|
|outgoing.mpns.pnserror|Ошибки MPNS|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с MPNS.|Нет измерений|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|notificationhub.pushes|Все исходящие уведомления|Количество|Итого|Все исходящие уведомления из центра уведомлений.|Нет измерений|
|incoming.all.requests|Все входящие запросы|Количество|Итого|Общее количество входящих запросов для концентратора уведомлений|Нет измерений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Количество|Итого|Общее количество неудачных входящих запросов для концентратора уведомлений|Нет измерений|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Процент свободных индексных дескрипторов|Количество|Среднее|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Процент свободного места|Количество|Среднее|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Процент используемых индексных дескрипторов|Количество|Среднее|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Процент используемого места|Количество|Среднее|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Скорость чтения с диска (байт/с)|Количество|Среднее|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Количество|Среднее|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Количество|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Скорость записи на диск (байт/с)|Количество|Среднее|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Количество|Среднее|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Количество|Среднее|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Скорость обмена с логическим диском (байт/с)|Количество|Среднее|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|Процент доступной памяти|Количество|Среднее|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Процент доступной области подкачки|Количество|Среднее|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Процент используемой памяти|Количество|Среднее|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Процент используемой области подкачки|Количество|Среднее|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Доступный объем памяти в МБ|Количество|Среднее|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Доступный объем подкачки в МБ|Количество|Среднее|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Операций чтения со страницы в секунду|Количество|Среднее|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Операций записи на страницу в секунду|Количество|Среднее|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Страниц в секунду|Количество|Среднее|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Используемая области подкачки в МБ|Количество|Среднее|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Используемый объем памяти в МБ|Количество|Среднее|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Всего передано байт|Количество|Среднее|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Всего получено байт|Количество|Среднее|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Всего байт|Количество|Среднее|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Всего передано пакетов|Количество|Среднее|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Всего получено пакетов|Количество|Среднее|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Всего ошибок Rx|Количество|Среднее|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Всего ошибок Tx|Количество|Среднее|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Всего конфликтов|Количество|Среднее|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Avg. Disk sec/Read|Количество|Среднее|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время обращения к диску (с)|Avg. Disk sec/Transfer|Количество|Среднее|Average_Avg. время обращения к диску (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Avg. Disk sec/Write|Количество|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Скорость обмена с физическим диском (байт/с)|Количество|Среднее|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Процент времени работы в привилегированном режиме|Количество|Среднее|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Процент времени пользователя|Количество|Среднее|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Используемая память в КБ|Количество|Среднее|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Виртуальная общая память|Количество|Среднее|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Процент времени DPC|Количество|Среднее|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Процент времени простоя|Количество|Среднее|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Процент времени прерывания|Количество|Среднее|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Процент времени ожидания ввода-вывода|Количество|Среднее|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Процент времени работы с низким приоритетом|Количество|Среднее|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Процент времени работы в привилегированном режиме|Количество|Среднее|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% загруженности процессора|Количество|Среднее|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Процент времени пользователя|Количество|Среднее|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Объем свободной физической памяти|Количество|Среднее|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Объем свободного места в файлах подкачки|Количество|Среднее|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Объем свободной виртуальной памяти|Количество|Среднее|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Процессы|Количество|Среднее|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Объем, сохраненный в файлах подкачки|Количество|Среднее|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Время доступности|Количество|Среднее|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Пользователи|Количество|Среднее|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Avg. Disk sec/Read|Количество|Среднее|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Avg. Disk sec/Write|Количество|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Количество|Среднее|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Количество|Среднее|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Количество|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Количество|Среднее|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Количество|Среднее|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Процент свободного места|Количество|Среднее|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Доступный объем в МБ|Количество|Среднее|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Количество|Среднее|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Полученных байтов/с|Количество|Среднее|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Отправленных байтов/с|Количество|Среднее|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Всего байтов/с|Количество|Среднее|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% загруженности процессора|Количество|Среднее|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Длина очереди процессора|Количество|Среднее|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Пульс|Пульс|Количество|Итого|Пульс|Computer, OSType, Version, SourceComputerId|
|Обновить|Обновить|Количество|Среднее|Обновить|Computer, Product, Classification, UpdateState, Optional, Approved|
|Мероприятие|Мероприятие|Количество|Среднее|Мероприятие|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунды|Среднее|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Количество|Среднее|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Количество|Итого|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байты|Среднее|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|Нет измерений|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Количество|Итого|Выполненные ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Количество|Итого|ClientError в ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Количество|Итого|ServerError в ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Количество|Итого|Выполненные SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Количество|Итого|ClientError в SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Количество|Итого|ServerError в SenderConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Количество|Итого|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Количество|Итого|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Количество|Итого|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Количество|Итого|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Количество|Итого|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Количество|Итого|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Количество|Итого|Всего SenderDisconnects для Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Среднее|Среднее время задержки поиска для службы поиска.|Нет измерений|
|SearchQueriesPerSecond|Search queries per second|Число/с|Среднее|Число поисковых запросов в секунду для службы поиска.|Нет измерений|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Среднее|Процент отрегулированных поисковых запросов для службы поиска.|Нет измерений|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Количество|Итого|Общее количество выполненных запросов для пространства имен (предварительная версия)|EntityName|
|ServerErrors|Ошибки сервера. (предварительная версия)|Количество|Итого|Ошибки на сервере для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Количество|Итого|Ошибки пользователей для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Количество|Итого|Регулируемые запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (предварительная версия)|Количество|Итого|Входящие запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (предварительная версия)|Количество|Итого|Входящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Количество|Итого|Исходящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ActiveConnections|Активные подключения (предварительная версия)|Количество|Итого|Число активных подключений для Microsoft.ServiceBus. (предварительная версия)|Нет измерений|
|Размер|Размер (предварительная версия)|Байты|Среднее|Размер очереди или раздела в байтах. (предварительная версия)|EntityName|
|Сообщения|Число сообщений в очереди или разделе. (предварительная версия)|Количество|Среднее|Число сообщений в очереди или разделе. (предварительная версия)|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе. (предварительная версия)|Количество|Среднее|Число активных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (предварительная версия)|Количество|Среднее|Count of dead-lettered messages in a Queue/Topic. (предварительная версия)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (предварительная версия)|Количество|Среднее|Count of scheduled messages in a Queue/Topic. (предварительная версия)|EntityName|
|CPUXNS|CPU usage per namespace|Процент|Максимальная|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|Нет измерений|
|WSXNS|Memory size usage per namespace|Процент|Максимальная|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|Нет измерений|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Количество|Среднее|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Байты|Среднее|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Количество|Среднее|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Байты|Среднее|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Процент|Среднее|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Процент|Среднее|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Количество|Среднее|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Количество|Среднее|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Количество|Среднее|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Количество|Среднее|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Количество|Среднее|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Количество|Максимальная|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Количество сообщений|Количество|Итого|Общее количество сообщений.|Нет измерений|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байты|Итого|Входящий трафик службы|Нет измерений|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байты|Итого|Исходящий трафик службы|Нет измерений|
|UserErrors|Ошибки пользователей|Процент|Максимальная|Процент ошибок пользователей|Нет измерений|
|SystemErrors|Системные ошибки|Процент|Максимальная|Процент системных ошибок|Нет измерений|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Байты|Среднее|Data space allocated. Not applicable to data warehouses.|Нет измерений|
|app_cpu_billed|App CPU billed|Количество|Итого|App CPU billed. Applies to serverless databases.|Нет измерений|
|app_cpu_percent|App CPU percentage|Процент|Среднее|App CPU percentage. Applies to serverless databases.|Нет измерений|
|app_memory_percent|App memory used percentage|Процент|Среднее|App memory used percentage. Applies to serverless databases.|Нет измерений|
|blocked_by_firewall|Заблокировано брандмауэром|Количество|Итого|Заблокировано брандмауэром|Нет измерений|
|cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Процент|Максимальная|Cache hit percentage. Applies only to data warehouses.|Нет измерений|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Процент|Максимальная|Cache used percentage. Applies only to data warehouses.|Нет измерений|
|connection_failed|Неудачные подключения|Количество|Итого|Неудачные подключения|Нет измерений|
|connection_successful|Успешные подключения|Количество|Итого|Успешные подключения|Нет измерений|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Нет измерений|
|cpu_limit|CPU limit|Количество|Среднее|CPU limit. Applies to vCore-based databases.|Нет измерений|
|cpu_used|CPU used|Количество|Среднее|CPU used. Applies to vCore-based databases.|Нет измерений|
|deadlock|Взаимоблокировки|Количество|Итого|Deadlocks. Not applicable to data warehouses.|Нет измерений|
|diff_backup_size_bytes|Differential backup storage size|Байты|Максимальная|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|Нет измерений|
|dtu_limit|Лимит DTU|Количество|Среднее|DTU Limit. Applies to DTU-based databases.|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|DTU percentage. Applies to DTU-based databases.|Нет измерений|
|dtu_used|DTU used|Количество|Среднее|DTU used. Applies to DTU-based databases.|Нет измерений|
|dw_cpu_percent|Процент использования ЦП на уровне узла DW|Процент|Среднее|Процент использования ЦП на уровне узла DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Процент операций ввода-вывода данных на уровне узла DW|Процент|Среднее|Процент операций ввода-вывода данных на уровне узла DW|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU percentage. Applies only to data warehouses.|Нет измерений|
|dwu_limit|Лимит DWU|Количество|Максимальная|DWU limit. Applies only to data warehouses.|Нет измерений|
|dwu_used|DWU used|Количество|Максимальная|DWU used. Applies only to data warehouses.|Нет измерений|
|full_backup_size_bytes|Full backup storage size|Байты|Максимальная|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|Нет измерений|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Процент|Среднее|Local tempdb percentage. Applies only to data warehouses.|Нет измерений|
|log_backup_size_bytes|Log backup storage size|Байты|Максимальная|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|Нет измерений|
|log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage. Not applicable to data warehouses.|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|Нет измерений|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Sessions percentage. Not applicable to data warehouses.|Нет измерений|
|sqlserver_process_core_percent|SQL Server process core percent|Процент|Максимальная|This metric is a placeholder and not populated at this time.|Нет измерений|
|sqlserver_process_memory_percent|SQL Server process memory percent|Процент|Максимальная|This metric is a placeholder and not populated at this time.|Нет измерений|
|хранилище|Data space used|Байты|Максимальная|Total database size. Not applicable to data warehouses.|Нет измерений|
|storage_percent|Data space used percent|Процент|Максимальная|Database size percentage. Not applicable to data warehouses or hyperscale databases.|Нет измерений|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Количество|Максимальная|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Количество|Максимальная|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|tempdb_log_used_percent|Tempdb Percent Log Used|Процент|Максимальная|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|workers_percent|Workers percentage|Процент|Среднее|Workers percentage. Not applicable to data warehouses.|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|In-Memory OLTP storage percent. Not applicable to data warehouses.|Нет измерений|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Байты|Среднее|Data space allocated|Нет измерений|
|allocated_data_storage_percent|Data space allocated percent|Процент|Максимальная|Data space allocated percent|Нет измерений|
|cpu_limit|CPU limit|Количество|Среднее|CPU limit. Applies to vCore-based elastic pools.|Нет измерений|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Нет измерений|
|cpu_used|CPU used|Количество|Среднее|CPU used. Applies to vCore-based elastic pools.|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|DTU percentage. Applies to DTU-based elastic pools.|Нет измерений|
|eDTU_limit|eDTU limit|Количество|Среднее|eDTU limit. Applies to DTU-based elastic pools.|Нет измерений|
|eDTU_used|eDTU used|Количество|Среднее|eDTU used. Applies to DTU-based elastic pools.|Нет измерений|
|log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|Нет измерений|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|Нет измерений|
|storage_limit|Data max size|Байты|Среднее|Storage limit|Нет измерений|
|storage_percent|Data space used percent||Процент|Среднее|Storage percentage|Нет измерений|
|storage_used|Data space used|Байты|Среднее|Используемое хранилище|Нет измерений|
|sqlserver_process_core_percent|SQL Server process core percent|Процент|Максимальная|This metric is a placeholder and not populated at this time.|Нет измерений|
|sqlserver_process_memory_percent|SQL Server process memory percent|Процент|Максимальная|This metric is a placeholder and not populated at this time.|Нет измерений|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Количество|Максимальная|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Количество|Максимальная|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|tempdb_log_used_percent|Tempdb Percent Log Used|Процент|Максимальная|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|Нет измерений|
|workers_percent|Workers percentage|Процент|Среднее|Workers percentage|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|avg_cpu_percent|Средний процент использования ЦП|Процент|Среднее|Средний процент использования ЦП|Нет измерений|
|io_bytes_read|Количество считанных байт ввода-вывода|Байты|Среднее|Количество считанных байт ввода-вывода|Нет измерений|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Количество|Среднее|IO requests count (Количество запросов ввода-вывода)|Нет измерений|
|io_bytes_written|IO bytes written (Записано байт ввода-вывода)|Байты|Среднее|IO bytes written (Записано байт ввода-вывода)|Нет измерений|
|reserved_storage_mb|Зарезервированное дисковое пространство|Количество|Среднее|Зарезервированное дисковое пространство|Нет измерений|
|storage_space_used_mb|Storage space used (Использовано дискового пространства)|Количество|Среднее|Storage space used (Использовано дискового пространства)|Нет измерений|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Количество|Среднее|Virtual core count (Число виртуальных ядер)|Нет измерений|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, Tier|
|BlobCount|Количество больших двоичных объектов|Количество|Итого|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType|       |BlobCount|Количество больших двоичных объектов|Количество|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, Tier|
|ContainerCount|Количество контейнеров больших двоичных объектов|Количество|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Нет измерений|
|IndexCapacity|Index Capacity|Байты|Среднее|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|Нет измерений|
|FileCount|Количество файлов|Количество|Среднее|Количество файлов в службе файлов учетной записи хранения.|Нет измерений|
|FileShareCount|Количество общих файловых ресурсов|Количество|Среднее|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Нет измерений|
|QueueCount|Количество очередей|Количество|Среднее|Количество очередей в службе очередей учетной записи хранения.|Нет измерений|
|QueueMessageCount|Количество сообщений очереди|Количество|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|Нет измерений|
|TableCount|Количество таблиц|Количество|Среднее|Количество таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|TableEntityCount|Количество сущностей таблиц|Количество|Среднее|Количество сущностей таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Количество|Среднее|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Количество|Итого|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Несинхронизирующиеся файлы|Количество|Итого|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Синхронизировано байт|Байты|Итого|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Количество|Максимальная|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|имя_сервера;|
|StorageSyncRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итого|Total size of data recalled by the server|имя_сервера;|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|LogicalName, PartitionId|
|InputEvents|Входные события|Количество|Итого|Входные события|LogicalName, PartitionId|
|InputEventBytes|Байты входного события|Байты|Итого|Байты входного события|LogicalName, PartitionId|
|LateInputEvents|События позднего ввода|Количество|Итого|События позднего ввода|LogicalName, PartitionId|
|OutputEvents|Выходные события|Количество|Итого|Выходные события|LogicalName, PartitionId|
|ConversionErrors|Ошибки преобразования данных|Количество|Итого|Ошибки преобразования данных|LogicalName, PartitionId|
|Errors|Ошибки среды выполнения|Количество|Итого|Ошибки среды выполнения|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Неупорядоченные события|Количество|Итого|Неупорядоченные события|LogicalName, PartitionId|
|AMLCalloutRequests|Запросы функций|Количество|Итого|Запросы функций|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Количество|Итого|Неудачные запросы функций|LogicalName, PartitionId|
|AMLCalloutInputEvents|События функций|Количество|Итого|События функций|LogicalName, PartitionId|
|DeserializationError|Ошибки десериализации входа|Количество|Итого|Ошибки десериализации входа|LogicalName, PartitionId|
|EarlyInputEvents|Ранние входные события|Количество|Итого|Ранние входные события|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимальная|Предельная задержка|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Необработанные входные события|Количество|Максимальная|Необработанные входные события|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Полученные входные источники|Количество|Итого|Полученные входные источники|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Количество|Итого|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Количество|Итого|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итого|Количество байтов, полученных от всех источников событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итого|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Количество|Итого|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Количество|Среднее|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|WarmStorageMaxProperties|Warm Storage Max Properties|Количество|Максимальная|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|Нет измерений|
|WarmStorageUsedProperties|Warm Storage Used Properties |Количество|Максимальная|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|Нет измерений|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Количество|Итого|Количество сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Количество|Итого|Количество недопустимых сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итого|Количество байтов, считанных из источника событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итого|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Количество|Итого|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Количество|Среднее|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|WarmStorageMaxProperties|Warm Storage Max Properties|Количество|Максимальная|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|Нет измерений|
|WarmStorageUsedProperties|Warm Storage Used Properties |Количество|Максимальная|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|Нет измерений|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Байт/с|Среднее|Average disk throughput due to read operations over the sample period.|Нет измерений|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Байт/с|Среднее|Average disk throughput due to write operations over the sample period.|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Total disk throughput due to read operations over the sample period.|Нет измерений|
|Скорость записи на диск|Скорость записи на диск|Байты|Итого|Total disk throughput due to write operations over the sample period.|Нет измерений|
|DiskReadOperations|Disk Read Operations|Количество|Итого|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|Нет измерений|
|DiskWriteOperations|Disk Write Operations|Количество|Итого|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|Нет измерений|
|Чтение с дисков (операций/с)|Чтение с дисков (операций/с)|Число/с|Среднее|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|Нет измерений|
|Запись на диски (операций/с)|Запись на диски (операций/с)|Число/с|Среднее|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|Нет измерений|
|DiskReadLatency|Disk Read Latency|Миллисекунды|Среднее|Total read latency. The sum of the device and kernel read latencies.|Нет измерений|
|DiskWriteLatency|Disk Write Latency|Миллисекунды|Среднее|Total write latency. The sum of the device and kernel write latencies.|Нет измерений|
|NetworkInBytesPerSecond|Network In Bytes/Sec|Байт/с|Среднее|Average network throughput for received traffic.|Нет измерений|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|Байт/с|Среднее|Average network throughput for transmitted traffic.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Total network throughput for received traffic.|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Total network throughput for transmitted traffic.|Нет измерений|
|MemoryUsed|Memory Used|Байты|Среднее|The amount of machine memory that is in use by the VM.|Нет измерений|
|MemoryGranted|Memory Granted|Байты|Среднее|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|Нет измерений|
|MemoryActive|Memory Active|Байты|Среднее|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|Нет измерений|
|Загрузка ЦП|Загрузка ЦП|Процент|Среднее|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|Нет измерений|
|PercentageCpuReady|Percentage CPU Ready|Миллисекунды|Итого|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|Нет измерений|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Процент загруженности ЦП|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|Процент использования памяти|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|Длина очереди диска|Длина дисковой очереди|Количество|Среднее|Длина дисковой очереди|Экземпляр|
|Длина очереди HTTP|Длина очереди HTTP:|Количество|Среднее|Длина очереди HTTP:|Экземпляр|
|Получено байт|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|Передано байт|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests (Запросы)|Requests (Запросы)|Количество|Итого|Requests (Запросы)|Экземпляр|
|Получено байт|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|Передано байт|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|AppConnections|Соединения|Количество|Среднее|Соединения|Экземпляр|
|Handles|Счетчик дескрипторов|Количество|Среднее|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Количество|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Количество|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Количество|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Количество|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Количество|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Количество|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Количество|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Количество|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Получено байт|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|Передано байт|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Итого|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Количество|Итого|Function Execution Count|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Количество|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Количество|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Количество|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Количество|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Количество|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Количество|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Количество|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests (Запросы)|Requests (Запросы)|Количество|Итого|Requests (Запросы)|Экземпляр|
|Получено байт|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|Передано байт|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Количество|Итого|Function Execution Units|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Количество|Итого|Function Execution Count|Экземпляр|
|AppConnections|Соединения|Количество|Среднее|Соединения|Экземпляр|
|Handles|Счетчик дескрипторов|Количество|Среднее|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Количество|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Количество|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Количество|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Количество|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Количество|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Количество|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Количество|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Количество|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Requests (Запросы)|Requests (Запросы)|Количество|Итого|Requests (Запросы)|Экземпляр|
|Получено байт|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|Передано байт|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|Процент загруженности ЦП|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|Процент использования памяти|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|Длина очереди диска|Длина дисковой очереди|Количество|Среднее|Длина дисковой очереди|Экземпляр|
|Длина очереди HTTP|Длина очереди HTTP:|Количество|Среднее|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Количество|Итого|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Количество|Среднее|Общее число внешних интерфейсов|Нет измерений|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Количество|Среднее|Рабочие роли плана службы приложений уровня "Малый"|Нет измерений|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Количество|Среднее|Рабочие процессы плана службы приложений уровня "Средний"|Нет измерений|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Количество|Среднее|Рабочие процессы плана службы приложений уровня "Крупный"|Нет измерений|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Количество|Среднее|Общее количество рабочих процессов|Нет измерений|
|WorkersAvailable|Доступные рабочие процессы|Количество|Среднее|Доступные рабочие процессы|Нет измерений|
|WorkersUsed|Использованные рабочие процессы|Количество|Среднее|Использованные рабочие процессы|Нет измерений|
|Процент загруженности ЦП|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|Процент использования памяти|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|

## <a name="next-steps"></a>Дальнейшие действия
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создайте оповещения на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](resource-logs-overview.md)
