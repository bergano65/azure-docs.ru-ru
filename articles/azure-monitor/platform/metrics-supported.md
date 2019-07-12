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
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705172"
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor

Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor. Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Чтобы запросить метрики с измерениями и получить к ним доступ, воспользуйтесь версией [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример*. Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Average|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Average|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Average|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Average|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Average|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Average|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Average|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Average|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: Текущие подключения|Count|Average|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Average|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Average|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Average|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: Использование памяти|Байты|Average|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Average|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Average|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Average|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Average|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: Quota|Байты|Average|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Count|Average|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Average|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Average|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Average|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Average|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Average|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Count|Average|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Count|Average|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Count|Average|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Count|Average|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Count|Average|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Count|Average|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Count|Average|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Count|Average|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Count|Average|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Count|Average|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Count|Average|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Average|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Count|Average|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Count|Average|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Count|Average|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Percent|Average|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Count|Average|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Average|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Count|Итого|Число запросов к шлюзу|Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Count|Итого|Число успешных запросов к шлюзу|Location, Hostname|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Count|Итого|Число неавторизованных запросов к шлюзу|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Count|Итого|Количество сбоев при обработке запросов к шлюзу.|Location, Hostname|
|OtherRequests|Другие запросы к шлюзу|Count|Итого|Количество других запросов к шлюзу|Location, Hostname|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Average|Общая длительность запросов шлюза в миллисекундах|Location, Hostname|
|Capacity|Capacity|Percent|Average|Метрика использования для службы ApiManagement|Местоположение|
|EventHubTotalEvents|События всего концентратора событий|Count|Итого|Количество событий, отправленных к концентратору событий|Местоположение|
|EventHubSuccessfulEvents|События успешной концентратора событий|Count|Итого|Число успешных событий концентратора событий.|Местоположение|
|EventHubTotalFailedEvents|События сбоя концентратора событий|Count|Итого|Количество неудачных событий концентратора событий.|Местоположение|
|EventHubRejectedEvents|События отклоненных концентратора событий|Count|Итого|Количество отклоненных событий концентратора событий (неправильные конфигурации, или доступ запрещен)|Местоположение|
|EventHubThrottledEvents|События регулируемых концентратора событий|Count|Итого|Число регулируемых событий концентратора событий.|Местоположение|
|EventHubTimedoutEvents|Истекло время ожидания события концентратора событий.|Count|Итого|Число истекло время ожидания события концентратора событий.|Местоположение|
|EventHubDroppedEvents|События удаленные концентратора событий|Count|Итого|Число событий, пропущенный из-за достигнут предел размера очереди|Местоположение|
|EventHubTotalBytesSent|Размер событий концентратора событий.|Байты|Итого|Общий размер событий концентратора событий в байтах|Местоположение|
|Requests|Requests|Count|Итого|Запросы к шлюзу|Расположение, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Итого|Общее количество заданий|Состояние модуля Runbook|
|TotalUpdateDeploymentRuns|Запускается развертывание общего обновления|Count|Итого|Запускает общее развертывание обновления|SoftwareUpdateConfigurationName, состояние|
|TotalUpdateDeploymentMachineRuns|Запущенных компьютеров общего обновления развертывания|Count|Итого|Компьютер развертывания обновления программного обеспечения всего выполняется в запуска развертывания обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Итого|Общее число выделенных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalNodeCount|Dedicated Node Count|Count|Итого|Общее число выделенных узлов в учетной записи пакетной службы.|Нет измерений|
|LowPriorityCoreCount|LowPriority Core Count|Count|Итого|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Итого|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Нет измерений|
|CreatingNodeCount|Creating Node Count|Count|Итого|Количество создаваемых узлов.|Нет измерений|
|StartingNodeCount|Starting Node Count|Count|Итого|Число узлов, которые запускаются.|Нет измерений|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Итого|Число узлов, ожидающих завершения задачи запуска.|Нет измерений|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Итого|Число узлов, на которых произошел сбой задачи запуска.|Нет измерений|
|IdleNodeCount|Idle Node Count|Count|Итого|Количество узлов в неактивном состоянии.|Нет измерений|
|OfflineNodeCount|Offline Node Count|Count|Всего|Количество узлов не в сети.|Нет измерений|
|RebootingNodeCount|Rebooting Node Count|Count|Итого|Количество перезапускаемых узлов.|Нет измерений|
|ReimagingNodeCount|Reimaging Node Count|Count|Итого|Число узлов, для которых пересоздается образ.|Нет измерений|
|RunningNodeCount|Running Node Count|Count|Итого|Число работающих узлов.|Нет измерений|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Итого|Количество узлов, покидающих пул.|Нет измерений|
|UnusableNodeCount|Unusable Node Count|Count|Итого|Число узлов, непригодных для использования.|Нет измерений|
|PreemptedNodeCount|Preempted Node Count|Count|Итого|Количество замещенных узлов.|Нет измерений|
|TaskStartEvent|Task Start Events|Count|Итого|Общее число запущенных задач.|Нет измерений|
|TaskCompleteEvent|Task Complete Events|Count|Итого|Общее число завершенных задач.|Нет измерений|
|TaskFailEvent|Task Fail Events|Count|Итого|Общее число задач, завершившихся сбоем.|Нет измерений|
|PoolCreateEvent|Pool Create Events|Count|Итого|Общее число созданных пулов.|Нет измерений|
|PoolResizeStartEvent|Pool Resize Start Events|Count|Итого|Общее число запущенных задач изменения размера пула.|Нет измерений|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Count|Итого|Общее число завершенных задач изменения размера пула.|Нет измерений|
|PoolDeleteStartEvent|Pool Delete Start Events|Count|Итого|Общее число запущенных задач удаления пула.|Нет измерений|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Count|Итого|Общее число завершенных задач удаления пула.|Нет измерений|
|JobDeleteCompleteEvent|Job Delete Complete Events (События окончания удаления задания)|Count|Итого|Общее количество заданий, которые были успешно удалены.|Нет измерений|
|JobDeleteStartEvent|Job Delete Start Events (События начала удаления задания)|Count|Итого|Общее количество заданий, для которых запрошено удаление.|Нет измерений|
|JobDisableCompleteEvent|Job Disable Complete Events (События окончания отключения задания)|Count|Итого|Общее количество заданий, которые были успешно отключены.|Нет измерений|
|JobDisableStartEvent|Job Disable Start Events (События начала отключения задания)|Count|Итого|Общее количество заданий, для которых запрошено отключение.|Нет измерений|
|JobStartEvent|Job Start Events (События запуска задания)|Count|Итого|Общее количество заданий, которые были успешно запущены.|Нет измерений|
|JobTerminateCompleteEvent|Job Terminate Complete Events (События окончания завершения задания)|Count|Итого|Общее количество заданий, которые были успешно завершены.|Нет измерений|
|JobTerminateStartEvent|Job Terminate Start Events (События начала завершения задания)|Count|Итого|Общее количество заданий, для которых запрошено завершение.|Нет измерений|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Count|Максимальная||ShardId|
|totalcommandsprocessed|Всего операций|Count|Итого||ShardId|
|cachehits|Попаданий в кэш|Count|Итого||ShardId|
|cachemisses|Промахов в кэше|Count|Итого||ShardId|
|getcommands|Операций считывания|Count|Итого||ShardId|
|setcommands|Операций записи|Count|Итого||ShardId|
|operationsPerSecond|Количество операций в секунду|Count|Максимальная||ShardId|
|evictedkeys|Исключенные ключи|Count|Итого||ShardId|
|totalkeys|Всего ключей|Count|Максимальная||ShardId|
|expiredkeys|Ключи с истекшим сроком действия|Count|Итого||ShardId|
|usedmemory|Используемая память|Байты|Максимальная||ShardId|
|usedmemorypercentage|Процент используемой памяти|Percent|Максимальная||ShardId|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||ShardId|
|serverLoad|Загрузка сервера|Percent|Максимальная||ShardId|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||ShardId|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||ShardId|
|percentProcessorTime|ЦП|Percent|Максимальная||ShardId|
|cacheLatency|Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))|Count|Average||ShardId, SampleType|
|errors|Errors|Count|Максимальная||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Итого||Нет измерений|
|cachehits0|Cache Hits (Shard 0)|Count|Итого||Нет измерений|
|cachemisses0|Cache Misses (Shard 0)|Count|Итого||Нет измерений|
|getcommands0|Gets (Shard 0)|Count|Итого||Нет измерений|
|setcommands0|Sets (Shard 0)|Count|Итого||Нет измерений|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Count|Максимальная||Нет измерений|
|evictedkeys0|Evicted Keys (Shard 0)|Count|Итого||Нет измерений|
|totalkeys0|Total Keys (Shard 0)|Count|Максимальная||Нет измерений|
|expiredkeys0|Expired Keys (Shard 0)|Count|Итого||Нет измерений|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||Нет измерений|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||Нет измерений|
|serverLoad0|Server Load (Shard 0)|Percent|Максимальная||Нет измерений|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||Нет измерений|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime0|CPU (Shard 0)|Percent|Максимальная||Нет измерений|
|connectedclients1|Connected Clients (Shard 1)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Итого||Нет измерений|
|cachehits1|Cache Hits (Shard 1)|Count|Итого||Нет измерений|
|cachemisses1|Cache Misses (Shard 1)|Count|Итого||Нет измерений|
|getcommands1|Gets (Shard 1)|Count|Итого||Нет измерений|
|getcommands1|Sets (Shard 1)|Count|Итого||Нет измерений|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Count|Максимальная||Нет измерений|
|evictedkeys1|Evicted Keys (Shard 1)|Count|Итого||Нет измерений|
|totalkeys1|Total Keys (Shard 1)|Count|Максимальная||Нет измерений|
|expiredkeys1|Expired Keys (Shard 1)|Count|Итого||Нет измерений|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||Нет измерений|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||Нет измерений|
|serverLoad1|Server Load (Shard 1)|Percent|Максимальная||Нет измерений|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||Нет измерений|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime1|CPU (Shard 1)|Percent|Максимальная||Нет измерений|
|connectedclients2|Connected Clients (Shard 2)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Итого||Нет измерений|
|cachehits2|Cache Hits (Shard 2)|Count|Итого||Нет измерений|
|cachemisses2|Cache Misses (Shard 2)|Count|Итого||Нет измерений|
|getcommands2|Gets (Shard 2)|Count|Итого||Нет измерений|
|getcommands2|Sets (Shard 2)|Count|Итого||Нет измерений|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Count|Максимальная||Нет измерений|
|evictedkeys2|Evicted Keys (Shard 2)|Count|Итого||Нет измерений|
|totalkeys2|Total Keys (Shard 2)|Count|Максимальная||Нет измерений|
|expiredkeys2|Expired Keys (Shard 2)|Count|Итого||Нет измерений|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||Нет измерений|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||Нет измерений|
|serverLoad2|Server Load (Shard 2)|Percent|Максимальная||Нет измерений|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||Нет измерений|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime2|CPU (Shard 2)|Percent|Максимальная||Нет измерений|
|connectedclients3|Connected Clients (Shard 3)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Итого||Нет измерений|
|cachehits3|Cache Hits (Shard 3)|Count|Итого||Нет измерений|
|cachemisses3|Cache Misses (Shard 3)|Count|Итого||Нет измерений|
|getcommands3|Gets (Shard 3)|Count|Итого||Нет измерений|
|setcommands3|Sets (Shard 3)|Count|Итого||Нет измерений|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Count|Максимальная||Нет измерений|
|evictedkeys3|Evicted Keys (Shard 3)|Count|Итого||Нет измерений|
|totalkeys3|Total Keys (Shard 3)|Count|Максимальная||Нет измерений|
|expiredkeys3|Expired Keys (Shard 3)|Count|Итого||Нет измерений|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||Нет измерений|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||Нет измерений|
|serverLoad3|Server Load (Shard 3)|Percent|Максимальная||Нет измерений|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||Нет измерений|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime3|CPU (Shard 3)|Percent|Максимальная||Нет измерений|
|connectedclients4|Connected Clients (Shard 4)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Итого||Нет измерений|
|cachehits4|Cache Hits (Shard 4)|Count|Итого||Нет измерений|
|cachemisses4|Cache Misses (Shard 4)|Count|Итого||Нет измерений|
|getcommands4|Gets (Shard 4)|Count|Итого||Нет измерений|
|setcommands4|Sets (Shard 4)|Count|Итого||Нет измерений|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Count|Максимальная||Нет измерений|
|evictedkeys4|Evicted Keys (Shard 4)|Count|Итого||Нет измерений|
|totalkeys4|Total Keys (Shard 4)|Count|Максимальная||Нет измерений|
|expiredkeys4|Expired Keys (Shard 4)|Count|Итого||Нет измерений|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||Нет измерений|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||Нет измерений|
|serverLoad4|Server Load (Shard 4)|Percent|Максимальная||Нет измерений|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||Нет измерений|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime4|CPU (Shard 4)|Percent|Максимальная||Нет измерений|
|connectedclients5|Connected Clients (Shard 5)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Итого||Нет измерений|
|cachehits5|Cache Hits (Shard 5)|Count|Итого||Нет измерений|
|cachemisses5|Cache Misses (Shard 5)|Count|Итого||Нет измерений|
|getcommands5|Gets (Shard 5)|Count|Итого||Нет измерений|
|getcommands5|Sets (Shard 5)|Count|Итого||Нет измерений|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Count|Максимальная||Нет измерений|
|evictedkeys5|Evicted Keys (Shard 5)|Count|Итого||Нет измерений|
|totalkeys5|Total Keys (Shard 5)|Count|Максимальная||Нет измерений|
|expiredkeys5|Expired Keys (Shard 5)|Count|Итого||Нет измерений|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||Нет измерений|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||Нет измерений|
|serverLoad5|Server Load (Shard 5)|Percent|Максимальная||Нет измерений|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||Нет измерений|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime5|CPU (Shard 5)|Percent|Максимальная||Нет измерений|
|connectedclients6|Connected Clients (Shard 6)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Итого||Нет измерений|
|cachehits6|Cache Hits (Shard 6)|Count|Итого||Нет измерений|
|cachemisses6|Cache Misses (Shard 6)|Count|Итого||Нет измерений|
|getcommands6|Gets (Shard 6)|Count|Итого||Нет измерений|
|setcommands6|Sets (Shard 6)|Count|Итого||Нет измерений|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Count|Максимальная||Нет измерений|
|evictedkeys6|Evicted Keys (Shard 6)|Count|Итого||Нет измерений|
|totalkeys6|Total Keys (Shard 6)|Count|Максимальная||Нет измерений|
|expiredkeys6|Expired Keys (Shard 6)|Count|Итого||Нет измерений|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||Нет измерений|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||Нет измерений|
|serverLoad6|Server Load (Shard 6)|Percent|Максимальная||Нет измерений|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||Нет измерений|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime6|CPU (Shard 6)|Percent|Максимальная||Нет измерений|
|connectedclients7|Connected Clients (Shard 7)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Итого||Нет измерений|
|cachehits7|Cache Hits (Shard 7)|Count|Итого||Нет измерений|
|cachemisses7|Cache Misses (Shard 7)|Count|Итого||Нет измерений|
|getcommands7|Gets (Shard 7)|Count|Итого||Нет измерений|
|setcommands7|Sets (Shard 7)|Count|Итого||Нет измерений|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Count|Максимальная||Нет измерений|
|evictedkeys7|Evicted Keys (Shard 7)|Count|Итого||Нет измерений|
|totalkeys7|Total Keys (Shard 7)|Count|Максимальная||Нет измерений|
|expiredkeys7|Expired Keys (Shard 7)|Count|Итого||Нет измерений|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||Нет измерений|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||Нет измерений|
|serverLoad7|Server Load (Shard 7)|Percent|Максимальная||Нет измерений|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||Нет измерений|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime7|CPU (Shard 7)|Percent|Максимальная||Нет измерений|
|connectedclients8|Connected Clients (Shard 8)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Итого||Нет измерений|
|cachehits8|Cache Hits (Shard 8)|Count|Итого||Нет измерений|
|cachemisses8|Cache Misses (Shard 8)|Count|Итого||Нет измерений|
|getcommands8|Gets (Shard 8)|Count|Итого||Нет измерений|
|setcommands8|Sets (Shard 8)|Count|Итого||Нет измерений|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Count|Максимальная||Нет измерений|
|evictedkeys8|Evicted Keys (Shard 8)|Count|Итого||Нет измерений|
|totalkeys8|Total Keys (Shard 8)|Count|Максимальная||Нет измерений|
|expiredkeys8|Expired Keys (Shard 8)|Count|Итого||Нет измерений|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||Нет измерений|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||Нет измерений|
|serverLoad8|Server Load (Shard 8)|Percent|Максимальная||Нет измерений|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||Нет измерений|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime8|CPU (Shard 8)|Percent|Максимальная||Нет измерений|
|connectedclients9|Connected Clients (Shard 9)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Итого||Нет измерений|
|cachehits9|Cache Hits (Shard 9)|Count|Итого||Нет измерений|
|cachemisses9|Cache Misses (Shard 9)|Count|Итого||Нет измерений|
|getcommands9|Gets (Shard 9)|Count|Итого||Нет измерений|
|setcommands9|Sets (Shard 9)|Count|Итого||Нет измерений|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Count|Максимальная||Нет измерений|
|evictedkeys9|Evicted Keys (Shard 9)|Count|Итого||Нет измерений|
|totalkeys9|Total Keys (Shard 9)|Count|Максимальная||Нет измерений|
|expiredkeys9|Expired Keys (Shard 9)|Count|Итого||Нет измерений|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||Нет измерений|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||Нет измерений|
|serverLoad9|Server Load (Shard 9)|Percent|Максимальная||Нет измерений|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||Нет измерений|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime9|CPU (Shard 9)|Percent|Максимальная||Нет измерений|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Average|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Average|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Average|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Average|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Итого|Общее число вызовов.|ApiName Имя_операции, регион|
|SuccessfulCalls|Successful Calls|Count|Итого|Число успешных вызовов.|ApiName Имя_операции, регион|
|TotalErrors|Total Errors|Count|Итого|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName Имя_операции, регион|
|BlockedCalls|Blocked Calls|Count|Итого|Число вызовов, превысивших ограничение скорости или квоты.|ApiName Имя_операции, регион|
|ServerErrors|Server Errors|Count|Итого|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName Имя_операции, регион|
|ClientErrors|Client Errors|Count|Итого|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName Имя_операции, регион|
|DataIn|Входящие данные:|Байты|Итого|Размер входящих данных в байтах.|ApiName Имя_операции, регион|
|DataOut|Выходные данные|Байты|Итого|Размер исходящих данных в байтах.|ApiName Имя_операции, регион|
|Задержка|Задержка|MilliSeconds|Average|Время задержки в миллисекундах.|ApiName Имя_операции, регион|
|CharactersTranslated|Преобразованные символы|Count|Итого|Общее количество символов во входящем текстовом запросе.|ApiName Имя_операции, регион|
|CharactersTrained|Обучение символов|Count|Итого|Общее число символов для обучения.|ApiName Имя_операции, регион|
|SpeechSessionDuration|Длительность речи|Секунды|Итого|Общая длительность речи в секундах.|ApiName Имя_операции, регион|
|TotalTransactions|Всего транзакций|Count|Итого|Общее число транзакций.|Нет измерений|
|TotalTokenCalls|Всего вызовов токенов|Count|Итого|Общее число вызовов токенов.|ApiName Имя_операции, регион|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть в оплаты|Байты|Итого|Число оплачиваемых байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик)|Нет измерений|
|Сеть (исходящий трафик)|Оплачиваемые вне сети|Байты|Итого|Количество оплачиваемых байтов, отправленных через все сетевые интерфейсы виртуальных машин (исходящий трафик)|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Байт, считанных с диска за период мониторинга|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Байтов, записанных на диск за период мониторинга|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Average|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Average|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Диск данных Прочитано байт/с (устаревшая версия)|Число/с|Average|Байт/с, считанных из одного диска за период мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Данные диска записано байт/с (устаревшая версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Диск данных операций чтения/сек (устаревшая версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска за период мониторинга.|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Данные записи на диск операций/с (устаревшая версия)|Число/с|Average|Число операций IOPS записи с одного диска за период мониторинга.|ИД слота|
|Длина очереди в расчете на диск|(Устарело) длина Очереди дисков данных|Count|Average|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Диск операционной системы Прочитано байт/с (устаревшая версия)|Число/с|Average|Чтение из одного диска во время мониторинга для диска операционной системы байт/с|Нет измерений|
|Скорость записи на диск ОС (байт/с)|Диск операционной системы записано байт/с (устаревшая версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга для диска операционной системы|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|Диск операционной системы операций чтения/сек (устаревшая версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Скорость записи на диск ОС (операций/с)|Диск операционной системы операций записи/сек (устаревшая версия)|Число/с|Average|Число операций IOPS записи с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Длина очереди на диск ОС|(Устарело) длина Очереди дисков ОС|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Диск данных Прочитано байт/с|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Average|Байт/с, считанных из одного диска за период мониторинга|LUN|
|Записи данных на диск байтов/с|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга|LUN|
|Диск данных операций чтения/сек|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска за период мониторинга.|LUN|
|Записи данных на диск операций/с|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Average|Число операций IOPS записи с одного диска за период мониторинга.|LUN|
|Длина очереди дисков данных|Длина очереди дисков данных (Предварительная версия)|Count|Average|Длина очереди дисков данных|LUN|
|Диск операционной системы Прочитано байт/с|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Average|Чтение из одного диска во время мониторинга для диска операционной системы байт/с|Нет измерений|
|Диск операционной системы записано байт/с|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга для диска операционной системы|Нет измерений|
|Диск операционной системы операций чтения/сек|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Диск операционной системы операций записи/сек|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Average|Число операций IOPS записи с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Длина очереди дисков ОС|Длина очереди дисков ОС (Предварительная версия)|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Входящие потоки|Входящие потоки (Предварительная версия)|Count|Average|Число входящих потоков из текущие потоки для входящего трафика (трафик, передаваемый в виртуальную Машину)|Нет измерений|
|Исходящие потоки|Исходящие потоки (Предварительная версия)|Count|Average|Число исходящих потоков из текущие потоки в исходящем направлении (трафик, передаваемый из виртуальной Машины)|Нет измерений|
|Скорость максимальное создания входящие потоки|Скорость максимальное создания входящие потоки (Предварительная версия)|Число/с|Average|Скорость создания максимальное входящие потоки (трафик, передаваемый в виртуальную Машину)|Нет измерений|
|Скорость максимальное создания исходящих потоков|Скорость максимальное создания исходящих потоков (Предварительная версия)|Число/с|Average|Создания Максимальная скорость исходящих потоков (трафик, передаваемый из виртуальной Машины)|Нет измерений|
|Попадание чтения в кэш диска данных уровня "премиум"|Чтение из кэша диска данных уровня "премиум" попадания (Предварительная версия)|Percent|Average|Попадание чтения в кэш диска данных уровня "премиум"|LUN|
|"Премиум" диск чтения промахов кэша|Промах чтения кэша диска данных уровня "премиум" (Предварительная версия)|Percent|Average|"Премиум" диск чтения промахов кэша|LUN|
|Попаданий чтение из кэша уровня "премиум" диск операционной системы|Чтение из кэша диска операционной системы "премиум" попадания (Предварительная версия)|Percent|Average|Попаданий чтение из кэша уровня "премиум" диск операционной системы|Нет измерений|
|Промах чтения диска кэша для уровня "премиум" операционной системы|Промах чтения кэша диска операционной системы "премиум" (Предварительная версия)|Percent|Average|Промах чтения диска кэша для уровня "премиум" операционной системы|Нет измерений|
|Сети в целом|Сети в целом|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сети всего исходящих|Сети всего исходящих|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Сеть (входящий трафик)|Сеть в оплаты|Байты|Итого|Число оплачиваемых байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик)|VMName|
|Сеть (исходящий трафик)|Оплачиваемые вне сети|Байты|Итого|Количество оплачиваемых байтов, отправленных через все сетевые интерфейсы виртуальных машин (исходящий трафик)|VMName|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Байт, считанных с диска за период мониторинга|VMName|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Байтов, записанных на диск за период мониторинга|VMName|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при чтении с диска.|VMName|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Average|Количество операций вода-вывода в секунду при записи на диск.|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Average|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Average|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Диск данных Прочитано байт/с (устаревшая версия)|Число/с|Average|Байт/с, считанных из одного диска за период мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Данные диска записано байт/с (устаревшая версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Диск данных операций чтения/сек (устаревшая версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска за период мониторинга.|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Данные записи на диск операций/с (устаревшая версия)|Число/с|Average|Число операций IOPS записи с одного диска за период мониторинга.|ИД слота|
|Длина очереди в расчете на диск|(Устарело) длина Очереди дисков данных|Count|Average|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Диск операционной системы Прочитано байт/с (устаревшая версия)|Число/с|Average|Чтение из одного диска во время мониторинга для диска операционной системы байт/с|Нет измерений|
|Скорость записи на диск ОС (байт/с)|Диск операционной системы записано байт/с (устаревшая версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга для диска операционной системы|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|Диск операционной системы операций чтения/сек (устаревшая версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Скорость записи на диск ОС (операций/с)|Диск операционной системы операций записи/сек (устаревшая версия)|Число/с|Average|Число операций IOPS записи с одного диска во время мониторинга для диска операционной системы|Нет измерений|
|Длина очереди на диск ОС|(Устарело) длина Очереди дисков ОС|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Диск данных Прочитано байт/с|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Average|Байт/с, считанных из одного диска за период мониторинга|LUN, VMName|
|Записи данных на диск байтов/с|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга|LUN, VMName|
|Диск данных операций чтения/сек|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска за период мониторинга.|LUN, VMName|
|Записи данных на диск операций/с|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Average|Число операций IOPS записи с одного диска за период мониторинга.|LUN, VMName|
|Длина очереди дисков данных|Длина очереди дисков данных (Предварительная версия)|Count|Average|Длина очереди дисков данных|LUN, VMName|
|Диск операционной системы Прочитано байт/с|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Average|Чтение из одного диска во время мониторинга для диска операционной системы байт/с|VMName|
|Диск операционной системы записано байт/с|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Average|Байт/с, записанных на один диск во время мониторинга для диска операционной системы|VMName|
|Диск операционной системы операций чтения/сек|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Average|Чтение операций ввода-ВЫВОДА, с одного диска во время мониторинга для диска операционной системы|VMName|
|Диск операционной системы операций записи/сек|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Average|Число операций IOPS записи с одного диска во время мониторинга для диска операционной системы|VMName|
|Длина очереди дисков ОС|Длина очереди дисков ОС (Предварительная версия)|Count|Average|Длина очереди дисков ОС|VMName|
|Входящие потоки|Входящие потоки (Предварительная версия)|Count|Average|Число входящих потоков из текущие потоки для входящего трафика (трафик, передаваемый в виртуальную Машину)|VMName|
|Исходящие потоки|Исходящие потоки (Предварительная версия)|Count|Average|Число исходящих потоков из текущие потоки в исходящем направлении (трафик, передаваемый из виртуальной Машины)|VMName|
|Скорость максимальное создания входящие потоки|Скорость максимальное создания входящие потоки (Предварительная версия)|Число/с|Average|Скорость создания максимальное входящие потоки (трафик, передаваемый в виртуальную Машину)|VMName|
|Скорость максимальное создания исходящих потоков|Скорость максимальное создания исходящих потоков (Предварительная версия)|Число/с|Average|Создания Максимальная скорость исходящих потоков (трафик, передаваемый из виртуальной Машины)|VMName|
|Попадание чтения в кэш диска данных уровня "премиум"|Чтение из кэша диска данных уровня "премиум" попадания (Предварительная версия)|Percent|Average|Попадание чтения в кэш диска данных уровня "премиум"|LUN, VMName|
|"Премиум" диск чтения промахов кэша|Промах чтения кэша диска данных уровня "премиум" (Предварительная версия)|Percent|Average|"Премиум" диск чтения промахов кэша|LUN, VMName|
|Попаданий чтение из кэша уровня "премиум" диск операционной системы|Чтение из кэша диска операционной системы "премиум" попадания (Предварительная версия)|Percent|Average|Попаданий чтение из кэша уровня "премиум" диск операционной системы|VMName|
|Промах чтения диска кэша для уровня "премиум" операционной системы|Промах чтения кэша диска операционной системы "премиум" (Предварительная версия)|Percent|Average|Промах чтения диска кэша для уровня "премиум" операционной системы|VMName|
|Сети в целом|Сети в целом|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Сети всего исходящих|Сети всего исходящих|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Count|Average|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Average|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байты|Average|Количество байт, полученных по сети в секунду.|Нет измерений|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байты|Average|Количество байт, переданных по сети в секунду.|Нет измерений|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalPullCount|Число всего на включение внесенных изменений|Count|Average|Извлекает число изображений в целом|Нет измерений|
|SuccessfulPullCount|Число успешных по запросу|Count|Average|Число успешно образа переносит|Нет измерений|
|TotalPushCount|Число общее Push-уведомлений|Count|Average|Количество изображений, Push-уведомлений в целом|Нет измерений|
|SuccessfulPushCount|Число успешно отправлены|Count|Average|Количество успешных образа Push-уведомлений|Нет измерений|
|Параметр RunDuration|Длительность выполнения|Миллисекунды|Итого|Длительность выполнения в миллисекундах|Нет измерений|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Count|Итого|Общее количество доступных ядер ЦП в управляемом кластере|Нет измерений|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байты|Итого|Общий объем доступной памяти в управляемом кластере|Нет измерений|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Count|Итого|Число модулей pod в состоянии готовности|пространство имен, pod|
|kube_node_status_condition|Состояния для различных условий узла|Count|Итого|Состояния для различных условий узла|Условие "," состояние "," status2 "," узел|
|kube_pod_status_phase|Число модулей pod по фазам|Count|Итого|Число модулей pod по фазам|фаза, пространство имен, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Count|Итого||Нет измерений|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Count|Итого||Нет измерений|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Count|Итого||Нет измерений|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Count|Итого||Нет измерений|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Итого||Нет измерений|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Count|Итого||Нет измерений|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Count|Итого||Нет измерений|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Итого||Нет измерений|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Count|Итого||Нет измерений|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Count|Итого||Нет измерений|
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
|ImportASAValuesFailed|Import ASA Values Failed Count|Count|Итого||Нет измерений|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Count|Итого||Нет измерений|
|DCIProfilesCount|Количество экземпляров профиля|Count|Последняя||Нет измерений|
|DCIInteractionsPerMonthCount|Количество взаимодействий в месяц|Count|Последняя||Нет измерений|
|DCIKpisCount|Количество ключевых показателей эффективности|Count|Последняя||Нет измерений|
|DCISegmentsCount|Количество сегментов|Count|Последняя||Нет измерений|
|DCIPredictiveMatchPoliciesCount|Количество операций прогнозного сопоставления|Count|Последняя||Нет измерений|
|DCIPredictionsCount|Количество прогнозов|Count|Последняя||Нет измерений|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|NICReadThroughput|Скорость чтения (сеть)|Байт/с|Average|Пропускная способность чтения сетевого интерфейса на устройстве за отчетный период для всех томов в шлюзе.|InstanceName|
|NICWriteThroughput|Записываются пропускная способность (сеть)|Байт/с|Average|Пропускная способность записи сетевого интерфейса на устройстве за отчетный период для всех томов в шлюзе.|InstanceName|
|CloudReadThroughputPerShare|Пропускная способность загрузки облака (общий)|Байт/с|Average|Производительность загрузки в Azure из общей папки в течение отчетного периода.|Share (Предоставить общий доступ)|
|CloudUploadThroughputPerShare|Пропускная способность отправки для облака (общий)|Байт/с|Average|Пропускной способности передачи в Azure из общей папки в течение отчетного периода.|Share (Предоставить общий доступ)|
|BytesUploadedToCloudPerShare|Отправленных байт облака (общий)|Байты|Average|Общее число байтов, отправляется в Azure из общей папки, в течение отчетного периода.|Share (Предоставить общий доступ)|
|Общая емкость|Общая емкость|Байты|Average|Общая емкость|Нет измерений|
|AvailableCapacity|Доступная емкость|Байты|Average|Доступная емкость в байтах в течение отчетного периода.|Нет измерений|
|CloudUploadThroughput|Пропускная способность отправки для облака|Байт/с|Average|Облако пропускной способности передачи в Azure в течение отчетного периода.|Нет измерений|
|CloudReadThroughput|Пропускная способность облака загрузки|Байт/с|Average|Облако загрузки пропускной способности в Azure в течение отчетного периода.|Нет измерений|
|BytesUploadedToCloud|Облака. отправленных байт (устройство)|Байты|Average|Общее число байтов, переданных в Azure с устройства в течение отчетного периода.|Нет измерений|
|HyperVVirtualProcessorUtilization|Граничные вычисления — загрузка ЦП|Percent|Average|Процент использования ЦП|InstanceName|
|HyperVMemoryUtilization|Граничный узел вычислений — использование памяти|Percent|Average|Объем оперативной памяти|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Count|Итого||pipelineName, activityName|
|SuccessfulRuns.|успешные циклы выполнения.|Count|Итого||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Count|Итого||FailureType, Name|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Count|Итого||FailureType, Name|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Count|Итого||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Count|Итого||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Count|Итого||Name, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Count|Итого||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Сущности максимальное допустимое число|Count|Максимальная||Нет измерений|
|MaxAllowedFactorySizeInGbUnits|Максимальное количество фабрики размер (ГБ на единицу)|Count|Максимальная||Нет измерений|
|Число ресурсов|Число сущностей|Count|Максимальная||Нет измерений|
|FactorySizeInGbUnits|Фабрики общий размер (ГБ на единицу)|Count|Максимальная||Нет измерений|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Итого|Количество успешно выполненных заданий.|Нет измерений|
|JobEndedFailure|Failed Jobs|Count|Итого|Количество невыполненных заданий.|Нет измерений|
|JobEndedCancelled|Отмененные задания|Count|Итого|Количество отмененных заданий.|Нет измерений|
|JobAUEndedSuccess|Successful AU Time|Секунды|Итого|Общее время AU успешно выполненных заданий.|Нет измерений|
|JobAUEndedFailure|Failed AU Time|Секунды|Итого|Общее время AU невыполненных заданий.|Нет измерений|
|JobAUEndedCancelled|Отмененные время AU|Секунды|Итого|Общее время AU отмененных заданий.|Нет измерений|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|Нет измерений|
|DataWritten|Записанные данные|Байты|Итого|Общий объем данных, записанных в учетной записи.|Нет измерений|
|DataRead|Данных прочитано|Байты|Итого|Общий объем данных, прочитанных в учетной записи.|Нет измерений|
|WriteRequests|Запросы на запись|Count|Итого|Количество запросов на запись данных в учетную запись.|Нет измерений|
|ReadRequests|Запросы на чтение|Count|Итого|Количество запросов на чтение данных для учетной записи.|Нет измерений|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Storage used|Байты|Average|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Итого|Неудачные подключения|Нет измерений|
|seconds_behind_master|Задержка репликации в секундах|Count|Average|Задержка репликации в секундах|Нет измерений|
|backup_storage_used|Объем хранилища резервных копий|Байты|Average|Объем хранилища резервных копий|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Storage used|Байты|Average|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Итого|Неудачные подключения|Нет измерений|
|seconds_behind_master|Задержка репликации в секундах|Count|Average|Задержка репликации в секундах|Нет измерений|
|backup_storage_used|Объем хранилища резервных копий|Байты|Average|Объем хранилища резервных копий|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Storage used|Байты|Average|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Итого|Неудачные подключения|Нет измерений|
|backup_storage_used|Объем хранилища резервных копий|Байты|Average|Объем хранилища резервных копий|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимальная|Lag реплики в секундах|Нет измерений|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|Максимальная|Задержки в байтах наиболее запаздывания реплики|Нет измерений|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|операций ввода-вывода|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Count|Average|Операций ввода-ВЫВОДА в секунду|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Storage used|Байты|Average|Storage used|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итого|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итого|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Итого|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Итого|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Count|Итого|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Count|Итого|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Count|Итого|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Count|Итого|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Count|Итого|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Count|Итого|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|Нет измерений|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Count|Итого|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|Нет измерений|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Count|Итого|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |Нет измерений|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Count|Итого|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|Нет измерений|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|Миллисекунды|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|Миллисекунды|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|Миллисекунды|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events). Эта метрика только начинает работать, когда включена маршрутизация (https://aka.ms/iotrouting) для центра Интернета вещей.|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|Миллисекунды|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events). Эта метрика только начинает работать, когда включена маршрутизация (https://aka.ms/iotrouting) для центра Интернета вещей.|Нет измерений|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|Миллисекунды|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байты|Итого|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Count|Итого|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|Нет измерений|
|EventGridDeliveries|Доставки сетки событий (Предварительная версия)|Count|Итого|Число событий, центр Интернета вещей опубликованы "Сетка событий". Используйте размерность результата для числа успешных и неудачных запросах. EventType измерения показывает тип события (https://aka.ms/ioteventgrid).|Результат, тип события|
|EventGridLatency|Средняя задержка (в миллисекундах) из центра Интернета вещей событие создания для при публикации события "Сетка событий". Это число — это среднее между все типы событий. EventType измерение используется для см. в разделе задержки определенного типа события.|EventType|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Итого|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Итого|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Average|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Итого|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Итого|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Average|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Итого|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Итого|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Average|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Average|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Итого|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Итого|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Average|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Итого|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Итого|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Average|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Count|Итого|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Count|Итого|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Average|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Итого|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Итого|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Итого|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Итого|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Итого|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Итого|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Итого|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Итого|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Итого|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Итого|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Count|Итого|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Count|Итого|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Итого|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Average|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|
|deviceDataUsage|Использование данных всего устройств|Байты|Итого|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Нет измерений|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Count|Average|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Count|Average|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Count|Итого|Метрики для операций конфигурации|Нет измерений|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Count|Итого|Количество попыток регистрации устройств|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Назначенные устройства|Count|Итого|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Попытки аттестации|Count|Итого|Количество попыток аттестации устройств|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AvailableStorage|Доступная служба хранилища|Байты|Итого|Общее доступное хранилище, сообщаемые точностью до 5 минут|Регион CollectionName, имя базы данных,|
|CassandraConnectionClosures|Отключение связи Cassandra|Count|Итого|Число подключений Cassandra, которые были закрыты, сообщаемые точностью до 1 минуты|Регион, ClosureReason|
|CassandraRequestCharges|Расходы запросов по Cassandra|Count|Итого|Единицах запроса для запросов в Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Запросы по Cassandra|Count|Count|Количество выполненных запросов Cassandra|Имя базы данных, CollectionName, регион, OperationType, типа ресурса, код ошибки|
|DataUsage|Использование данных|Байты|Итого|Общий объем данных, оплату точностью до 5 минут|Регион CollectionName, имя базы данных,|
|DocumentCount|Число документов|Count|Итого|Число документов общее сообщил точностью до 5 минут|Регион CollectionName, имя базы данных,|
|DocumentQuota|Квота на документы|Байты|Итого|Квота общий объем хранилища, сообщаемые точностью до 5 минут|Регион CollectionName, имя базы данных,|
|IndexUsage|Использование индексов|Байты|Итого|Всего индекса данные об использовании точностью до 5 минут|Регион CollectionName, имя базы данных,|
|MetadataRequests|Запросы метаданных|Count|Count|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|Имя базы данных, CollectionName, регион, StatusCode, |
|MongoRequestCharge|Запросить оплату Mongo|Count|Итого|Использованные единицы запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Запросы Mongo|Count|Count|Количество выполненных запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Подготовленная пропускная способность|Count|Максимальная|Подготовленная пропускная способность|DatabaseName, CollectionName|
|ReplicationLatency|P99 Задержка репликации|MilliSeconds|Average|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|SourceRegion, TargetRegion|
|ServiceAvailability|Доступность службы|Percent|Average|Учетная запись доступности запросов на уровне детализации один час, день или месяц|Нет измерений|
|TotalRequestUnits|Общее количество единиц запросов|Count|Итого|Использованные единицы запросов|Имя базы данных, CollectionName, регион, StatusCode, OperationType|
|TotalRequests|Общее количество запросов|Count|Count|Количество выполненных запросов|Имя базы данных, CollectionName, регион, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итого|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|Публиковать события с ошибками|Count|Итого|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итого|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|PublishSuccessLatencyInMs|Публикация задержка успешного запроса|Count|Итого|Публикация задержка успешного запроса в миллисекундах|Нет измерений|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Count|Итого|Всего событий, которые соответствуют этой подписке на события|Нет измерений|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итого|Всего событий, которые не удалось доставить в эту подписку на события|Error, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итого|Всего событий, доставленных в эту подписку на события|Нет измерений|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Average|Длительность обработки назначения в миллисекундах|Нет измерений|
|DroppedEventCount|Удаленные события|Count|Итого|Всего удаленных событий, которые соответствуют этой подписке на события|DropReason|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итого|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итого|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|События с ошибками|Count|Итого|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итого|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|PublishSuccessLatencyInMs|Публикация задержка успешного запроса|Count|Итого|Публикация задержка успешного запроса в миллисекундах|Нет измерений|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итого|Выполненные запросы для Microsoft.EventHub.|EntityName, |
|ServerErrors|Ошибки сервера.|Count|Итого|Ошибки на сервере для Microsoft.EventHub.|EntityName, |
|UserErrors|Ошибки пользователей.|Count|Итого|Ошибки пользователей для Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Итого|Ошибки превышения квоты для Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Регулируемые запросы.|Count|Итого|Регулируемые запросы для Microsoft.EventHub.|EntityName, |
|IncomingRequests|Входящих запросов|Count|Итого|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Итого|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Итого|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байты|Итого|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байты|Итого|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Число активных подключений для Microsoft.EventHub.|Нет измерений|
|ConnectionsOpened|Открытые подключения.|Count|Average|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Average|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Count|Итого|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Count|Итого|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байты|Итого|Записанные байты для Microsoft.EventHub.|EntityName|
|Size|Size|Байты|Average|Размер EventHub в байтах.|EntityName|
|INREQS|Входящие запросы (устаревшая версия)|Count|Итого|Общее количество входящих запросов на отправку для пространства имен (не рекомендуется)|Нет измерений|
|SUCCREQ|Количество успешных запросов (устаревшая версия)|Count|Итого|Общее число успешных запросов для пространства имен (не рекомендуется)|Нет измерений|
|FAILREQ|Неудачных запросов (устаревшая версия)|Count|Итого|Общее количество неудачных запросов для пространства имен (не рекомендуется)|Нет измерений|
|SVRBSY|Сервер занят ошибки (устаревшая версия)|Count|Итого|Общее количество занятых ошибок сервера для пространства имен (не рекомендуется)|Нет измерений|
|INTERR|Внутренние ошибки сервера (устаревшая версия)|Count|Итого|Общее количество внутренних ошибок сервера для пространства имен (не рекомендуется)|Нет измерений|
|MISCERR|Другие ошибки (устаревшая версия)|Count|Итого|Общее количество неудачных запросов для пространства имен (не рекомендуется)|Нет измерений|
|INMSGS|Входящих сообщений (устаревшая) (устарело)|Count|Итого|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику входящих сообщений (устаревшая версия)|Нет измерений|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Count|Итого|Общее количество входящих сообщений для пространства имен (не рекомендуется)|Нет измерений|
|OUTMSGS|Исходящих сообщений (устаревшая) (устарело)|Count|Итого|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику исходящих сообщений (устаревшая версия)|Нет измерений|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Count|Итого|Общее число исходящих сообщений для пространства имен (не рекомендуется)|Нет измерений|
|EHINMBS|Входящие байты (не рекомендуется) (устарело)|Байты|Итого|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Входящие байты" (устарело)|Нет измерений|
|EHINBYTES|Входящие байты (не рекомендуется)|Байты|Итого|Входящие сообщения пропускная способность концентратора событий для пространства имен (не рекомендуется)|Нет измерений|
|EHOUTMBS|Исходящие байты (не рекомендуется) (устарело)|Байты|Итого|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "исходящие байты" (устарело)|Нет измерений|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байты|Итого|Исходящие сообщения пропускная способность концентратора событий для пространства имен (не рекомендуется)|Нет измерений|
|EHABL|Архивные сообщения невыполненной работы (не рекомендуется)|Count|Итого|Архивные сообщения концентратора событий в невыполненной работе для пространства имен (не рекомендуется)|Нет измерений|
|EHAMSGS|Архивные сообщения (не рекомендуется)|Count|Итого|Архивные сообщения концентратора событий в пространстве имен (не рекомендуется)|Нет измерений|
|EHAMBS|Скорость обработки архивных сообщений (не рекомендуется)|Байты|Итого|Пропускная способность архивных сообщений концентратора событий в пространстве имен (не рекомендуется)|Нет измерений|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Итого|Выполненные запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Итого|Ошибки на сервере для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Итого|Ошибки пользователей для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|QuotaExceededErrors|Ошибки превышения квоты. (предварительная версия)|Count|Итого|Ошибки превышения квоты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Итого|Регулируемые запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Итого|Входящие запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Итого|Входящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Итого|Исходящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingBytes|Входящие байты. (предварительная версия)|Байты|Итого|Входящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingBytes|Исходящие байты. (предварительная версия)|Байты|Итого|Исходящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Average|Число активных подключений для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Count|Average|Открытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Закрытые подключения. (предварительная версия)|Count|Average|Закрытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CaptureBacklog|Невыполненная работа записи. (предварительная версия)|Count|Итого|Невыполненная работа записи для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedMessages|Записанные сообщения. (предварительная версия)|Count|Итого|Записанные сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedBytes|Записанные байты. (предварительная версия)|Байты|Итого|Записанные байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ЦП|CPU (Preview) (ЦП (предварительная версия))|Percent|Максимальная|Использование ЦП для кластера концентратора событий в процентах|Role|
|AvailableMemory|Available Memory (Preview) (Доступная память (предварительная версия))|Count|Максимальная|Объем доступной памяти для кластера концентратора событий в байтах|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Count|Итого|Число запросов к шлюзу|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Count|Итого|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Количество активных исполнителей|Count|Максимальная|Количество активных исполнителей|ClusterDnsName MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Count|Average|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Count|Average|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Count|Average|Емкость, передаваемая для автомасштабирования при выполнении.|Нет измерений|
|ScaleActionsInitiated|Инициированные действия масштабирования|Count|Итого|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Общедоступная предварительная версия)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Доступность|Percent|Average|Процент тестов успешно завершенную доступности|availabilityResult availabilityResult/имя, и расположение|
|availabilityResults/count|Тесты доступности|Count|Count|Число тестов доступности|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Длительность теста доступности|MilliSeconds|Average|Длительность теста доступности|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Время подключения к сети при загрузке страницы|MilliSeconds|Average|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|Нет измерений|
|browserTimings/processingDuration|Время обработки клиента|MilliSeconds|Average|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|Нет измерений|
|browserTimings/receiveDuration|Время получения ответа|MilliSeconds|Average|Время от первого до последнего байта или до отключения.|Нет измерений|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Average|Время от установки сетевого подключения до получения первого байта.|Нет измерений|
|browserTimings/totalDuration|Время загрузки страницы в браузере|MilliSeconds|Average|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|Нет измерений|
|dependencies/count|Вызовы зависимостей|Count|Count|Число вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Длительность зависимости|MilliSeconds|Average|Длительность вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Сбои вызовов зависимостей|Count|Count|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Просмотры страниц|Count|Count|Число просмотров страниц.|operation/synthetic|
|pageViews/duration|Время загрузки страницы для просмотра|MilliSeconds|Average|Время загрузки страницы для просмотра|operation/synthetic|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Average|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue (HTTP-запросы в очереди приложений)|Count|Average|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|Число/с|Average|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|Число/с|Average|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|Байт/с|Average|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Обработка ЦП|Percent|Average|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Процессорное время|Percent|Average|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Объем доступной памяти|Байты|Average|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Количество байтов исключительного использования процессов|Байты|Average|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|Время ответа от сервера|MilliSeconds|Average|Время с момента получения HTTP-запроса до завершения отправки ответа.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Запросы сервера|Count|Count|Число выполненных запросов HTTP.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Failed requests (Неудачные запросы)|Count|Count|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|запросы и скорость|Частота запросов к серверу|Число/с|Average|Интенсивность запросов к серверу в секунду|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Исключения|Count|Count|Общее число всех неперехваченных исключений.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Исключения браузера|Count|Count|Число необработанных исключений в браузере.|Нет измерений|
|exceptions/server|Исключения сервера|Count|Count|Число неперехваченных исключений, созданных в серверном приложении.|cloud/roleName, cloud/roleInstance|
|traces/count|Трассировки|Count|Count|Число документов трассировки|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Count|Count|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунды|Average|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Всего результатов для API службы|Count|Count|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization (Использование кэша)|Percent|Average|Уровень загрузки в области кластера|Нет|
|QueryDuration|Длительность запросов|Миллисекунды|Average|Длительность запросов в секундах|Состояние запросов|
|IngestionUtilization|Использование приема данных|Percent|Average|Доля использованных слотов приема данных в кластере|Нет|
|KeepAlive|Проверка активности (Keep Alive)|Count|Average|Проверка работоспособности показывает, что кластер отвечает на запросы|Нет|
|IngestionVolumeInMB|Ingestion Volume (In MB) (Объем приема данных (в МБ))|Count|Итого|Общий объем данных, принятых в кластере (в МБ)|База данных|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds) (Задержка приема данных (в секундах))|Секунды|Average|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|Нет|
|EventProcessedForEventHubs|Events Processed (for Event Hubs) (Обработанные события (для концентраторов событий))|Count|Итого|Число событий, обрабатываемых кластером при приеме данных из концентратора событий|Нет|
|IngestionResult|Ingestion Result (Результат приема данных)|Count|Count|Число операций приема данных|Status|
|ЦП|ЦП|Percent|Average|Уровень загрузки ЦП|Нет|
| ContinuousExportNumOfRecordsExported | Число записей, экспортированных в непрерывный Экспорт | Count | Итого | Число записей, экспортированных для каждого артефакта хранилища, записанных в ходе операции экспорта  | Нет |
| ExportUtilization | Использование экспорта | Percent | Максимальная | Использование экспорта | Нет |
| ContinuousExportPendingCount | Непрерывный Экспорт ожидающих Count | Count | Максимальная | Число ожидающих непрерывный Экспорт заданий, готовых к выполнению | Нет |
| ContinuousExportMaxLatenessMinutes | Непрерывный Экспорт Max просрочки минут | Count | Максимальная | Максимальное время в минутах все непрерывные экспортов, которые являются ожидающие и готов к выполнению | Нет |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итого|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Count|Итого|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Count|Итого|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Count|Итого|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Отмененные запуски|Count|Итого|Число выполнений рабочего процесса отменен.|Нет измерений|
|RunLatency|Run Latency|Секунды|Average|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Average|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Count|Итого|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Percent|Итого|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Count|Итого|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Count|Итого|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Count|Итого|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed|Count|Итого|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Count|Итого|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Average|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Average|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Count|Итого|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Count|Итого|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Count|Итого|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Count|Итого|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Count|Итого|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Count|Итого|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Count|Итого|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Average|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Average|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Average|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итого|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|BillableActionExecutions|Billable Action Executions|Count|Итого|Число выполненных действий рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Итого|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Нет измерений|
|TotalBillableExecutions|Total Billable Executions|Count|Итого|Число выполнений рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Итого|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Итого|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Итого|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Итого|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Итого|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Итого|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итого|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Count|Итого|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Count|Итого|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Count|Итого|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Отмененные запуски|Count|Итого|Число выполнений рабочего процесса отменен.|Нет измерений|
|RunLatency|Run Latency|Секунды|Average|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Average|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Count|Итого|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunStartThrottledEvents|События регулировки начала выполнения|Count|Итого|Число рабочих процессов, начало выполнения событий регулирования.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Percent|Итого|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Count|Итого|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Count|Итого|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Count|Итого|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed |Count|Итого|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Count|Итого|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Average|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Average|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Count|Итого|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Count|Итого|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Count|Итого|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Count|Итого|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Count|Итого|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Count|Итого|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Count|Итого|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Average|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Average|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Average|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итого|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Загрузка процессора рабочего процесса для среды службы интеграции|Percent|Average|Использование обработчика рабочего процесса для среды службы интеграции.|Нет измерений|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Использование памяти рабочего процесса для среды службы интеграции|Percent|Average|Использование памяти рабочего процесса для среды службы интеграции.|Нет измерений|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Загрузка процессора соединитель для среды службы интеграции|Percent|Average|Использование процессора соединитель для среды службы интеграции.|Нет измерений|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Использование соединителя памяти для среды службы интеграции|Percent|Average|Соединитель объем памяти для среды службы интеграции.|Нет измерений|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Завершенные запуски|Завершенные запуски|Count|Итого|Число запусков завершена успешно для этой рабочей области|Сценарий|
|Запуски по началу работы|Запуски по началу работы|Count|Итого|Число запусков для этой рабочей области|Сценарий|
|циклы выполнения со сбоем;|циклы выполнения со сбоем;|Count|Итого|Число выполнений, завершившихся сбоем, если для этой рабочей области|Сценарий|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|ApiCategory ResponseCode ApiName ResultType,|
|Доступность|Доступность|Percent|Average|Доступность API-интерфейсы|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency (Среднее время прочей задержки)|мс/операцию|Average|Среднее время прочей задержки (кроме задержки на чтение или запись) в миллисекундах на операцию|Нет измерений|
|AverageReadLatency|Средняя задержка чтения|мс/операцию|Average|Средняя задержка чтения в миллисекундах на операцию|Нет измерений|
|AverageTotalLatency|Average total latency (Средняя общая задержка)|мс/операцию|Average|Средняя общая задержка в миллисекундах на операцию|Нет измерений|
|AverageWriteLatency|Средняя задержка записи|мс/операцию|Average|Средняя задержка записи в миллисекундах на операцию|Нет измерений|
|FilesystemOtherOps|Filesystem other ops (Прочие операции файловой системы)|операции|Average|Число прочих операций файловой системы (кроме операций чтения и записи)|Нет измерений|
|FilesystemReadOps|Filesystem read ops (Операции чтения файловой системы)|операции|Average|Число операций чтения файловой системы|Нет измерений|
|FilesystemTotalOps|Filesystem total ops (Общее число операций файловой системы)|операции|Average|Сумма всех операций файловой системы|Нет измерений|
|FilesystemWriteOps|Filesystem write ops (Операций записи файловой системы)|операции|Average|Число операций записи файловой системы|Нет измерений|
|IoBytesPerOtherOps|Io bytes per other ops (Байты ввода-вывода на прочие операции)|байт/операцию|Average|Число байт ввода-вывода на прочие операции (кроме операций чтения и записи)|Нет измерений|
|IoBytesPerReadOps|Io bytes per read ops (Байты ввода-вывода на операцию чтения)|байт/операцию|Average|Число байт ввода-вывода на операцию чтения|Нет измерений|
|IoBytesPerTotalOps|Io bytes per op across all operations (Байты ввода-вывода на операцию по всем операциям)|байт/операцию|Average|Сумма байт ввода-вывода по всем операциям|Нет измерений|
|IoBytesPerWriteOps|Io bytes per write ops (Байты ввода-вывода на операцию записи)|байт/операцию|Average|Число байт ввода-вывода на операцию записи|Нет измерений|
|OtherIops|Other iops (Прочие операции ввода-вывода в секунду)|операций/с|Average|Прочие операции ввода-вывода в секунду|Нет измерений|
|OtherThroughput|Other throughput (Прочая пропускная способность)|МB/с|Average|Прочая пропускная способность (кроме операций чтения и записи) в мегабайтах в секунду|Нет измерений|
|ReadIops|Read iops (Число операций ввода-вывода в секунду при чтении)|операций/с|Average|Число операций ввода-вывода в секунду при чтении|Нет измерений|
|ReadThroughput|Read throughput (Пропускная способность при чтении)|МB/с|Average|Пропускная способность при чтении (в мегабайтах в секунду)|Нет измерений|
|TotalIops|Общее число операций ввода-вывода|операций/с|Average|Сумма всех операций ввода-вывода в секунду|Нет измерений|
|TotalThroughput|Общая пропускная способность|МB/с|Average|Сумма всей пропускной способности в мегабайтах в секунду|Нет измерений|
|VolumeAllocatedSize|Volume allocated size (Выделенный объем тома)|Байты|Average|Выделенный размер тома (не фактически используемые байты)|Нет измерений|
|VolumeLogicalSize|Volume logical size (Логический размер тома)|Байты|Average|Логический размер тома (используемые байты)|Нет измерений|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|Байты|Average|Размер всех моментальных снимков в томе|Нет измерений|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|операций/с|Average|Число операций ввода-вывода в секунду при записи|Нет измерений|
|WriteThroughput|Write throughput (Пропускная способность операций записи)|МB/с|Average|Пропускная способность операций записи в мегабайтах в секунду|Нет измерений|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size (Выделенный размер пула для тома)|Байты|Average|Выделенный размер пула (не фактически используемые байты)|Нет измерений|
|VolumePoolAllocatedUsed|Volume pool allocated used (Используемый размер выделенного пула для тома)|Байты|Average|Используемый выделенный размер пула|Нет измерений|
|VolumePoolTotalLogicalSize|Volume pool total logical size (Общий логический размер пула для тома)|Байты|Average|Сумма логического размера всех томов, входящих в пул|Нет измерений|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size (Общий размер моментальных снимков пула для тома)|Байты|Average|Сумма всех моментальных снимков в пуле|Нет измерений|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Count|Итого|Число байт, отправленных сетевым интерфейсом|Нет измерений|
|BytesReceivedRate|Получено байт|Count|Итого|Число байт, полученных сетевым интерфейсом|Нет измерений|
|PacketsSentRate|Отправлено пакетов|Count|Итого|Число пакетов, отправленных сетевым интерфейсом|Нет измерений|
|PacketsReceivedRate|Получено пакетов|Count|Итого|Число пакетов, полученных сетевым интерфейсом|Нет измерений|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Average|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Count|Average|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Количество байтов|Count|Итого|Общее количество байтов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Количество пакетов|Count|Итого|Общее количество пакетов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Количество пакетов SYN|Count|Итого|Общее количество пакетов SYN, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Количество подключений SNAT|Count|Итого|Общее количество подключений SNAT, созданных за период времени|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Count|Итого|Общее число портов SNAT, выделенных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Count|Итого|Общее количество портов SNAT, использованных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Итого|Количество запросов, выполненных для зоны DNS|Нет измерений|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Количество наборов записей в зоне DNS|Нет измерений|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Percent|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|Нет измерений|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
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
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Count|Максимальная|Выполняется ли атака DDoS|Нет измерений|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|Нет измерений|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Average|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Count|Итого|Общее количество байтов, переданных за период времени|Port, Direction|
|PacketCount|Количество пакетов|Count|Итого|Общее количество пакетов, переданных за период времени|Port, Direction|
|SynCount|Количество пакетов SYN|Count|Итого|Общее количество пакетов SYN, переданных за период времени|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ApplicationRuleHit|Число попаданий правил приложения|Count|Итого|Количество раз, когда произошло попадание правила приложения|Протокол OCSP, поэтому|
|NetworkRuleHit|Число попаданий правил сети|Count|Итого|Количество раз, когда произошло попадание правила сети|Протокол OCSP, поэтому|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Итого|Количество байтов в секунду, обрабатываемых шлюзом приложений|Нет измерений|
|UnhealthyHostCount|Количество неработоспособных узлов|Count|Average|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Count|Average|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Count|Итого|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Count|Итого|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Count|Итого|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Count|Итого|Число текущих установленных подключений к шлюзу приложений|Нет измерений|
|CapacityUnits|Текущих единицах мощности|Count|Average|Единиц емкости|Нет измерений|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|Байт/с|Average|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|Байт/с|Average|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Count|Максимальная|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Average|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Итого|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Итого|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Count|Итого|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Count|Итого|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Count|Итого|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Count|Итого|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Average|Входящий трафик Azure в секунду (в битах)|Тип PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Average|Исходящий трафик Azure в секунду (в битах)|Тип PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Average|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Average|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Average|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Average|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Count|Итого|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Count|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Percent|Average|Доля проб мониторинга подключения с ошибками, в процентах|Нет измерений|
|AverageRoundtripMs|Среднее Время приема-передачи (мс)|MilliSeconds|Average|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|Нет измерений|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Count|Итого|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Размер запроса|Байты|Итого|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Размер ответа|Байты|Итого|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Число запросов к серверному компоненту|Count|Итого|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Average|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Серверная часть|
|TotalLatency|Total Latency (Общая задержка)|MilliSeconds|Average|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Percent|Average|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Итого|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Итого|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Нет измерений|
|registration.create|Операции создания регистрации|Count|Итого|Число всех успешных операций создания регистраций.|Нет измерений|
|registration.update|Операции обновления регистрации|Count|Итого|Число всех успешных операций обновления регистраций.|Нет измерений|
|registration.get|Операции чтения регистрации|Count|Итого|Число всех успешных запросов регистрации.|Нет измерений|
|registration.delete|Операции удаления регистрации|Count|Итого|Число всех успешных операций удаления регистраций.|Нет измерений|
|incoming|Входящие сообщения|Count|Итого|Число всех успешно отправленных вызовов API. |Нет измерений|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Итого|Отменено запланированных Push-уведомлений|Нет измерений|
|incoming.scheduled.cancel|Отменено запланированных Push-уведомлений|Count|Итого|Отменено запланированных Push-уведомлений|Нет измерений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Итого|Запланированных уведомлений в состоянии ожидания|Нет измерений|
|installation.all|Операции управления установкой|Count|Итого|Операции управления установкой|Нет измерений|
|installation.get|Операции получения установки|Count|Итого|Операции получения установки|Нет измерений|
|installation.upsert|Операции создания или обновления установки|Count|Итого|Операции создания или обновления установки|Нет измерений|
|installation.patch|Операции исправления установки|Count|Итого|Операции исправления установки|Нет измерений|
|installation.delete|Операции удаления установки|Count|Итого|Операции удаления установки|Нет измерений|
|outgoing.allpns.success|Успешные уведомления|Count|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Итого|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Нет измерений|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Итого|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Нет измерений|
|outgoing.allpns.channelerror|Ошибки канала|Count|Итого|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Нет измерений|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Итого|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Нет измерений|
|outgoing.wns.success|Успешные уведомления WNS|Count|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Нет измерений|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Нет измерений|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Итого|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Нет измерений|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Итого|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Нет измерений|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Итого|Windows Live недоступна.|Нет измерений|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Итого|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Нет измерений|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Итого|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Нет измерений|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Итого|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Нет измерений|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Итого|Полезные данные уведомления слишком большие (состояние WNS: 413).|Нет измерений|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Нет измерений|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS — отключено).|Нет измерений|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Нет измерений|
|outgoing.wns.pnserror|Ошибки WNS|Count|Итого|Уведомление не доставлено из-за ошибок связи с WNS.|Нет измерений|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Итого|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Нет измерений|
|outgoing.apns.success|Успешные уведомления APNS|Count|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Итого|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|Нет измерений|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Итого|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Нет измерений|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Итого|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|Нет измерений|
|outgoing.apns.pnserror|Ошибки APNS|Count|Итого|Количество неудачных отправлений из-за ошибок связи с APNS.|Нет измерений|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Итого|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Нет измерений|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Итого|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Нет измерений|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Итого|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Нет измерений|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Итого|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Нет измерений|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Итого|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Нет измерений|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Итого|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Нет измерений|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Итого|Количество неудачных отправлений из-за ошибок связи с GCM.|Нет измерений|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Нет измерений|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Нет измерений|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Итого|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Нет измерений|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Итого|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Нет измерений|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Итого|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Нет измерений|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Итого|Количество отправок, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Нет измерений|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Итого|Количество неудачных отправлений из-за ошибок связи с MPNS.|Нет измерений|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|notificationhub.pushes|Все исходящие уведомления|Count|Итого|Все исходящие уведомления из центра уведомлений.|Нет измерений|
|incoming.all.requests|Все входящие запросы|Count|Итого|Общее количество входящих запросов для концентратора уведомлений|Нет измерений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Итого|Общее количество неудачных входящих запросов для концентратора уведомлений|Нет измерений|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Процент свободных индексных дескрипторов|Count|Average|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Процент свободного места|Count|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Процент используемых индексных дескрипторов|Count|Average|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Процент используемого места|Count|Average|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Скорость чтения с диска (байт/с)|Count|Average|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Count|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Скорость записи на диск (байт/с)|Count|Average|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Count|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Count|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Скорость обмена с логическим диском (байт/с)|Count|Average|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|Процент доступной памяти|Count|Average|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Процент доступной области подкачки|Count|Average|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Процент используемой памяти|Count|Average|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Процент используемой области подкачки|Count|Average|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Доступный объем памяти в МБ|Count|Average|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Доступный объем подкачки в МБ|Count|Average|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Операций чтения со страницы в секунду|Count|Average|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Операций записи на страницу в секунду|Count|Average|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Страниц в секунду|Count|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Используемая области подкачки в МБ|Count|Average|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Используемый объем памяти в МБ|Count|Average|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Всего передано байт|Count|Average|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Всего получено байт|Count|Average|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Всего байт|Count|Average|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Всего передано пакетов|Count|Average|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Всего получено пакетов|Count|Average|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Всего ошибок Rx|Count|Average|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Всего ошибок Tx|Count|Average|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Всего конфликтов|Count|Average|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Count|Average|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время обращения к диску (с)|Среднее время обращения к диску (с)|Count|Average|Average_Avg. время обращения к диску (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (с)|Count|Average|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Скорость обмена с физическим диском (байт/с)|Count|Average|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Процент времени работы в привилегированном режиме|Count|Average|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Процент времени пользователя|Count|Average|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Используемая память в КБ|Count|Average|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Виртуальная общая память|Count|Average|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Процент времени DPC|Count|Average|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Процент времени простоя|Count|Average|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Процент времени прерывания|Count|Average|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Процент времени ожидания ввода-вывода|Count|Average|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Процент времени работы с низким приоритетом|Count|Average|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Процент времени работы в привилегированном режиме|Count|Average|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% загруженности процессора|Count|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Процент времени пользователя|Count|Average|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Объем свободной физической памяти|Count|Average|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Объем свободного места в файлах подкачки|Count|Average|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Объем свободной виртуальной памяти|Count|Average|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Процессы|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Объем, сохраненный в файлах подкачки|Count|Average|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Время доступности|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Пользователи|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Count|Average|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (с)|Count|Average|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Count|Average|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Count|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Count|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Count|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Процент свободного места|Count|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Доступный объем в МБ|Count|Average|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Count|Average|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Полученных байтов/с|Count|Average|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Отправленных байтов/с|Count|Average|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Всего байтов/с|Count|Average|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% загруженности процессора|Count|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Длина очереди процессора|Count|Average|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Пульс|Пульс|Count|Итого|Пульс|Computer, OSType, Version, SourceComputerId|
|Обновление|Обновление|Count|Average|Обновление|Computer, Product, Classification, UpdateState, Optional, Approved|
|Событие|Событие|Count|Average|Событие|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунды|Average|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Average|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Count|Итого|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байты|Average|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Percent|Average|Среднее значение пробуксовки памяти.|Нет измерений|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Итого|Выполненные ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Итого|ClientError в ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Итого|ServerError в ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Итого|Выполненные SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Итого|ClientError в SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Итого|ServerError в SenderConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Итого|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Итого|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Итого|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Итого|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Итого|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Итого|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Итого|Всего SenderDisconnects для Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Average|Среднее время задержки поиска для службы поиска.|Нет измерений|
|SearchQueriesPerSecond|Search queries per second|Число/с|Average|Число поисковых запросов в секунду для службы поиска.|Нет измерений|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Percent|Average|Процент отрегулированных поисковых запросов для службы поиска.|Нет измерений|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Итого|Общее количество выполненных запросов для пространства имен (предварительная версия)|EntityName|
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Итого|Ошибки на сервере для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Итого|Ошибки пользователей для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Итого|Регулируемые запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Итого|Входящие запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Итого|Входящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Итого|Исходящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Итого|Число активных подключений для Microsoft.ServiceBus. (предварительная версия)|Нет измерений|
|Size|Размер (предварительная версия)|Байты|Average|Размер очереди или раздела в байтах. (предварительная версия)|EntityName|
|Сообщения|Число сообщений в очереди или разделе. (предварительная версия)|Count|Average|Число сообщений в очереди или разделе. (предварительная версия)|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе. (предварительная версия)|Count|Average|Число активных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|DeadletteredMessages|Количество недоставленных сообщений в очереди или раздела. (предварительная версия)|Count|Average|Количество недоставленных сообщений в очереди или раздела. (предварительная версия)|EntityName|
|ScheduledMessages|Число запланированных сообщений в очереди или раздела. (предварительная версия)|Count|Average|Число запланированных сообщений в очереди или раздела. (предварительная версия)|EntityName|
|CPUXNS|CPU usage per namespace|Percent|Максимальная|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|Нет измерений|
|WSXNS|Memory size usage per namespace|Percent|Максимальная|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|Нет измерений|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|ЦП, выделенных для этого контейнера (в миллиядрах)|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Байты|Average|Память, выделенная для этого контейнера в МБ|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Фактическое использование ЦП (в миллиядрах)|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|ActualMemory|ActualMemory|Байты|Average|Фактическое использование памяти в МБ|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Использование ЦП для этого контейнера как процент AllocatedCpu|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Использование ЦП для этого контейнера как процент AllocatedCpu|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Состояние приложения Service Fabric Mesh|ApplicationName, состояние|
|ServiceStatus|ServiceStatus|Count|Average|Состояние работоспособности службы в приложении Service Fabric Mesh|ServiceName ApplicationName, состояние,|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Состояние работоспособности реплики службы в приложении Service Fabric Mesh|ApplicationName, статус, ServiceName, ServiceReplicaName|
|Состояние_контейнера|Состояние_контейнера|Count|Average|Состояние контейнера в Service Fabric Mesh приложения|ApplicationName, ServiceName, Имя_пакета_кода, ServiceReplicaName, состояние|
|RestartCount|RestartCount|Count|Average|Число контейнера перезапусков приложения Service Fabric Mesh|ApplicationName, статус, ServiceName, ServiceReplicaName, Имя_пакета_кода|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Count|Максимальная|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Количество сообщений|Count|Итого|Общее количество сообщений.|Нет измерений|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байты|Итого|Входящий трафик службы|Нет измерений|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байты|Итого|Исходящий трафик службы|Нет измерений|
|UserErrors|Ошибки пользователей|Percent|Максимальная|Процент ошибок пользователей|Нет измерений|
|SystemErrors|Системные ошибки|Percent|Максимальная|Процент системных ошибок|Нет измерений|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Percent|Average|Процент использования ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Percent|Average|Процент операций ввода/вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Percent|Average|Log IO percentage|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Percent|Average|Процент использования DTU|Нет измерений|
|storage|Пространство данных, используемое|Байты|Максимальная|Total database size|Нет измерений|
|connection_successful|Успешные подключения|Count|Итого|Успешные подключения|Нет измерений|
|connection_failed|Неудачные подключения|Count|Итого|Неудачные подключения|Нет измерений|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Итого|Заблокировано брандмауэром|Нет измерений|
|взаимоблокировка|Взаимоблокировки|Count|Итого|Взаимоблокировки|Нет измерений|
|storage_percent|Процент пространства, используемого данных|Percent|Максимальная|Размер базы данных в процентах|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Percent|Average|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|
|workers_percent|Workers percentage|Percent|Average|Workers percentage|Нет измерений|
|sessions_percent|Процент использования сеансов|Percent|Average|Процент использования сеансов|Нет измерений|
|dtu_limit|Лимит DTU|Count|Average|Лимит DTU|Нет измерений|
|dtu_used|DTU used|Count|Average|DTU used|Нет измерений|
|cpu_limit|Ограничения на ЦП|Count|Average|Ограничения на ЦП|Нет измерений|
|cpu_used|ЦП|Count|Average|ЦП|Нет измерений|
|dwu_limit|Лимит DWU|Count|Максимальная|Лимит DWU|Нет измерений|
|dwu_consumption_percent|DWU percentage|Percent|Максимальная|DWU percentage|Нет измерений|
|dwu_used|DWU used|Count|Максимальная|DWU used|Нет измерений|
|dw_cpu_percent|Процент использования ЦП на уровне узла DW|Percent|Average|Процент использования ЦП на уровне узла DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Процент операций ввода-вывода данных на уровне узла DW|Percent|Average|Процент операций ввода-вывода данных на уровне узла DW|DwLogicalNodeId|
    |cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Percent|Максимальная|Cache hit percentage (Процент попаданий в кэш)|Нет измерений|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Percent|Максимальная|Cache used percentage (Процент использования кэша)|Нет измерений|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Percent|Average|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Нет измерений|
|app_cpu_billed|Приложение ЦП выставлен счет|Count|Итого|Приложение ЦП выставлен счет|Нет измерений|
|app_cpu_percent|Процент ЦП приложения|Percent|Average|Процент ЦП приложения|Нет измерений|
|app_memory_percent|Процент используемой памяти приложения|Percent|Average|Процент используемой памяти приложения|Нет измерений|
|allocated_data_storage|Выделено пространство данных|Байты|Average|Выделено пространство данных|Нет измерений|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Percent|Average|Процент использования ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Percent|Average|Процент операций ввода/вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Percent|Average|Log IO percentage|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Percent|Average|Процент использования DTU|Нет измерений|
|storage_percent|Процент пространства, используемого данных||Percent|Average|Storage percentage|Нет измерений|
|workers_percent|Workers percentage|Percent|Average|Workers percentage|Нет измерений|
|sessions_percent|Процент использования сеансов|Percent|Average|Процент использования сеансов|Нет измерений|
|eDTU_limit|eDTU limit|Count|Average|eDTU limit|Нет измерений|
|storage_limit|Максимальный размер данных|Байты|Average|Storage limit|Нет измерений|
|eDTU_used|eDTU used|Count|Average|eDTU used|Нет измерений|
|storage_used|Пространство данных, используемое|Байты|Average|Storage used|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Percent|Average|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|
|cpu_limit|Ограничения на ЦП|Count|Average|Ограничения на ЦП|Нет измерений|
|cpu_used|ЦП|Count|Average|ЦП|Нет измерений|
|allocated_data_storage|Выделено пространство данных|Байты|Average|Выделено пространство данных|Нет измерений|
|allocated_data_storage_percent|Процент пространства, выделенного данных|Percent|Максимальная|Процент пространства, выделенного данных|Нет измерений|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Count|Average|Virtual core count (Число виртуальных ядер)|Нет измерений|
|avg_cpu_percent|Average CPU percentage (Средний процент использования ЦП)|Percent|Average|Average CPU percentage (Средний процент использования ЦП)|Нет измерений|
|reserved_storage_mb|Зарезервированное дисковое пространство|Count|Average|Зарезервированное дисковое пространство|Нет измерений|
|storage_space_used_mb|Использованное дисковое пространство|Count|Average|Использованное дисковое пространство|Нет измерений|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Count|Average|IO requests count (Количество запросов ввода-вывода)|Нет измерений|
|io_bytes_read|Количество считанных байт ввода-вывода|Байты|Average|Количество считанных байт ввода-вывода|Нет измерений|
|io_bytes_written|Количество записанных байт ввода-вывода|Байты|Average|Количество записанных байт ввода-вывода|Нет измерений|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Average|Используемая емкость учетной записи|Нет измерений|
|Транзакции|Транзакции|Count|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Average|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Count|Итого|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType|       |BlobCount|Количество больших двоичных объектов|Count|Average|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Average|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Нет измерений|
|IndexCapacity|Индекс емкости|Байты|Average|Объем хранилища, используемого индексом (иерархических) ADLS Gen2 в байтах.|Нет измерений|
|Транзакции|Транзакции|Count|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Average|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|Нет измерений|
|FileCount|Количество файлов|Count|Average|Количество файлов в службе файлов учетной записи хранения.|Нет измерений|
|FileShareCount|Количество общих файловых ресурсов|Count|Average|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Average|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Нет измерений|
|QueueCount|Количество очередей|Count|Average|Количество очередей в службе очередей учетной записи хранения.|Нет измерений|
|QueueMessageCount|Количество сообщений очереди|Count|Average|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Average|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|Нет измерений|
|TableCount|Количество таблиц|Count|Average|Количество таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|TableEntityCount|Количество сущностей таблиц|Count|Average|Количество сущностей таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Результат сеанса синхронизации|Count|Average|Метрики, журналы значение 1 каждой времени конечную точку сервера успешно завершает сеанс синхронизации с облачной конечной точки|SyncDirection SyncGroupName ServerEndpointName,|
|StorageSyncSyncSessionAppliedFilesCount|Файлы, синхронизированные|Count|Итого|Число файлов синхронизированы|SyncDirection SyncGroupName ServerEndpointName,|
|StorageSyncSyncSessionPerItemErrorsCount|Несинхронизирующиеся файлы|Count|Итого|Количество файлов, которые не удалось синхронизировать|SyncDirection SyncGroupName ServerEndpointName,|
|StorageSyncBatchTransferredFileBytes|Синхронизировано байт|Байты|Итого|Общий размер файлов, передаваемых для сеансов синхронизации|SyncDirection SyncGroupName ServerEndpointName,|
|StorageSyncServerHeartbeat|Состояние сервера|Count|Максимальная|Метрики, журналы значение 1 каждой времени зарегистрированный сервер успешно записывает периодический сигнал с облачной конечной точки|имя_сервера;|
|StorageSyncRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итого|Общий объем данных, инициированные сервера|имя_сервера;|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Percent|Максимальная|Использование единиц потоковой передачи (%)|LogicalName PartitionId|
|InputEvents|Входные события|Count|Итого|Входные события|LogicalName PartitionId|
|InputEventBytes|Байты входного события|Байты|Итого|Байты входного события|LogicalName PartitionId|
|LateInputEvents|События позднего ввода|Count|Итого|События позднего ввода|LogicalName PartitionId|
|OutputEvents|Выходные события|Count|Итого|Выходные события|LogicalName PartitionId|
|ConversionErrors|Ошибки преобразования данных|Count|Итого|Ошибки преобразования данных|LogicalName PartitionId|
|Errors|Ошибки среды выполнения|Count|Итого|Ошибки среды выполнения|LogicalName PartitionId|
|DroppedOrAdjustedEvents|Неупорядоченные события|Count|Итого|Неупорядоченные события|LogicalName PartitionId|
|AMLCalloutRequests|Запросы функций|Count|Итого|Запросы функций|LogicalName PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Итого|Неудачные запросы функций|LogicalName PartitionId|
|AMLCalloutInputEvents|События функций|Count|Итого|События функций|LogicalName PartitionId|
|DeserializationError|Ошибки десериализации входа|Count|Итого|Ошибки десериализации входа|LogicalName PartitionId|
|EarlyInputEvents|Ранние входные события|Count|Итого|Ранние входные события|LogicalName PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимальная|Предельная задержка|LogicalName PartitionId|
|InputEventsSourcesBacklogged|Необработанные входные события|Count|Максимальная|Необработанные входные события|LogicalName PartitionId|
|InputEventsSourcesPerSecond|Полученные входные источники|Count|Итого|Полученные входные источники|LogicalName PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итого|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итого|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итого|Количество байтов, полученных от всех источников событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итого|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итого|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Average|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|WarmStorageMaxProperties|Свойства Max "горячего" резервирования хранилища|Count|Максимальная|Максимально допустимое свойств, используемых в среде с номером SKU S1 и S2 и максимальное число свойств, предоставляемых Store "горячего" резервирования для номера SKU (PAYG)|Нет измерений|
|WarmStorageUsedProperties|Использовать "горячего" резервирования хранилище свойств |Count|Максимальная|Число свойств, используемых в среде с номером SKU S1 и S2 и количество свойств, используемых Store "горячего" резервирования для номера SKU (PAYG)|Нет измерений|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итого|Количество сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итого|Количество недопустимых сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итого|Количество байтов, считанных из источника событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итого|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итого|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Average|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|WarmStorageMaxProperties|Свойства Max "горячего" резервирования хранилища|Count|Максимальная|Максимально допустимое свойств, используемых в среде с номером SKU S1 и S2 и максимальное число свойств, предоставляемых Store "горячего" резервирования для номера SKU (PAYG)|Нет измерений|
|WarmStorageUsedProperties|Использовать "горячего" резервирования хранилище свойств |Count|Максимальная|Число свойств, используемых в среде с номером SKU S1 и S2 и количество свойств, используемых Store "горячего" резервирования для номера SKU (PAYG)|Нет измерений|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Байт/с|Average|Время обращения к диску пропускной способности из-за операций чтения в период выборки.|Нет измерений|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Байт/с|Average|Время обращения к диску пропускной способности из-за операции записи в период выборки.|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Общая пропускная способность дисков из-за операций чтения в период выборки.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Общая пропускная способность дисков из-за операции записи в период выборки.|Нет измерений|
|DiskReadOperations|Операции чтения с диска|Count|Итого|Операции чтения число операций ввода-ВЫВОДА в предыдущем примере периоде. Обратите внимание на то, что эти операции могут быть размер переменной.|Нет измерений|
|DiskWriteOperations|Операций записи на диск|Count|Итого|Число операций ввода-ВЫВОДА операций записи в предыдущий период выборки. Обратите внимание на то, что эти операции могут быть размер переменной.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Average|Среднее число операций ввода-ВЫВОДА операций чтения в предыдущем примере периоде. Обратите внимание на то, что эти операции могут быть размер переменной.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Average|Среднее число операций ввода-ВЫВОДА операций записи в предыдущий период выборки. Обратите внимание на то, что эти операции могут быть размер переменной.|Нет измерений|
|DiskReadLatency|Задержка чтения с диска|Миллисекунды|Average|Задержка чтения всего. Сумма устройства и ядра задержкой операций чтения.|Нет измерений|
|DiskWriteLatency|Задержка записи на диск|Миллисекунды|Average|Задержка всего записи. Задержки записи на сумме устройства и ядра.|Нет измерений|
|NetworkInBytesPerSecond|Сеть в байт/с|Байт/с|Average|Пропускная способность сети среднее для принятого трафика.|Нет измерений|
|NetworkOutBytesPerSecond|Исходящий сетевой трафик байт/с|Байт/с|Average|Пропускная способность сети среднее для передаваемого трафика.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Общая пропускная способность сети для принятого трафика.|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Общая пропускная способность сети для передачи трафика.|Нет измерений|
|MemoryUsed|Память, используемая|Байты|Average|Объем памяти компьютера, который используется виртуальной Машиной.|Нет измерений|
|MemoryGranted|Памяти, предоставленный|Байты|Average|Объем памяти, предоставленное узлом виртуальной Машины. Память не была предоставлена на узле, пока он обрабатывается один раз и предоставил памяти может быть переключена или ballooned немедленно, если VMkernel требуется память.|Нет измерений|
|MemoryActive|Активный памяти|Байты|Average|Объем памяти, используемой виртуальной Машины в прошлом небольшой промежуток времени. Это «true» число объем памяти на виртуальной Машине в настоящее время есть необходимость. Дополнительную, неиспользуемые память может быть переключена или ballooned без влияния на производительность виртуальной машины.|Нет измерений|
|Percentage CPU|Загрузка ЦП|Percent|Average|Использование ЦП. Это значение передается вместе с 100%, представляющий все ядра процессора в системе. Например виртуальная машина двунаправленного, с использованием 50% Четырехъядерная система полностью использует два ядра.|Нет измерений|
|PercentageCpuReady|Все готово для ЦП в процентах|Миллисекунды|Итого|Ready time не времени тратится на ожидание для простое станут доступны за последний интервал обновления.|Нет измерений|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Percent|Average|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент использования памяти|Экземпляр|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Average|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Average|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Average|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа:|Экземпляр|
|AppConnections|Подключения|Count|Average|Подключения|Экземпляр|
|Handles|Счетчик дескрипторов|Count|Average|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Average|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Average|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Average|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Average|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Единицы выполнения функции.|МБ / мс|Итого|[Единицы выполнения функции](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Итого|Function Execution Count|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Average|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Average|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Average|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа:|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Count|Итого|Единицы выполнения функции.|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Итого|Function Execution Count|Экземпляр|
|AppConnections|Подключения|Count|Average|Подключения|Экземпляр|
|Handles|Счетчик дескрипторов|Count|Average|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Average|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Average|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итого|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итого|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итого|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итого|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итого|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итого|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Итого|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Итого|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Итого|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Requests|Requests|Count|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итого|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа:|Экземпляр|
|CpuPercentage|Процент использования ЦП|Percent|Average|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент использования памяти|Экземпляр|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Average|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Count|Итого|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Average|Общее число внешних интерфейсов|Нет измерений|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Count|Average|Рабочие роли плана службы приложений уровня "Малый"|Нет измерений|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Средняя|Рабочие процессы плана службы приложений уровня "Средний"|Нет измерений|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Count|Average|Рабочие процессы плана службы приложений уровня "Крупный"|Нет измерений|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Count|Average|Общее количество рабочих процессов|Нет измерений|
|WorkersAvailable|Доступные рабочие процессы|Count|Average|Доступные рабочие процессы|Нет измерений|
|WorkersUsed|Использованные рабочие процессы|Count|Average|Использованные рабочие процессы|Нет измерений|
|CpuPercentage|Процент использования ЦП|Percent|Average|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент использования памяти|Экземпляр|

## <a name="next-steps"></a>Следующие шаги
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создайте оповещения на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](diagnostic-logs-overview.md)
