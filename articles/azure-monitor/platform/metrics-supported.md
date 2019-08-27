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
ms.openlocfilehash: dbbe5a667b8d467b416e4a4a571d8d3599ec45b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051822"
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

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байт|Average|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Average|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|CountPerSecond|Average|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Average|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Average|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Average|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Average|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Average|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: Текущие подключения|Count|Average|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Average|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байт|Average|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байт|Average|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: Использование памяти|Байт|Average|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байт|Average|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байт|Average|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байт|Average|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байт|Average|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Квота|Память: Квота|Байт|Average|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Count|Average|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байт|Average|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байт|Average|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|CountPerSecond|Average|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|CountPerSecond|Average|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|CountPerSecond|Average|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
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
|mashup_engine_memory_metric|Память подсистемы M|Байт|Average|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Запрошено|Запрошено|Count|Всего|Общее число запросов к шлюзу за указанный период. Его можно разделить на несколько измерений, чтобы помочь в диагностике проблем. |Location, Баккендреспонсекоде, Ластеррорреасон, Гатевайреспонсекоде|
|TotalRequests|Total Gateway Requests|Count|Всего|Общее число запросов к шлюзу за указанный период. Эта метрика устарела, мы рекомендуем использовать новую `Requests` метрику. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Count|Всего|Общее число успешных запросов к шлюзу за указанный период. Эта метрика устарела, мы рекомендуем использовать новую `Requests` метрику.|Location, Hostname|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Count|Всего| Общее число неавторизованных запросов к шлюзу за указанный период. Эта метрика устарела, мы рекомендуем использовать новую `Requests` метрику.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Count|Всего|Общее число неудачных запросов к шлюзу за указанный период. Эта метрика устарела, мы рекомендуем использовать новую `Requests` метрику.|Location, Hostname|
|OtherRequests|Другие запросы к шлюзу|Count|Всего|Общее число запросов шлюза за указанный период, которые не относятся к категориям успешных, неавторизованных или неудачных. Эта метрика устарела, мы рекомендуем использовать новую `Requests` метрику. |Location, Hostname|
|Продолжительность|Общая длительность запросов шлюза|мс|Average|Время между получением клиентом запроса от клиента и возвратом ответа клиенту.|Location, Hostname|
|Capacity|Capacity|Percent|Average|Индикатор загрузки экземпляра управления API для принятия обоснованных решений о том, следует ли масштабировать экземпляр в соответствии с большей нагрузкой.|Местоположение|
|евенсубтоталевентс|Всего событий EventHub|Count|Всего|Общее число событий, отправленных в EventHub из управления API за указанный период.|Местоположение|
|евенсубсукцессфулевентс|Успешные события EventHub|Count|Всего|Общее число успешных событий EventHub за указанный период.|Местоположение|
|евенсубтоталфаиледевентс|События EventHub, завершившиеся сбоем|Count|Всего|Общее число неудачных событий EventHub за указанный период.|Местоположение|
|евенсубрежектедевентс|Отклоненные события EventHub|Count|Всего|Общее число отклоненных событий EventHub (неправильной конфигурации или несанкционированного доступа) за указанный период.|Местоположение|
|евенсубсроттледевентс|Регулируемые события EventHub|Count|Всего|Общее число регулируемых событий EventHub за указанный период.|Местоположение|
|евенсубтимедаутевентс|Истекло время ожидания событий EventHub|Count|Всего|Общее число истечения времени ожидания событий EventHub за указанный период.|Местоположение|
|евенсубдроппедевентс|Удалены события EventHub|Count|Всего|Общее число пропущенных событий из-за ограничения размера очереди в заданный период.|Местоположение|
|евенсубтоталбитессент|Размер событий EventHub|Байт|Всего|Общий размер событий EventHub в байтах за заданный период.|Местоположение|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Всего|Общее количество заданий|Состояние модуля Runbook|
|тоталупдатедеплойментрунс|Всего запусков развертывания обновлений|Count|Всего|Всего запусков развертывания обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние|
|тоталупдатедеплойментмачинерунс|Всего запусков компьютера для развертывания обновлений|Count|Всего|Всего компьютеров, на которых выполняется развертывание обновлений программного обеспечения, выполняются при развертывании обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Всего|Общее число выделенных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalNodeCount|Dedicated Node Count|Count|Всего|Общее число выделенных узлов в учетной записи пакетной службы.|Нет измерений|
|LowPriorityCoreCount|LowPriority Core Count|Count|Всего|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Всего|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Нет измерений|
|CreatingNodeCount|Creating Node Count|Count|Всего|Количество создаваемых узлов.|Нет измерений|
|StartingNodeCount|Starting Node Count|Count|Всего|Число узлов, которые запускаются.|Нет измерений|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Всего|Число узлов, ожидающих завершения задачи запуска.|Нет измерений|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Всего|Число узлов, на которых произошел сбой задачи запуска.|Нет измерений|
|IdleNodeCount|Idle Node Count|Count|Всего|Количество узлов в неактивном состоянии.|Нет измерений|
|OfflineNodeCount|Offline Node Count|Count|Всего|Количество узлов не в сети.|Нет измерений|
|RebootingNodeCount|Rebooting Node Count|Count|Всего|Количество перезапускаемых узлов.|Нет измерений|
|ReimagingNodeCount|Reimaging Node Count|Count|Всего|Число узлов, для которых пересоздается образ.|Нет измерений|
|RunningNodeCount|Running Node Count|Count|Всего|Число работающих узлов.|Нет измерений|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Всего|Количество узлов, покидающих пул.|Нет измерений|
|UnusableNodeCount|Unusable Node Count|Count|Всего|Число узлов, непригодных для использования.|Нет измерений|
|PreemptedNodeCount|Preempted Node Count|Count|Всего|Количество замещенных узлов.|Нет измерений|
|TaskStartEvent|Task Start Events|Count|Всего|Общее число запущенных задач.|Нет измерений|
|TaskCompleteEvent|Task Complete Events|Count|Всего|Общее число завершенных задач.|Нет измерений|
|TaskFailEvent|Task Fail Events|Count|Всего|Общее число задач, завершившихся сбоем.|Нет измерений|
|PoolCreateEvent|Pool Create Events|Count|Всего|Общее число созданных пулов.|Нет измерений|
|PoolResizeStartEvent|Pool Resize Start Events|Count|Всего|Общее число запущенных задач изменения размера пула.|Нет измерений|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Count|Всего|Общее число завершенных задач изменения размера пула.|Нет измерений|
|PoolDeleteStartEvent|Pool Delete Start Events|Count|Всего|Общее число запущенных задач удаления пула.|Нет измерений|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Count|Всего|Общее число завершенных задач удаления пула.|Нет измерений|
|JobDeleteCompleteEvent|Job Delete Complete Events (События окончания удаления задания)|Count|Всего|Общее количество заданий, которые были успешно удалены.|Нет измерений|
|JobDeleteStartEvent|Job Delete Start Events (События начала удаления задания)|Count|Всего|Общее количество заданий, для которых запрошено удаление.|Нет измерений|
|JobDisableCompleteEvent|Job Disable Complete Events (События окончания отключения задания)|Count|Всего|Общее количество заданий, которые были успешно отключены.|Нет измерений|
|JobDisableStartEvent|Job Disable Start Events (События начала отключения задания)|Count|Всего|Общее количество заданий, для которых запрошено отключение.|Нет измерений|
|JobStartEvent|Job Start Events (События запуска задания)|Count|Всего|Общее количество заданий, которые были успешно запущены.|Нет измерений|
|JobTerminateCompleteEvent|Job Terminate Complete Events (События окончания завершения задания)|Count|Всего|Общее количество заданий, которые были успешно завершены.|Нет измерений|
|JobTerminateStartEvent|Job Terminate Start Events (События начала завершения задания)|Count|Всего|Общее количество заданий, для которых запрошено завершение.|Нет измерений|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|connectedclients|Подключено клиентов|Count|Максимум||ShardId|
|totalcommandsprocessed|Всего операций|Count|Всего||ShardId|
|cachehits|Попаданий в кэш|Count|Всего||ShardId|
|cachemisses|Промахов в кэше|Count|Всего||ShardId|
|getcommands|Извлечения|Count|Всего||ShardId|
|setcommands|Наборы|Count|Всего||ShardId|
|operationsPerSecond|Количество операций в секунду|Count|Максимум||ShardId|
|evictedkeys|Вытеснено ключей|Count|Всего||ShardId|
|totalkeys|Всего ключей|Count|Максимум||ShardId|
|expiredkeys|Просрочено ключей|Count|Всего||ShardId|
|usedmemory|Занято памяти|Байт|Максимум||ShardId|
|usedmemorypercentage|Процент используемой памяти|Percent|Максимум||ShardId|
|usedmemoryRss|RSS используемой памяти|Байт|Максимум||ShardId|
|serverLoad|Нагрузка сервера|Percent|Максимум||ShardId|
|cacheWrite|Запись в кэш|BytesPerSecond|Максимум||ShardId|
|cacheRead|Чтение кэша|BytesPerSecond|Максимум||ShardId|
|percentProcessorTime|ЦП|Percent|Максимум||ShardId|
|cacheLatency|Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))|Count|Average||ShardId, SampleType|
|ош.|Ошибки|Count|Максимум||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Count|Максимум||Нет измерений|
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Всего||Нет измерений|
|cachehits0|Cache Hits (Shard 0)|Count|Всего||Нет измерений|
|cachemisses0|Cache Misses (Shard 0)|Count|Всего||Нет измерений|
|getcommands0|Gets (Shard 0)|Count|Всего||Нет измерений|
|setcommands0|Sets (Shard 0)|Count|Всего||Нет измерений|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Count|Максимум||Нет измерений|
|evictedkeys0|Evicted Keys (Shard 0)|Count|Всего||Нет измерений|
|totalkeys0|Total Keys (Shard 0)|Count|Максимум||Нет измерений|
|expiredkeys0|Expired Keys (Shard 0)|Count|Всего||Нет измерений|
|usedmemory0|Used Memory (Shard 0)|Байт|Максимум||Нет измерений|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байт|Максимум||Нет измерений|
|serverLoad0|Server Load (Shard 0)|Percent|Максимум||Нет измерений|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime0|CPU (Shard 0)|Percent|Максимум||Нет измерений|
|connectedclients1|Connected Clients (Shard 1)|Count|Максимум||Нет измерений|
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Всего||Нет измерений|
|cachehits1|Cache Hits (Shard 1)|Count|Всего||Нет измерений|
|cachemisses1|Cache Misses (Shard 1)|Count|Всего||Нет измерений|
|getcommands1|Gets (Shard 1)|Count|Всего||Нет измерений|
|getcommands1|Sets (Shard 1)|Count|Всего||Нет измерений|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Count|Максимум||Нет измерений|
|evictedkeys1|Evicted Keys (Shard 1)|Count|Всего||Нет измерений|
|totalkeys1|Total Keys (Shard 1)|Count|Максимум||Нет измерений|
|expiredkeys1|Expired Keys (Shard 1)|Count|Всего||Нет измерений|
|usedmemory1|Used Memory (Shard 1)|Байт|Максимум||Нет измерений|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байт|Максимум||Нет измерений|
|serverLoad1|Server Load (Shard 1)|Percent|Максимум||Нет измерений|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime1|CPU (Shard 1)|Percent|Максимум||Нет измерений|
|connectedclients2|Connected Clients (Shard 2)|Count|Максимум||Нет измерений|
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Всего||Нет измерений|
|cachehits2|Cache Hits (Shard 2)|Count|Всего||Нет измерений|
|cachemisses2|Cache Misses (Shard 2)|Count|Всего||Нет измерений|
|getcommands2|Gets (Shard 2)|Count|Всего||Нет измерений|
|getcommands2|Sets (Shard 2)|Count|Всего||Нет измерений|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Count|Максимум||Нет измерений|
|evictedkeys2|Evicted Keys (Shard 2)|Count|Всего||Нет измерений|
|totalkeys2|Total Keys (Shard 2)|Count|Максимум||Нет измерений|
|expiredkeys2|Expired Keys (Shard 2)|Count|Всего||Нет измерений|
|usedmemory2|Used Memory (Shard 2)|Байт|Максимум||Нет измерений|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байт|Максимум||Нет измерений|
|serverLoad2|Server Load (Shard 2)|Percent|Максимум||Нет измерений|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимум||Нет измерений|
|percentProcessorTime2|CPU (Shard 2)|Percent|Максимум||Нет измерений|
|connectedclients3|Connected Clients (Shard 3)|Count|Максимум||Нет измерений|
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Всего||Нет измерений|
|cachehits3|Cache Hits (Shard 3)|Count|Всего||Нет измерений|
|cachemisses3|Cache Misses (Shard 3)|Count|Всего||Нет измерений|
|getcommands3|Gets (Shard 3)|Count|Всего||Нет измерений|
|setcommands3|Sets (Shard 3)|Count|Всего||Нет измерений|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Count|Максимум||Нет измерений|
|evictedkeys3|Evicted Keys (Shard 3)|Count|Всего||Нет измерений|
|totalkeys3|Total Keys (Shard 3)|Count|Максимум||Нет измерений|
|expiredkeys3|Expired Keys (Shard 3)|Count|Всего||Нет измерений|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимум||Нет измерений|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байт|Максимум||Нет измерений|
|serverLoad3|Server Load (Shard 3)|Percent|Максимум||Нет измерений|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime3|CPU (Shard 3)|Percent|Максимум||Нет измерений|
|connectedclients4|Connected Clients (Shard 4)|Count|Максимум||Нет измерений|
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Всего||Нет измерений|
|cachehits4|Cache Hits (Shard 4)|Count|Всего||Нет измерений|
|cachemisses4|Cache Misses (Shard 4)|Count|Всего||Нет измерений|
|getcommands4|Gets (Shard 4)|Count|Всего||Нет измерений|
|setcommands4|Sets (Shard 4)|Count|Всего||Нет измерений|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Count|Максимум||Нет измерений|
|evictedkeys4|Evicted Keys (Shard 4)|Count|Всего||Нет измерений|
|totalkeys4|Total Keys (Shard 4)|Count|Максимум||Нет измерений|
|expiredkeys4|Expired Keys (Shard 4)|Count|Всего||Нет измерений|
|usedmemory4|Used Memory (Shard 4)|Байт|Максимум||Нет измерений|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байт|Максимум||Нет измерений|
|serverLoad4|Server Load (Shard 4)|Percent|Максимум||Нет измерений|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime4|CPU (Shard 4)|Percent|Максимум||Нет измерений|
|connectedclients5|Connected Clients (Shard 5)|Count|Максимум||Нет измерений|
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Всего||Нет измерений|
|cachehits5|Cache Hits (Shard 5)|Count|Всего||Нет измерений|
|cachemisses5|Cache Misses (Shard 5)|Count|Всего||Нет измерений|
|getcommands5|Gets (Shard 5)|Count|Всего||Нет измерений|
|getcommands5|Sets (Shard 5)|Count|Всего||Нет измерений|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Count|Максимум||Нет измерений|
|evictedkeys5|Evicted Keys (Shard 5)|Count|Всего||Нет измерений|
|totalkeys5|Total Keys (Shard 5)|Count|Максимум||Нет измерений|
|expiredkeys5|Expired Keys (Shard 5)|Count|Всего||Нет измерений|
|usedmemory5|Used Memory (Shard 5)|Байт|Максимум||Нет измерений|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байт|Максимум||Нет измерений|
|serverLoad5|Server Load (Shard 5)|Percent|Максимум||Нет измерений|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime5|CPU (Shard 5)|Percent|Максимум||Нет измерений|
|connectedclients6|Connected Clients (Shard 6)|Count|Максимум||Нет измерений|
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Всего||Нет измерений|
|cachehits6|Cache Hits (Shard 6)|Count|Всего||Нет измерений|
|cachemisses6|Cache Misses (Shard 6)|Count|Всего||Нет измерений|
|getcommands6|Gets (Shard 6)|Count|Всего||Нет измерений|
|setcommands6|Sets (Shard 6)|Count|Всего||Нет измерений|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Count|Максимум||Нет измерений|
|evictedkeys6|Evicted Keys (Shard 6)|Count|Всего||Нет измерений|
|totalkeys6|Total Keys (Shard 6)|Count|Максимум||Нет измерений|
|expiredkeys6|Expired Keys (Shard 6)|Count|Всего||Нет измерений|
|usedmemory6|Used Memory (Shard 6)|Байт|Максимум||Нет измерений|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байт|Максимум||Нет измерений|
|serverLoad6|Server Load (Shard 6)|Percent|Максимум||Нет измерений|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime6|CPU (Shard 6)|Percent|Максимум||Нет измерений|
|connectedclients7|Connected Clients (Shard 7)|Count|Максимум||Нет измерений|
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Всего||Нет измерений|
|cachehits7|Cache Hits (Shard 7)|Count|Всего||Нет измерений|
|cachemisses7|Cache Misses (Shard 7)|Count|Всего||Нет измерений|
|getcommands7|Gets (Shard 7)|Count|Всего||Нет измерений|
|setcommands7|Sets (Shard 7)|Count|Всего||Нет измерений|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Count|Максимум||Нет измерений|
|evictedkeys7|Evicted Keys (Shard 7)|Count|Всего||Нет измерений|
|totalkeys7|Total Keys (Shard 7)|Count|Максимум||Нет измерений|
|expiredkeys7|Expired Keys (Shard 7)|Count|Всего||Нет измерений|
|usedmemory7|Used Memory (Shard 7)|Байт|Максимум||Нет измерений|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байт|Максимум||Нет измерений|
|serverLoad7|Server Load (Shard 7)|Percent|Максимум||Нет измерений|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime7|CPU (Shard 7)|Percent|Максимум||Нет измерений|
|connectedclients8|Connected Clients (Shard 8)|Count|Максимум||Нет измерений|
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Всего||Нет измерений|
|cachehits8|Cache Hits (Shard 8)|Count|Всего||Нет измерений|
|cachemisses8|Cache Misses (Shard 8)|Count|Всего||Нет измерений|
|getcommands8|Gets (Shard 8)|Count|Всего||Нет измерений|
|setcommands8|Sets (Shard 8)|Count|Всего||Нет измерений|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Count|Максимум||Нет измерений|
|evictedkeys8|Evicted Keys (Shard 8)|Count|Всего||Нет измерений|
|totalkeys8|Total Keys (Shard 8)|Count|Максимум||Нет измерений|
|expiredkeys8|Expired Keys (Shard 8)|Count|Всего||Нет измерений|
|usedmemory8|Used Memory (Shard 8)|Байт|Максимум||Нет измерений|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байт|Максимум||Нет измерений|
|serverLoad8|Server Load (Shard 8)|Percent|Максимум||Нет измерений|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime8|CPU (Shard 8)|Percent|Максимум||Нет измерений|
|connectedclients9|Connected Clients (Shard 9)|Count|Максимум||Нет измерений|
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Всего||Нет измерений|
|cachehits9|Cache Hits (Shard 9)|Count|Всего||Нет измерений|
|cachemisses9|Cache Misses (Shard 9)|Count|Всего||Нет измерений|
|getcommands9|Gets (Shard 9)|Count|Всего||Нет измерений|
|setcommands9|Sets (Shard 9)|Count|Всего||Нет измерений|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Count|Максимум||Нет измерений|
|evictedkeys9|Evicted Keys (Shard 9)|Count|Всего||Нет измерений|
|totalkeys9|Total Keys (Shard 9)|Count|Максимум||Нет измерений|
|expiredkeys9|Expired Keys (Shard 9)|Count|Всего||Нет измерений|
|usedmemory9|Used Memory (Shard 9)|Байт|Максимум||Нет измерений|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байт|Максимум||Нет измерений|
|serverLoad9|Server Load (Shard 9)|Percent|Максимум||Нет измерений|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Максимум||Нет измерений|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Максимум||Нет измерений|
|percentProcessorTime9|CPU (Shard 9)|Percent|Максимум||Нет измерений|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Вход сети|Сеть (входящий трафик)|Байт|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Выход сети|Сеть (исходящий трафик)|Байт|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Чтение с диска|BytesPerSecond|Average|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|BytesPerSecond|Average|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|CountPerSecond|Average|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|CountPerSecond|Average|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Вход сети|Сеть (входящий трафик)|Байт|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Выход сети|Сеть (исходящий трафик)|Байт|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Чтение с диска|BytesPerSecond|Average|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|BytesPerSecond|Average|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|CountPerSecond|Average|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Disk Write Operations/Sec|Запись на диски (операций/с)|CountPerSecond|Average|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Всего|Общее число вызовов.|ApiName, Имя_операции, регион|
|SuccessfulCalls|Successful Calls|Count|Всего|Число успешных вызовов.|ApiName, Имя_операции, регион|
|TotalErrors|Total Errors|Count|Всего|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName, Имя_операции, регион|
|BlockedCalls|Blocked Calls|Count|Всего|Число вызовов, превысивших ограничение скорости или квоты.|ApiName, Имя_операции, регион|
|ServerErrors|Server Errors|Count|Всего|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName, Имя_операции, регион|
|ClientErrors|Client Errors|Count|Всего|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName, Имя_операции, регион|
|DataIn|Входящие данные|Байт|Всего|Размер входящих данных в байтах.|ApiName, Имя_операции, регион|
|DataOut|Исходящие данные|Байт|Всего|Размер исходящих данных в байтах.|ApiName, Имя_операции, регион|
|Задержка|Задержка|MilliSeconds|Average|Время задержки в миллисекундах.|ApiName, Имя_операции, регион|
|CharactersTranslated|Символов расшифровано|Count|Всего|Общее количество символов во входящем текстовом запросе.|ApiName, Имя_операции, регион|
|чарактерстраинед|Обученные символы|Count|Всего|Общее количество обученных символов.|ApiName, Имя_операции, регион|
|SpeechSessionDuration|Длительность речевого сеанса|Секунды|Всего|Общая длительность речи в секундах.|ApiName, Имя_операции, регион|
|TotalTransactions|Всего транзакций|Count|Всего|Общее число транзакций.|Нет измерений|
|TotalTokenCalls|Всего вызовов токенов|Count|Всего|Общее число вызовов токенов.|ApiName, Имя_операции, регион|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Вход сети|Оплачиваемый входящий трафик|Байт|Всего|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик)|Нет измерений|
|Выход сети|Оплачиваемый исходящий трафик|Байт|Всего|Число оплачиваемых байтов за все сетевые интерфейсы виртуальных машин (исходящий трафик)|Нет измерений|
|Число байтов, считанных с диска|Скорость чтения с диска|Байт|Всего|Байт считано с диска за период мониторинга|Нет измерений|
|Число байтов, записанных на диск|Скорость записи на диск|Байт|Всего|Число байтов, записанных на диск в течение периода мониторинга|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|CountPerSecond|Average|Операций ввода-вывода с чтением с диска/с|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|CountPerSecond|Average|Операций ввода-вывода с записью на диск/с|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Average|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Average|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с) (устарело)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с) (не рекомендуется)|CountPerSecond|Average|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с (устарело)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду (не рекомендуется)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди (не рекомендуется)|Count|Average|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (устарело)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (устарело)|CountPerSecond|Average|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду (не рекомендуется)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду (не рекомендуется)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Длина очереди ОС на диск|Диск ОС длина очереди (не рекомендуется)|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|CountPerSecond|Average|Байтов в секунду, записанных на один диск за период мониторинга|LUN|
|Операций чтения с диска данных/с|Скорость чтения с диска данных (операций/с) (предварительная версия)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Операций записи на диск данных в секунду|Скорость записи на диск данных (операций/с) (предварительная версия)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Длина очереди диска данных|Глубина очереди диска данных (Предварительная версия)|Count|Average|Длина очереди дисков данных|LUN|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|CountPerSecond|Average|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Нет измерений|
|Операций чтения с диска ОС в секунду|Скорость чтения с диска ОС (операций/с) (предварительная версия)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Операций записи на диск ОС в секунду|Скорость записи на диск ОС (операций/с) (предварительная версия)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Длина очереди диска ОС|Глубина очереди диска ОС (Предварительная версия)|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Входящие потоки|Входящие потоки (Предварительная версия)|Count|Average|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|Нет измерений|
|Исходящие потоки|Исходящие потоки (Предварительная версия)|Count|Average|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|Нет измерений|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков (Предварительная версия)|CountPerSecond|Average|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|Нет измерений|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков (Предварительная версия)|CountPerSecond|Average|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|Нет измерений|
|Попадание в кэш диска данных Premium|Попадание на чтение кэша диска данных Premium (Предварительная версия)|Percent|Average|Попадание в кэш диска данных Premium|LUN|
|Промах чтения кэша на диске данных Premium|Промах чтения кэша на диске данных Premium (Предварительная версия)|Percent|Average|Промах чтения кэша на диске данных Premium|LUN|
|Попадание чтения кэша на диске ОС Premium|Попадание чтения кэша на диске ОС Premium (Предварительная версия)|Percent|Average|Попадание чтения кэша на диске ОС Premium|Нет измерений|
|Промах чтения кэша на диске ОС Premium|Промах чтения кэша диска ОС Premium (Предварительная версия)|Percent|Average|Промах чтения кэша на диске ОС Premium|Нет измерений|
|Суммарный входящий трафик|Суммарный входящий трафик|Байт|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Суммарный исходящий трафик|Суммарный исходящий трафик|Байт|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Загрузка ЦП|Загрузка ЦП|Percent|Average|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Вход сети|Оплачиваемый входящий трафик|Байт|Всего|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик)|VMName|
|Выход сети|Оплачиваемый исходящий трафик|Байт|Всего|Число оплачиваемых байтов за все сетевые интерфейсы виртуальных машин (исходящий трафик)|VMName|
|Число байтов, считанных с диска|Скорость чтения с диска|Байт|Всего|Байт считано с диска за период мониторинга|VMName|
|Число байтов, записанных на диск|Скорость записи на диск|Байт|Всего|Число байтов, записанных на диск в течение периода мониторинга|VMName|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|CountPerSecond|Average|Операций ввода-вывода с чтением с диска/с|VMName|
|Disk Write Operations/Sec|Запись на диски (операций/с)|CountPerSecond|Average|Операций ввода-вывода с записью на диск/с|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Average|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Average|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с) (устарело)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с) (не рекомендуется)|CountPerSecond|Average|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с (устарело)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду (не рекомендуется)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди (не рекомендуется)|Count|Average|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (устарело)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (устарело)|CountPerSecond|Average|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду (не рекомендуется)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду (не рекомендуется)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Нет измерений|
|Длина очереди ОС на диск|Диск ОС длина очереди (не рекомендуется)|Count|Average|Длина очереди дисков ОС|Нет измерений|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN, VMName|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|CountPerSecond|Average|Байтов в секунду, записанных на один диск за период мониторинга|LUN, VMName|
|Операций чтения с диска данных/с|Скорость чтения с диска данных (операций/с) (предварительная версия)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Операций записи на диск данных в секунду|Скорость записи на диск данных (операций/с) (предварительная версия)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Длина очереди диска данных|Глубина очереди диска данных (Предварительная версия)|Count|Average|Длина очереди дисков данных|LUN, VMName|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|CountPerSecond|Average|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|VMName|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|CountPerSecond|Average|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|VMName|
|Операций чтения с диска ОС в секунду|Скорость чтения с диска ОС (операций/с) (предварительная версия)|CountPerSecond|Average|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Операций записи на диск ОС в секунду|Скорость записи на диск ОС (операций/с) (предварительная версия)|CountPerSecond|Average|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Длина очереди диска ОС|Глубина очереди диска ОС (Предварительная версия)|Count|Average|Длина очереди дисков ОС|VMName|
|Входящие потоки|Входящие потоки (Предварительная версия)|Count|Average|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|VMName|
|Исходящие потоки|Исходящие потоки (Предварительная версия)|Count|Average|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|VMName|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков (Предварительная версия)|CountPerSecond|Average|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|VMName|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков (Предварительная версия)|CountPerSecond|Average|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|VMName|
|Попадание в кэш диска данных Premium|Попадание на чтение кэша диска данных Premium (Предварительная версия)|Percent|Average|Попадание в кэш диска данных Premium|LUN, VMName|
|Промах чтения кэша на диске данных Premium|Промах чтения кэша на диске данных Premium (Предварительная версия)|Percent|Average|Промах чтения кэша на диске данных Premium|LUN, VMName|
|Попадание чтения кэша на диске ОС Premium|Попадание чтения кэша на диске ОС Premium (Предварительная версия)|Percent|Average|Попадание чтения кэша на диске ОС Premium|VMName|
|Промах чтения кэша на диске ОС Premium|Промах чтения кэша диска ОС Premium (Предварительная версия)|Percent|Average|Промах чтения кэша на диске ОС Premium|VMName|
|Суммарный входящий трафик|Суммарный входящий трафик|Байт|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Суммарный исходящий трафик|Суммарный исходящий трафик|Байт|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Count|Average|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байт|Average|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байт|Average|Количество байт, полученных по сети в секунду.|Нет измерений|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байт|Average|Количество байт, переданных по сети в секунду.|Нет измерений|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|тоталпуллкаунт|Общее число броских|Count|Average|Общее число опросов изображений|Нет измерений|
|сукцессфулпуллкаунт|Число успешных операций извлечения|Count|Average|Число успешных операций извлечения изображений|Нет измерений|
|тоталпушкаунт|Общее число push-уведомлений|Count|Average|Общее число push-уведомлений для образа|Нет измерений|
|сукцессфулпушкаунт|Число успешных push-уведомлений|Count|Average|Число успешных push-уведомлений образа|Нет измерений|
|рундуратион|Длительность выполнения|мс|Всего|Длительность выполнения в миллисекундах|Нет измерений|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Count|Всего|Общее количество доступных ядер ЦП в управляемом кластере|Нет измерений|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байт|Всего|Общий объем доступной памяти в управляемом кластере|Нет измерений|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Count|Всего|Число модулей pod в состоянии готовности|пространство имен, pod|
|kube_node_status_condition|Состояния для различных условий узла|Count|Всего|Состояния для различных условий узла|условие, состояние, status2, узел|
|kube_pod_status_phase|Число модулей pod по фазам|Count|Всего|Число модулей pod по фазам|фаза, пространство имен, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Count|Всего||Нет измерений|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Всего||Нет измерений|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Count|Всего||Нет измерений|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Count|Всего||Нет измерений|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Всего||Нет измерений|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Count|Всего||Нет измерений|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Count|Всего||Нет измерений|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Секунды|Всего||Нет измерений|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Секунды|Всего||Нет измерений|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Секунды|Всего||Нет измерений|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Секунды|Всего||Нет измерений|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Секунды|Всего||Нет измерений|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Секунды|Всего||Нет измерений|
|ImportASAValuesFailed|Import ASA Values Failed Count|Count|Всего||Нет измерений|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Count|Всего||Нет измерений|
|DCIProfilesCount|Количество экземпляров профиля|Count|Последняя||Нет измерений|
|DCIInteractionsPerMonthCount|Количество взаимодействий в месяц|Count|Последняя||Нет измерений|
|DCIKpisCount|Количество ключевых показателей эффективности|Count|Последняя||Нет измерений|
|DCISegmentsCount|Количество сегментов|Count|Последняя||Нет измерений|
|DCIPredictiveMatchPoliciesCount|Количество операций прогнозного сопоставления|Count|Последняя||Нет измерений|
|DCIPredictionsCount|Количество прогнозов|Count|Последняя||Нет измерений|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. Датабокседже/Датабокседжедевицес

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|никреадсраугхпут|Пропускная способность чтения (сеть)|BytesPerSecond|Average|Пропускная способность чтения сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|никвритесраугхпут|Пропускная способность записи (сеть)|BytesPerSecond|Average|Пропускная способность записи сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|клаудреадсраугхпутпершаре|Пропускная способность скачивания в облаке (общая папка)|BytesPerSecond|Average|Пропускная способность загрузки в Azure из общей папки в течение отчетного периода.|Общий доступ|
|клаудуплоадсраугхпутпершаре|Пропускная способность передачи в облаке (общая папка)|BytesPerSecond|Average|Пропускная способность передачи в Azure из общей папки в течение отчетного периода.|Общий доступ|
|битесуплоадедтоклаудпершаре|Отправлено облачных байт (общий ресурс)|Байт|Average|Общее число байтов, отправленных в Azure из общей папки в течение отчетного периода.|Общий доступ|
|Общая емкость составляет|Общая емкость|Байт|Average|Общая емкость|Нет измерений|
|AvailableCapacity|Доступная емкость|Байт|Average|Доступная емкость в байтах за отчетный период.|Нет измерений|
|клаудуплоадсраугхпут|Пропускная способность отправки для облака|BytesPerSecond|Average|Пропускная способность облачной передачи в Azure за период отчетного периода.|Нет измерений|
|клаудреадсраугхпут|Пропускная способность скачивания в облаке|BytesPerSecond|Average|Пропускная способность скачивания облака в Azure в течение отчетного периода.|Нет измерений|
|битесуплоадедтоклауд|Отправлено облачных байт (устройство)|Байт|Average|Общее число байтов, отправленных в Azure с устройства за отчетный период.|Нет измерений|
|хиперввиртуалпроцессорутилизатион|Вычисление ребра — процент ЦП|Percent|Average|Процент загрузки ЦП|InstanceName|
|хипервмеморютилизатион|Использование памяти при вычислении граничных ресурсов|Percent|Average|Используемый объем ОЗУ|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|FailedRuns и|Неудачные выполнения|Count|Всего||Пипелиненаме, activityName|
|SuccessfulRuns.|Удачные выполнения|Count|Всего||Пипелиненаме, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Count|Всего||FailureType, Name|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Count|Всего||FailureType, Name|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Count|Всего||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Count|Всего||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Count|Всего||Name, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Count|Всего||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байт|Average||IntegrationRuntimeName, NodeName|
|максалловедресаурцекаунт|Максимальное число допустимых сущностей|Count|Максимум||Нет измерений|
|максалловедфакторисизеингбунитс|Максимально допустимый размер фабрики (единица GB)|Count|Максимум||Нет измерений|
|ресаурцекаунт|Общее число сущностей|Count|Максимум||Нет измерений|
|факторисизеингбунитс|Общий размер фабрики (ГБ единиц)|Count|Максимум||Нет измерений|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Всего|Количество успешно выполненных заданий.|Нет измерений|
|JobEndedFailure|Failed Jobs|Count|Всего|Количество невыполненных заданий.|Нет измерений|
|JobEndedCancelled|Отмененные задания|Count|Всего|Число отмененных заданий.|Нет измерений|
|JobAUEndedSuccess|Successful AU Time|Секунды|Всего|Общее время AU успешно выполненных заданий.|Нет измерений|
|JobAUEndedFailure|Failed AU Time|Секунды|Всего|Общее время AU невыполненных заданий.|Нет измерений|
|JobAUEndedCancelled|Время отмены AU|Секунды|Всего|Общее время AU для отмененных заданий.|Нет измерений|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байт|Максимум|Общий объем данных, хранимых в учетной записи.|Нет измерений|
|DataWritten|Записанные данные|Байт|Всего|Общий объем данных, записанных в учетной записи.|Нет измерений|
|DataRead|Данных прочитано|Байт|Всего|Общий объем данных, прочитанных в учетной записи.|Нет измерений|
|WriteRequests|Запросы на запись|Count|Всего|Количество запросов на запись данных в учетную запись.|Нет измерений|
|ReadRequests|Запросы на чтение|Count|Всего|Количество запросов на чтение данных для учетной записи.|Нет измерений|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байт|Average|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байт|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байт|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байт|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Всего|Неудачные подключения|Нет измерений|
|seconds_behind_master|Задержка репликации в секундах|Count|Average|Задержка репликации в секундах|Нет измерений|
|backup_storage_used|Используемое хранилище резервных копий|Байт|Average|Используемое хранилище резервных копий|Нет измерений|
|network_bytes_egress|Выход сети|Байт|Всего|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Вход сети|Байт|Всего|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байт|Average|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байт|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байт|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байт|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Всего|Неудачные подключения|Нет измерений|
|seconds_behind_master|Задержка репликации в секундах|Count|Average|Задержка репликации в секундах|Нет измерений|
|backup_storage_used|Используемое хранилище резервных копий|Байт|Average|Используемое хранилище резервных копий|Нет измерений|
|network_bytes_egress|Выход сети|Байт|Всего|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Вход сети|Байт|Всего|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Percent|Average|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байт|Average|Используемое хранилище|Нет измерений|
|storage_limit|Storage limit|Байт|Average|Storage limit|Нет измерений|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Percent|Average|Процент хранилища для журнала сервера|Нет измерений|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байт|Average|Используемый объем хранилища для журнала сервера|Нет измерений|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байт|Average|Максимальный объем хранилища для журнала сервера|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|connections_failed|Неудачные подключения|Count|Всего|Неудачные подключения|Нет измерений|
|backup_storage_used|Используемое хранилище резервных копий|Байт|Average|Используемое хранилище резервных копий|Нет измерений|
|network_bytes_egress|Выход сети|Байт|Всего|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Вход сети|Байт|Всего|Входящий сетевой трафик по активным подключениям|Нет измерений|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимум|Запаздывание реплики в секундах|Нет измерений|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байт|Максимум|Запаздывание в байтах самой задержкой реплики|Нет измерений|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. Дбфорпостгрескл/serversv2

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Percent|Average|Нагрузка ЦП|Нет измерений|
|memory_percent|Процент памяти|Percent|Average|Процент памяти|Нет измерений|
|/|Операций ввода-вывода в сек.|Count|Average|Операций ввода-вывода в секунду|Нет измерений|
|storage_percent|Процент хранилища|Percent|Average|Процент хранилища|Нет измерений|
|storage_used|Используемое хранилище|Байт|Average|Используемое хранилище|Нет измерений|
|active_connections|Активные подключения|Count|Average|Активные подключения|Нет измерений|
|network_bytes_egress|Выход сети|Байт|Всего|Исходящий сетевой трафик по активным подключениям|Нет измерений|
|network_bytes_ingress|Вход сети|Байт|Всего|Входящий сетевой трафик по активным подключениям|Нет измерений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Всего|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Всего|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Count|Всего|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Count|Всего|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Count|Всего|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Count|Всего|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Count|Всего|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Count|Всего|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|Нет измерений|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Count|Всего|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|Нет измерений|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Count|Всего|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |Нет измерений|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Count|Всего|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|Нет измерений|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|мс|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|мс|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|мс|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events). Эта метрика начинает работать только при включенной маршрутизации https://aka.ms/iotrouting) (для центра Интернета вещей).|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|мс|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events). Эта метрика начинает работать только при включенной маршрутизации https://aka.ms/iotrouting) (для центра Интернета вещей).|Нет измерений|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|мс|Average|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байт|Всего|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Count|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|Нет измерений|
|евентгридделивериес|Сетка событий: доставок (Предварительная версия)|Count|Всего|Число событий центра Интернета вещей, опубликованных в сетке событий. Используйте измерение результат для количества успешных и неудачных запросов. Измерение EventType показывает тип события (https://aka.ms/ioteventgrid).|Результат, EventType|
|евентгридлатенци|Средняя задержка (в миллисекундах) от момента создания события центра Интернета вещей до публикации события в сетке событий. Это число является средним значением между всеми типами событий. Используйте измерение EventType для просмотра задержки определенного типа события.|EventType|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байт|Average|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Всего|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Всего|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байт|Average|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Всего|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Всего|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байт|Average|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байт|Average|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байт|Average|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байт|Average|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Count|Всего|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Count|Всего|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байт|Average|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Всего|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Всего|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Всего|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Всего|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Всего|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Всего|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Всего|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Всего|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Всего|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Всего|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Count|Всего|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Count|Всего|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Всего|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Average|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|
|deviceDataUsage|Общее использование данных устройства|Байт|Всего|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Нет измерений|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Count|Average|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Count|Average|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Count|Всего|Метрики для операций конфигурации|Нет измерений|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Count|Всего|Количество попыток регистрации устройств|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Назначенные устройства|Count|Всего|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Попытки аттестации|Count|Всего|Количество попыток аттестации устройств|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|AvailableStorage|Доступная служба хранилища|Байт|Всего|Общее Доступное хранилище получено по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|CassandraConnectionClosures|Отключение связи Cassandra|Count|Всего|Число закрытых подключений Cassandra, отправленных в отчет с детализацией по 1 минуте|Регион, Клосуререасон|
|CassandraRequestCharges|Расходы запросов по Cassandra|Count|Всего|Для запросов Cassandra используется параметр "RUs"|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Запросы по Cassandra|Count|Count|Число выполненных запросов Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Использование данных|Байт|Всего|Общая степень использования данных в 5 минут|CollectionName, DatabaseName, регион|
|DocumentCount|Число документов|Count|Всего|Общее число документов, о которых сообщило 5 минут|CollectionName, DatabaseName, регион|
|DocumentQuota|Квота на документы|Байт|Всего|Общая квота хранилища, полученная на уровне 5 минут|CollectionName, DatabaseName, регион|
|IndexUsage|Использование индексов|Байт|Всего|Общее использование индекса, полученное по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|MetadataRequests|Запросы метаданных|Count|Count|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|DatabaseName, CollectionName, регион, StatusCode, |
|MongoRequestCharge|Запросить оплату Mongo|Count|Всего|Использованные единицы запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Запросы Mongo|Count|Count|Количество выполненных запросов Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Подготовленная пропускная способность|Count|Максимум|Подготовленная пропускная способность|DatabaseName, CollectionName|
|ReplicationLatency|Задержка репликации P99|MilliSeconds|Average|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|SourceRegion, TargetRegion|
|ServiceAvailability|Доступность службы|Percent|Average|Доступность запросов учетной записи за один час, с детализацией дня или месяца|Нет измерений|
|TotalRequestUnits|Общее количество единиц запросов|Count|Всего|Использованные единицы запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Общее количество запросов|Count|Count|Количество выполненных запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Всего|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|Опубликовать события с ошибками|Count|Всего|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Всего|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|публишсукцесслатенциинмс|Задержка успешной публикации|Count|Всего|Задержка успешной публикации в миллисекундах|Нет измерений|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Count|Всего|Всего событий, которые соответствуют этой подписке на события|Нет измерений|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Всего|Всего событий, которые не удалось доставить в эту подписку на события|Error, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Всего|Всего событий, доставленных в эту подписку на события|Нет измерений|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|мс|Average|Длительность обработки назначения в миллисекундах|Нет измерений|
|DroppedEventCount|Удаленные события|Count|Всего|Всего удаленных событий, которые соответствуют этой подписке на события|дропреасон|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Всего|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Всего|Всего событий, опубликованных в этом разделе|Нет измерений|
|PublishFailCount|События с ошибками|Count|Всего|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Всего|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Нет измерений|
|публишсукцесслатенциинмс|Задержка успешной публикации|Count|Всего|Задержка успешной публикации в миллисекундах|Нет измерений|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Всего|Выполненные запросы для Microsoft.EventHub.|EntityName, |
|ServerErrors|Ошибки сервера.|Count|Всего|Ошибки на сервере для Microsoft.EventHub.|EntityName, |
|UserErrors|Ошибки пользователей.|Count|Всего|Ошибки пользователей для Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Всего|Ошибки превышения квоты для Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Регулируемые запросы.|Count|Всего|Регулируемые запросы для Microsoft.EventHub.|EntityName, |
|IncomingRequests|Входящих запросов|Count|Всего|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Всего|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Всего|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байт|Всего|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байт|Всего|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Число активных подключений для Microsoft.EventHub.|Нет измерений|
|ConnectionsOpened|Открытые подключения.|Count|Average|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Average|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Count|Всего|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Count|Всего|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байт|Всего|Записанные байты для Microsoft.EventHub.|EntityName|
|Size|Size|Байт|Average|Размер EventHub в байтах.|EntityName|
|INREQS|Входящие запросы (не рекомендуется)|Count|Всего|Всего входящих запросов на отправку для пространства имен (не рекомендуется)|Нет измерений|
|SUCCREQ|Успешные запросы (устарело)|Count|Всего|Общее количество успешных запросов для пространства имен (не рекомендуется)|Нет измерений|
|FAILREQ|Неудачные запросы (устарело)|Count|Всего|Всего неудачных запросов для пространства имен (не рекомендуется)|Нет измерений|
|SVRBSY|Ошибки занятости сервера (устарели)|Count|Всего|Общее число ошибок "сервер занят" для пространства имен (не рекомендуется)|Нет измерений|
|INTERR|Внутренние ошибки сервера (не рекомендуется)|Count|Всего|Общее количество внутренних ошибок сервера для пространства имен (не рекомендуется)|Нет измерений|
|MISCERR|Другие ошибки (не рекомендуется)|Count|Всего|Всего неудачных запросов для пространства имен (не рекомендуется)|Нет измерений|
|INMSGS|Входящие сообщения (устарели) (не рекомендуется)|Count|Всего|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих сообщений (не рекомендуется).|Нет измерений|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Count|Всего|Всего входящих сообщений для пространства имен (не рекомендуется)|Нет измерений|
|OUTMSGS|Исходящие сообщения (устарели) (не рекомендуется)|Count|Всего|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих сообщений (не рекомендуется).|Нет измерений|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Count|Всего|Всего исходящих сообщений для пространства имен (не рекомендуется)|Нет измерений|
|EHINMBS|Входящие байты (не рекомендуется) (не рекомендуется)|Байт|Всего|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих байт (не рекомендуется).|Нет измерений|
|EHINBYTES|Входящие байты (не рекомендуется)|Байт|Всего|Пропускная способность входящего сообщения концентратора событий для пространства имен (не рекомендуется)|Нет измерений|
|EHOUTMBS|Исходящие байты (не рекомендуется) (не рекомендуется)|Байт|Всего|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих байт (не рекомендуется).|Нет измерений|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байт|Всего|Пропускная способность исходящего сообщения концентратора событий для пространства имен (не рекомендуется)|Нет измерений|
|EHABL|Архивировать сообщения невыполненной работы (не рекомендуется)|Count|Всего|Архивные сообщения концентратора событий в невыполненной работе для пространства имен (не рекомендуется)|Нет измерений|
|EHAMSGS|Архивирование сообщений (не рекомендуется)|Count|Всего|Архивные сообщения концентратора событий в пространстве имен (не рекомендуется)|Нет измерений|
|EHAMBS|Пропускная способность сообщений архивации (не рекомендуется)|Байт|Всего|Пропускная способность архивного сообщения концентратора событий в пространстве имен (не рекомендуется)|Нет измерений|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Всего|Выполненные запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Всего|Ошибки на сервере для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Всего|Ошибки пользователей для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|QuotaExceededErrors|Ошибки превышения квоты. (предварительная версия)|Count|Всего|Ошибки превышения квоты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Всего|Регулируемые запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Всего|Входящие запросы для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Всего|Входящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Всего|Исходящие сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|IncomingBytes|Входящие байты. (предварительная версия)|Байт|Всего|Входящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|OutgoingBytes|Исходящие байты. (предварительная версия)|Байт|Всего|Исходящие байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Average|Число активных подключений для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Count|Average|Открытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Закрытые подключения. (предварительная версия)|Count|Average|Закрытые подключения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CaptureBacklog|Невыполненная работа записи. (предварительная версия)|Count|Всего|Невыполненная работа записи для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedMessages|Записанные сообщения. (предварительная версия)|Count|Всего|Записанные сообщения для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|CapturedBytes|Записанные байты. (предварительная версия)|Байт|Всего|Записанные байты для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ЦП|CPU (Preview) (ЦП (предварительная версия))|Percent|Максимум|Использование ЦП для кластера концентратора событий в процентах|Role|
|AvailableMemory|Available Memory (Preview) (Доступная память (предварительная версия))|Count|Максимум|Объем доступной памяти для кластера концентратора событий в байтах|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Count|Всего|Число запросов к шлюзу|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Count|Всего|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|ClusterDnsName, HttpStatus|
|нумактивеворкерс|Число активных рабочих ролей|Count|Максимум|Число активных рабочих ролей|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Count|Average|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Count|Average|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Count|Average|Емкость, передаваемая для автомасштабирования при выполнении.|Нет измерений|
|ScaleActionsInitiated|Инициированные действия масштабирования|Count|Всего|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Общедоступная предварительная версия)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|availabilityResults/Аваилабилитиперцентаже|Доступность|Percent|Average|Процент успешно завершенных тестов доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location|
|availabilityResults/Count|Тесты доступности|Count|Count|Число тестов доступности.|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Длительность теста доступности|MilliSeconds|Average|Длительность теста доступности|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Время подключения для загрузки страницы|MilliSeconds|Average|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|Нет измерений|
|browserTimings/processingDuration|Время обработки на стороне клиента|MilliSeconds|Average|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|Нет измерений|
|browserTimings/receiveDuration|Время получения отклика|MilliSeconds|Average|Время от первого до последнего байта или до отключения.|Нет измерений|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Average|Время от установки сетевого подключения до получения первого байта.|Нет измерений|
|browserTimings/totalDuration|Время загрузки страницы браузера|MilliSeconds|Average|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|Нет измерений|
|dependencies/count|Вызовы зависимостей|Count|Count|Число вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Длительность зависимости|MilliSeconds|Average|Длительность вызовов, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Сбои при вызове зависимости|Count|Count|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Просмотры страниц|Count|Count|Число просмотров страниц.|operation/synthetic|
|pageViews/duration|Время загрузки страницы|MilliSeconds|Average|Время загрузки страницы|operation/synthetic|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Average|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-запросы в очереди приложений|Count|Average|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|CountPerSecond|Average|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|CountPerSecond|Average|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|BytesPerSecond|Average|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Загрузка ЦП процесса|Percent|Average|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Загруженность процессора|Percent|Average|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Доступная память|Байт|Average|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Байты исключительного пользования процесса|Байт|Average|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|Время ответа сервера|MilliSeconds|Average|Время с момента получения HTTP-запроса до завершения отправки ответа.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Запросы сервера|Count|Count|Число выполненных запросов HTTP.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Failed requests (Неудачные запросы)|Count|Count|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|запросов и ставок|Частота запросов сервера|CountPerSecond|Average|Частота запросов сервера в секунду|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Исключения|Count|Count|Общее число всех неперехваченных исключений.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Исключения браузера|Count|Count|Число необработанных исключений в браузере.|Нет измерений|
|exceptions/server|Исключения сервера|Count|Count|Число неперехваченных исключений, созданных в серверном приложении.|cloud/roleName, cloud/roleInstance|
|traces/count|Трассировки|Count|Count|Число документов трассировки|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Count|Count|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|мс|Average|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Всего результатов для API службы|Count|Count|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|качеутилизатион|Cache Utilization (Использование кэша)|Percent|Average|Уровень загрузки в области кластера|Отсутствуют|
|QueryDuration|Длительность запросов|мс|Average|Длительность запросов в секундах|Состояние запросов|
|инжестионутилизатион|Использование приема данных|Percent|Average|Доля использованных слотов приема данных в кластере|Отсутствуют|
|Проверки|Проверка активности (Keep Alive)|Count|Average|Проверка работоспособности показывает, что кластер отвечает на запросы|Отсутствуют|
|инжестионволумеинмб|Ingestion Volume (In MB) (Объем приема данных (в МБ))|Count|Всего|Общий объем данных, принятых в кластере (в МБ)|База данных|
|инжестионлатенциинсекондс|Ingestion Latency (In seconds) (Задержка приема данных (в секундах))|Секунды|Average|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|Отсутствуют|
|евентпроцесседфоревенсубс|Events Processed (for Event Hubs) (Обработанные события (для концентраторов событий))|Count|Всего|Число событий, обрабатываемых кластером при приеме данных из концентратора событий|Отсутствуют|
|IngestionResult|Ingestion Result (Результат приема данных)|Count|Count|Число операций приема данных|Status|
|ЦП|ЦП|Percent|Average|Уровень загрузки ЦП|Отсутствуют|
| континуаусекспортнумофрекордсекспортед | Число записей, экспортированных при непрерывном экспорте | Count | Всего | Число записей, экспортируемых для каждого артефакта хранилища, записанного во время операции экспорта  | Отсутствуют |
| експортутилизатион | Использование экспорта | Percent | Максимум | Использование экспорта | Отсутствуют |
| континуаусекспортпендингкаунт | Число ожидающих непрерывного экспорта | Count | Максимум | Число ожидающих заданий непрерывного экспорта, готовых к выполнению | Отсутствуют |
| континуаусекспортмакслатенессминутес | Максимальное время непрерывного экспорта (мин.) | Count | Максимум | Максимальное время в минутах для всех непрерывных экспортов, ожидающих выполнения и готовых к выполнению | Отсутствуют |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Всего|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Count|Всего|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Count|Всего|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Count|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Выполнение отменено|Count|Всего|Число отмененных запусков рабочего процесса.|Нет измерений|
|RunLatency|Run Latency|Секунды|Average|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Average|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Count|Всего|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Percent|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Count|Всего|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Count|Всего|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Count|Всего|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed|Count|Всего|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Count|Всего|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Average|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Average|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Count|Всего|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Count|Всего|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Count|Всего|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Count|Всего|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Count|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Count|Всего|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Count|Всего|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Average|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Average|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Average|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Всего|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|BillableActionExecutions|Billable Action Executions|Count|Всего|Число выполненных действий рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Всего|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Нет измерений|
|TotalBillableExecutions|Total Billable Executions|Count|Всего|Число выполнений рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Всего|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Всего|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Всего|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Всего|Число выполнений внутренних операций, за которые выставляется счет.|Нет измерений|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Всего|Число операций стандартного соединителя, за которые выставляется счет.|Нет измерений|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Всего|Число операций с использованием хранилища, за которые выставляется счет.|Нет измерений|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/Интегратионсервицеенвиронментс

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Всего|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Count|Всего|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Count|Всего|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Count|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Выполнение отменено|Count|Всего|Число отмененных запусков рабочего процесса.|Нет измерений|
|RunLatency|Run Latency|Секунды|Average|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Average|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Count|Всего|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|рунстартсроттледевентс|Запустить регулируемые события запуска|Count|Всего|Число перерегулируемых событий запуска рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Percent|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Count|Всего|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Count|Всего|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Count|Всего|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed |Count|Всего|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Count|Всего|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Average|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Average|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Count|Всего|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Count|Всего|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Count|Всего|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Count|Всего|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Count|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Count|Всего|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Count|Всего|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Average|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Average|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Average|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Всего|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|интегратионсервицеенвиронментворкфловпроцессорусаже|Использование процессора рабочих процессов для среда службы интеграции|Percent|Average|Использование процессора рабочих процессов для среды службы интеграции.|Нет измерений|
|интегратионсервицеенвиронментворкфловмеморюсаже|Использование памяти рабочего процесса для среда службы интеграции|Percent|Average|Использование памяти рабочего процесса для среды службы интеграции.|Нет измерений|
|интегратионсервицеенвиронментконнекторпроцессорусаже|Использование процессора соединителя для среда службы интеграции|Percent|Average|Использование процессора соединителя для среды службы интеграции.|Нет измерений|
|интегратионсервицеенвиронментконнектормеморюсаже|Использование памяти соединителя для среда службы интеграции|Percent|Average|Использование памяти соединителя для среды службы интеграции.|Нет измерений|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Завершенные запуски|Завершенные запуски|Count|Всего|Число выполнений, успешно завершенных для этой рабочей области|Сценарий|
|Запущенные запуски|Запущенные запуски|Count|Всего|Число запусков, запущенных для этой рабочей области|Сценарий|
|Неудачные выполнения|Неудачные выполнения|Count|Всего|Число запусков, завершившихся с этой рабочей областью|Сценарий|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|Апикатегори, ApiName, ResultType, ResponseCode|
|Доступность|Доступность|Percent|Average|Доступность API-интерфейсов|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
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
|VolumeAllocatedSize|Volume allocated size (Выделенный объем тома)|байт|Average|Выделенный размер тома (не фактически используемые байты)|Нет измерений|
|VolumeLogicalSize|Volume logical size (Логический размер тома)|байт|Average|Логический размер тома (используемые байты)|Нет измерений|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|байт|Average|Размер всех моментальных снимков в томе|Нет измерений|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|операций/с|Average|Число операций ввода-вывода в секунду при записи|Нет измерений|
|WriteThroughput|Write throughput (Пропускная способность операций записи)|МB/с|Average|Пропускная способность операций записи в мегабайтах в секунду|Нет измерений|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size (Выделенный размер пула для тома)|байт|Average|Выделенный размер пула (не фактически используемые байты)|Нет измерений|
|VolumePoolAllocatedUsed|Volume pool allocated used (Используемый размер выделенного пула для тома)|байт|Average|Используемый выделенный размер пула|Нет измерений|
|VolumePoolTotalLogicalSize|Volume pool total logical size (Общий логический размер пула для тома)|байт|Average|Сумма логического размера всех томов, входящих в пул|Нет измерений|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size (Общий размер моментальных снимков пула для тома)|байт|Average|Сумма всех моментальных снимков в пуле|Нет измерений|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Count|Всего|Число байт, отправленных сетевым интерфейсом|Нет измерений|
|BytesReceivedRate|Получено байт|Count|Всего|Число байт, полученных сетевым интерфейсом|Нет измерений|
|PacketsSentRate|Отправлено пакетов|Count|Всего|Число пакетов, отправленных сетевым интерфейсом|Нет измерений|
|PacketsReceivedRate|Получено пакетов|Count|Всего|Число пакетов, полученных сетевым интерфейсом|Нет измерений|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Average|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Count|Average|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Количество байтов|Count|Всего|Общее количество байтов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Количество пакетов|Count|Всего|Общее количество пакетов, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Количество пакетов SYN|Count|Всего|Общее количество пакетов SYN, переданных за период времени|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Количество подключений SNAT|Count|Всего|Общее количество подключений SNAT, созданных за период времени|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Count|Всего|Общее число портов SNAT, выделенных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Count|Всего|Общее количество портов SNAT, использованных за период времени|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Всего|Количество запросов, выполненных для зоны DNS|Нет измерений|
|RecordSetCount|Количество наборов записей|Count|Максимум|Количество наборов записей в зоне DNS|Нет измерений|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Percent|Максимум|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|Нет измерений|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|PacketsInDDoS|Входящие пакеты (атака DDoS)|CountPerSecond|Максимум|Входящие пакеты (атака DDoS)|Нет измерений|
|PacketsDroppedDDoS|Удаленные входящие пакеты (атака DDoS)|CountPerSecond|Максимум|Удаленные входящие пакеты (атака DDoS)|Нет измерений|
|PacketsForwardedDDoS|Перенаправленные входящие пакеты (атака DDoS)|CountPerSecond|Максимум|Перенаправленные входящие пакеты (атака DDoS)|Нет измерений|
|TCPPacketsInDDoS|Входящие пакеты TCP (атака DDoS)|CountPerSecond|Максимум|Входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsDroppedDDoS|Удаленные входящие пакеты TCP (атака DDoS)|CountPerSecond|Максимум|Удаленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsForwardedDDoS|Перенаправленные входящие пакеты TCP (атака DDoS)|CountPerSecond|Максимум|Перенаправленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|UDPPacketsInDDoS|Входящие пакеты UDP (атака DDoS)|CountPerSecond|Максимум|Входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsDroppedDDoS|Удаленные входящие пакеты UDP (атака DDoS)|CountPerSecond|Максимум|Удаленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsForwardedDDoS|Перенаправленные входящие пакеты UDP (атака DDoS)|CountPerSecond|Максимум|Перенаправленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|BytesInDDoS|Входящие байты (атака DDoS)|BytesPerSecond|Максимум|Входящие байты (атака DDoS)|Нет измерений|
|BytesDroppedDDoS|Удаленные входящие байты (атака DDoS)|BytesPerSecond|Максимум|Удаленные входящие байты (атака DDoS)|Нет измерений|
|BytesForwardedDDoS|Перенаправленные входящие байты (атака DDoS)|BytesPerSecond|Максимум|Перенаправленные входящие байты (атака DDoS)|Нет измерений|
|TCPBytesInDDoS|Входящие байты TCP (атака DDoS)|BytesPerSecond|Максимум|Входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesDroppedDDoS|Удаленные входящие байты TCP (атака DDoS)|BytesPerSecond|Максимум|Удаленные входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesForwardedDDoS|Перенаправленные входящие байты TCP (атака DDoS)|BytesPerSecond|Максимум|Перенаправленные входящие байты TCP (атака DDoS)|Нет измерений|
|UDPBytesInDDoS|Входящие байты UDP (атака DDoS)|BytesPerSecond|Максимум|Входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesDroppedDDoS|Удаленные входящие байты UDP (атака DDoS)|BytesPerSecond|Максимум|Удаленные входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesForwardedDDoS|Перенаправленные входящие байты UDP (атака DDoS)|BytesPerSecond|Максимум|Перенаправленные входящие байты UDP (атака DDoS)|Нет измерений|
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Count|Максимум|Выполняется ли атака DDoS|Нет измерений|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|CountPerSecond|Максимум|Входящие пакеты TCP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|CountPerSecond|Максимум|Входящие пакеты UDP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|CountPerSecond|Максимум|Входящие пакеты SYN для активации защиты от атаки DDoS|Нет измерений|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Average|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Count|Всего|Общее количество байтов, переданных за период времени|Port, Direction|
|PacketCount|Количество пакетов|Count|Всего|Общее количество пакетов, переданных за период времени|Port, Direction|
|SynCount|Количество пакетов SYN|Count|Всего|Общее количество пакетов SYN, переданных за период времени|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|аппликатионрулехит|Число попаданий правил приложения|Count|Всего|Число попаданий в правилах приложения|Состояние, причина, протокол|
|нетворкрулехит|Число попаданий сетевых правил|Count|Всего|Число попаданий сетевых правил|Состояние, причина, протокол|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|BytesPerSecond|Всего|Количество байтов в секунду, обрабатываемых шлюзом приложений|Нет измерений|
|UnhealthyHostCount|Количество неработоспособных узлов|Count|Average|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Count|Average|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Count|Всего|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|FailedRequests|Неудачные запросы|Count|Всего|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Count|Всего|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Count|Всего|Число текущих установленных подключений к шлюзу приложений|Нет измерений|
|капаЦитюнитс|Текущие единицы мощности|Count|Average|Потребленные единицы мощности|Нет измерений|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|BytesPerSecond|Average|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|BytesPerSecond|Average|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|Нет измерений|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Count|Максимум|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|BytesPerSecond|Average|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байт|Всего|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байт|Всего|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Count|Всего|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Count|Всего|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Count|Всего|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Count|Всего|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Входящий трафик Azure в секунду (в битах)|пирингтипе|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Исходящий трафик Azure в секунду (в битах)|пирингтипе|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Count|Всего|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Count|Максимум|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Percent|Average|Доля проб мониторинга подключения с ошибками, в процентах|Нет измерений|
|AverageRoundtripMs|Среднее Время приема-передачи (мс)|MilliSeconds|Average|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|Нет измерений|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Count|Всего|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Размер запроса|Байт|Всего|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Размер ответа|Байт|Всего|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Число запросов к серверному компоненту|Count|Всего|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Average|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Сервер|
|TotalLatency|Total Latency (Общая задержка)|MilliSeconds|Average|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Percent|Average|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Всего|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Всего|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Нет измерений|
|registration.create|Операции создания регистрации|Count|Всего|Число всех успешных операций создания регистраций.|Нет измерений|
|registration.update|Операции обновления регистрации|Count|Всего|Число всех успешных операций обновления регистраций.|Нет измерений|
|registration.get|Операции чтения регистрации|Count|Всего|Число всех успешных запросов регистрации.|Нет измерений|
|registration.delete|Операции удаления регистрации|Count|Всего|Число всех успешных операций удаления регистраций.|Нет измерений|
|incoming|Входящие сообщения|Count|Всего|Число всех успешно отправленных вызовов API. |Нет измерений|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Всего|Запланированные push-уведомления отменены|Нет измерений|
|incoming.scheduled.cancel|Запланированные push-уведомления отменены|Count|Всего|Запланированные push-уведомления отменены|Нет измерений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Всего|Запланированных уведомлений в состоянии ожидания|Нет измерений|
|installation.all|Операции управления установкой|Count|Всего|Операции управления установкой|Нет измерений|
|installation.get|Получить операции установки|Count|Всего|Получить операции установки|Нет измерений|
|installation.upsert|Операции создания или обновления установки|Count|Всего|Создать или обновить операции установки|Нет измерений|
|installation.patch|Исправить операции установки|Count|Всего|Исправить операции установки|Нет измерений|
|installation.delete|Операции удаления установки|Count|Всего|Удалить операции установки|Нет измерений|
|outgoing.allpns.success|Успешные уведомления|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Всего|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Нет измерений|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Всего|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Нет измерений|
|outgoing.allpns.channelerror|Ошибки канала|Count|Всего|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Нет измерений|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Всего|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Нет измерений|
|outgoing.wns.success|Успешные уведомления WNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Нет измерений|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Нет измерений|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Всего|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Нет измерений|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Нет измерений|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Всего|Windows Live недоступна.|Нет измерений|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Всего|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Нет измерений|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Всего|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Нет измерений|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Всего|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Нет измерений|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Всего|Полезные данные уведомления слишком большие (состояние WNS: 413).|Нет измерений|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Нет измерений|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS — отключено).|Нет измерений|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Нет измерений|
|outgoing.wns.pnserror|Ошибки WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи с WNS.|Нет измерений|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Нет измерений|
|outgoing.apns.success|Успешные уведомления APNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Всего|Число push-уведомлений со сбоем из-за недопустимого токена (код состояния для двоичного протокола APNS: 8. Код состояния протокола HTTP APNS: 400 с "Баддевицетокен").|Нет измерений|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Всего|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Нет измерений|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Всего|Число push-уведомлений, которые завершились со сбоем, так как полезные данные слишком велики (код состояния для двоичного протокола APNS: 7).|Нет измерений|
|outgoing.apns.pnserror|Ошибки APNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с APNS.|Нет измерений|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Нет измерений|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Всего|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Нет измерений|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Нет измерений|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Всего|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Нет измерений|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Нет измерений|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Всего|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Нет измерений|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Всего|Количество неудачных отправлений из-за ошибок связи с GCM.|Нет измерений|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Нет измерений|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Нет измерений|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Нет измерений|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Нет измерений|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Всего|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Нет измерений|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Всего|Количество отправок, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Нет измерений|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с MPNS.|Нет измерений|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|notificationhub.pushes|Все исходящие уведомления|Count|Всего|Все исходящие уведомления из центра уведомлений.|Нет измерений|
|incoming.all.requests|Все входящие запросы|Count|Всего|Общее количество входящих запросов для концентратора уведомлений|Нет измерений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Всего|Общее количество неудачных входящих запросов для концентратора уведомлений|Нет измерений|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
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
|Average_Uptime|Время работы|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Пользователи|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Count|Average|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (с)|Count|Average|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Count|Average|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Count|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Count|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Count|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Процент свободного места|Count|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Доступная память в МБ|Count|Average|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Count|Average|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Полученных байтов/с|Count|Average|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Отправленных байтов/с|Count|Average|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Всего байтов/с|Count|Average|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% загруженности процессора|Count|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Длина очереди процессора|Count|Average|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Пульс|Пульс|Count|Всего|Пульс|Computer, OSType, Version, SourceComputerId|
|Обновление|Обновление|Count|Average|Обновление|Computer, Product, Classification, UpdateState, Optional, Approved|
|событие|событие|Count|Average|событие|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|мс|Average|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Average|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Count|Всего|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байт|Average|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Percent|Average|Среднее значение пробуксовки памяти.|Нет измерений|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Всего|Выполненные ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Всего|ClientError в ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Всего|ServerError в ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Всего|Выполненные SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Всего|ClientError в SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Всего|ServerError в SenderConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Всего|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Всего|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Всего|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Всего|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Всего|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Всего|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Всего|Всего SenderDisconnects для Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Average|Среднее время задержки поиска для службы поиска.|Нет измерений|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Average|Число поисковых запросов в секунду для службы поиска.|Нет измерений|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Percent|Average|Процент отрегулированных поисковых запросов для службы поиска.|Нет измерений|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Всего|Общее количество выполненных запросов для пространства имен (предварительная версия)|EntityName|
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Всего|Ошибки на сервере для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Всего|Ошибки пользователей для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Всего|Регулируемые запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Всего|Входящие запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Всего|Входящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Всего|Исходящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Всего|Число активных подключений для Microsoft.ServiceBus. (предварительная версия)|Нет измерений|
|Size|Размер (предварительная версия)|Байт|Average|Размер очереди или раздела в байтах. (предварительная версия)|EntityName|
|Сообщения|Число сообщений в очереди или разделе. (предварительная версия)|Count|Average|Число сообщений в очереди или разделе. (предварительная версия)|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе. (предварительная версия)|Count|Average|Число активных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|деадлеттередмессажес|Количество недоставленных сообщений в очереди или разделе. (предварительная версия)|Count|Average|Количество недоставленных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|счедуледмессажес|Число запланированных сообщений в очереди или разделе. (предварительная версия)|Count|Average|Число запланированных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|CPUXNS|CPU usage per namespace|Percent|Максимум|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|Нет измерений|
|WSXNS|Memory size usage per namespace|Percent|Максимум|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|Нет измерений|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. Сервицефабрикмеш/приложения

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|аллокатедкпу|аллокатедкпу|Count|Average|ЦП, выделенный этому контейнеру в миллиардах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|аллокатедмемори|аллокатедмемори|Байт|Average|Память, выделенная для этого контейнера, в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|актуалкпу|актуалкпу|Count|Average|Фактическое использование ЦП в миллиардах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|актуалмемори|актуалмемори|Байт|Average|Фактическое использование памяти в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|График|График|Percent|Average|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|меморютилизатион|меморютилизатион|Percent|Average|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|аппликатионстатус|аппликатионстатус|Count|Average|Состояние Service Fabric приложения сетки|ApplicationName, состояние|
|сервицестатус|сервицестатус|Count|Average|Состояние работоспособности службы в Service Fabric приложении для сетки|ApplicationName, состояние, ServiceName|
|сервицерепликастатус|сервицерепликастатус|Count|Average|Состояние работоспособности реплики службы в Service Fabric приложении сети|ApplicationName, состояние, ServiceName, Сервицерепликанаме|
|контаинерстатус|контаинерстатус|Count|Average|Состояние контейнера в Service Fabric приложении сетки|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме, состояние|
|рестарткаунт|рестарткаунт|Count|Average|Число перезапусков контейнера в Service Fabric приложении сетки|ApplicationName, Status, ServiceName, Сервицерепликанаме, Кодепаккаженаме|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Count|Максимум|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Число сообщений|Count|Всего|Общее количество сообщений.|Нет измерений|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байт|Всего|Входящий трафик службы|Нет измерений|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байт|Всего|Исходящий трафик службы|Нет измерений|
|UserErrors|Ошибки пользователей|Percent|Максимум|Процент ошибок пользователей|Нет измерений|
|SystemErrors|Системные ошибки|Percent|Максимум|Процент системных ошибок|Нет измерений|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Процент ЦП|Percent|Average|Процент ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода-вывода данных|Percent|Average|Процент операций ввода-вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Percent|Average|Процент операций ввода-вывода журнала. Неприменимо к хранилищам данных.|Нет измерений|
|dtu_consumption_percent|Процент DTU|Percent|Average|Процент DTU. Применяется к базам данных на основе DTU.|Нет измерений|
|хранилище|Используемое место для данных|Байт|Максимум|Общий размер базы данных. Неприменимо к хранилищам данных.|Нет измерений|
|connection_successful|Успешные подключения|Count|Всего|Успешные подключения|Нет измерений|
|connection_failed|Неудачные подключения|Count|Всего|Неудачные подключения|Нет измерений|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Всего|Заблокировано брандмауэром|Нет измерений|
|взаимоблокировка|Взаимоблокировки|Count|Всего|Взаимоблокировок. Неприменимо к хранилищам данных.|Нет измерений|
|storage_percent|Процент использования пространства данных|Percent|Максимум|Размер базы данных в процентах. Неприменимо к хранилищам данных или базам данных с масштабированием.|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Percent|Average|Процент хранения выполняющейся в памяти OLTP. Неприменимо к хранилищам данных.|Нет измерений|
|workers_percent|Процент рабочих ролей|Percent|Average|Процент рабочих ролей. Неприменимо к хранилищам данных.|Нет измерений|
|sessions_percent|Процент сеансов|Percent|Average|Процент сеансов. Неприменимо к хранилищам данных.|Нет измерений|
|dtu_limit|Лимит DTU|Count|Average|Предел DTU. Применяется к базам данных на основе DTU.|Нет измерений|
|dtu_used|DTU used|Count|Average|Использованные DTU. Применяется к базам данных на основе DTU.|Нет измерений|
|cpu_limit|Ограничение ЦП|Count|Average|Ограничение ЦП. Применяется к базам данных на основе виртуальное ядро.|Нет измерений|
|cpu_used|Используемый ЦП|Count|Average|ЦП используется. Применяется к базам данных на основе виртуальное ядро.|Нет измерений|
|dwu_limit|Лимит DWU|Count|Максимум|Ограничение DWU. Применяется только к хранилищам данных.|Нет измерений|
|dwu_consumption_percent|DWU percentage|Percent|Максимум|DWU в процентах. Применяется только к хранилищам данных.|Нет измерений|
|dwu_used|DWU used|Count|Максимум|Используется DWU. Применяется только к хранилищам данных.|Нет измерений|
|dw_cpu_percent|Процент использования ЦП на уровне узла DW|Percent|Average|Процент использования ЦП на уровне узла DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Процент операций ввода-вывода данных на уровне узла DW|Percent|Average|Процент операций ввода-вывода данных на уровне узла DW|DwLogicalNodeId|
|cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Percent|Максимум|Процент попаданий в кэш. Применяется только к хранилищам данных.|Нет измерений|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Percent|Максимум|Процент использованного кэша. Применяется только к хранилищам данных.|Нет измерений|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Percent|Average|Процент локальной базы данных tempdb. Применяется только к хранилищам данных.|Нет измерений|
|app_cpu_billed|Выставление счетов за использование ЦП приложениями|Count|Всего|Выплата за ЦП приложения. Применяется к бессерверным базам данных.|Нет измерений|
|app_cpu_percent|Процент загрузки ЦП приложения|Percent|Average|Процент загрузки ЦП приложения. Применяется к бессерверным базам данных.|Нет измерений|
|app_memory_percent|Процент используемой памяти приложения|Percent|Average|Процент используемой памяти приложения. Применяется к бессерверным базам данных.|Нет измерений|
|allocated_data_storage|Выделено места для данных|Байт|Average|Выделенное пространство данных. Неприменимо к хранилищам данных.|Нет измерений|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|cpu_percent|Процент ЦП|Percent|Average|Процент ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода-вывода данных|Percent|Average|Процент операций ввода-вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Percent|Average|Процент операций ввода-вывода журнала|Нет измерений|
|dtu_consumption_percent|Процент DTU|Percent|Average|Процент DTU. Применяется к эластичным пулам на основе DTU.|Нет измерений|
|storage_percent|Процент использования пространства данных||Percent|Average|Storage percentage|Нет измерений|
|workers_percent|Процент рабочих ролей|Percent|Average|Процент рабочих ролей|Нет измерений|
|sessions_percent|Процент сеансов|Percent|Average|Процент сеансов|Нет измерений|
|eDTU_limit|eDTU limit|Count|Average|предел eDTU. Применяется к эластичным пулам на основе DTU.|Нет измерений|
|storage_limit|Максимальный размер данных|Байт|Average|Storage limit|Нет измерений|
|eDTU_used|eDTU used|Count|Average|используемые eDTU. Применяется к эластичным пулам на основе DTU.|Нет измерений|
|storage_used|Используемое место для данных|Байт|Average|Используемое хранилище|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Percent|Average|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|
|cpu_limit|Ограничение ЦП|Count|Average|Ограничение ЦП. Применяется к эластичным пулам на основе виртуальное ядро.|Нет измерений|
|cpu_used|Используемый ЦП|Count|Average|ЦП используется. Применяется к эластичным пулам на основе виртуальное ядро.|Нет измерений|
|allocated_data_storage|Выделено места для данных|Байт|Average|Выделено места для данных|Нет измерений|
|allocated_data_storage_percent|Процент выделенного пространства данных|Percent|Максимум|Процент выделенного пространства данных|Нет измерений|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Count|Average|Virtual core count (Число виртуальных ядер)|Нет измерений|
|avg_cpu_percent|Average CPU percentage (Средний процент использования ЦП)|Percent|Average|Average CPU percentage (Средний процент использования ЦП)|Нет измерений|
|reserved_storage_mb|Зарезервированное дисковое пространство|Count|Average|Зарезервированное дисковое пространство|Нет измерений|
|storage_space_used_mb|Использованное дисковое пространство|Count|Average|Использованное дисковое пространство|Нет измерений|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Count|Average|IO requests count (Количество запросов ввода-вывода)|Нет измерений|
|io_bytes_read|Количество считанных байт ввода-вывода|Байт|Average|Количество считанных байт ввода-вывода|Нет измерений|
|io_bytes_written|Количество записанных байт ввода-вывода|Байт|Average|Количество записанных байт ввода-вывода|Нет измерений|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байт|Average|Используемая емкость учетной записи|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байт|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байт|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Серверная задержка успешного запроса|мс|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Сквозная задержка успешного запроса|мс|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BlobCapacity|Емкость BLOB-объектов|Байт|Average|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Число BLOB-объектов|Count|Всего|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType|       |BlobCount|Число BLOB-объектов|Count|Average|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Число контейнеров BLOB-объектов|Count|Average|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Нет измерений|
|IndexCapacity|Емкость индекса|Байт|Average|Объем хранилища, используемый ADLS 2-го поколения (иерархический) индекс в байтах.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байт|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байт|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Серверная задержка успешного запроса|мс|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Сквозная задержка успешного запроса|мс|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|FileCapacity|Емкость файлов|Байт|Average|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|Нет измерений|
|FileCount|Количество файлов|Count|Average|Количество файлов в службе файлов учетной записи хранения.|Нет измерений|
|FileShareCount|Число общих папок|Count|Average|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байт|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байт|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Серверная задержка успешного запроса|мс|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Сквозная задержка успешного запроса|мс|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байт|Average|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Нет измерений|
|QueueCount|Счетчик очередей|Count|Average|Количество очередей в службе очередей учетной записи хранения.|Нет измерений|
|QueueMessageCount|Число сообщений в очереди|Count|Average|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байт|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байт|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Серверная задержка успешного запроса|мс|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Сквозная задержка успешного запроса|мс|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байт|Average|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|Нет измерений|
|TableCount|Число таблиц|Count|Average|Количество таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|TableEntityCount|Число сущностей таблиц|Count|Average|Количество сущностей таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName, Authentication|
|Входящий трафик|Входящий трафик|Байт|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Authentication|
|Исходящие|Исходящие|Байт|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Серверная задержка успешного запроса|мс|Average|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Сквозная задержка успешного запроса|мс|Average|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Authentication|
|Доступность|Доступность|Percent|Average|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/Сторажесинксервицес

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|серверсинксессионресулт|Результат сеанса синхронизации|Count|Average|Метрика, которая регистрирует значение 1 каждый раз, когда конечная точка сервера успешно завершает сеанс синхронизации с конечной точкой облака.|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксинксессионапплиедфилескаунт|Синхронизировано файлов|Count|Всего|Число синхронизированных файлов|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксинксессионперитемеррорскаунт|Несинхронизирующиеся файлы|Count|Всего|Число файлов, которые не удалось синхронизировать|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинкбатчтрансферредфилебитес|Синхронизировано байт|Байт|Всего|Общий размер файлов, переданных для сеансов синхронизации|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксерверхеартбеат|Сетевой статус сервера|Count|Максимум|Метрика, которая регистрирует значение 1 каждый раз, когда зарегистрированный сервер успешно зарегистрирует пульс с конечной точкой облака.|имя_сервера;|
|сторажесинкрекаллиототалсизебитес|Отзыв уровней в облаке|Байт|Всего|Общий размер данных, отозванных сервером|имя_сервера;|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Percent|Максимум|Использование единиц потоковой передачи (%)|LogicalName, PartitionId|
|InputEvents|Входные события|Count|Всего|Входные события|LogicalName, PartitionId|
|InputEventBytes|Байт входных событий|Байт|Всего|Байт входных событий|LogicalName, PartitionId|
|LateInputEvents|События позднего ввода|Count|Всего|Поздние входные события|LogicalName, PartitionId|
|OutputEvents|Выходные события|Count|Всего|Выходные события|LogicalName, PartitionId|
|ConversionErrors|Ошибки преобразования данных|Count|Всего|Ошибки преобразования данных|LogicalName, PartitionId|
|Ошибки|Ошибки времени выполнения|Count|Всего|Ошибки времени выполнения|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|События, поступающие не по порядку|Count|Всего|События, поступающие не по порядку|LogicalName, PartitionId|
|AMLCalloutRequests|Запросы функций|Count|Всего|Запросы функций|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Всего|Неудачные запросы функций|LogicalName, PartitionId|
|AMLCalloutInputEvents|События функций|Count|Всего|События функций|LogicalName, PartitionId|
|DeserializationError|Ошибки ввода десериализации|Count|Всего|Ошибки ввода десериализации|LogicalName, PartitionId|
|EarlyInputEvents|Ранние входные события|Count|Всего|Ранние входные события|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимум|Предельная задержка|LogicalName, PartitionId|
|инпутевентссаурцесбакклогжед|Необработанные входные события|Count|Максимум|Необработанные входные события|LogicalName, PartitionId|
|инпутевентссаурцесперсеконд|Полученные входные источники|Count|Всего|Полученные входные источники|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Всего|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Всего|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байт|Всего|Количество байтов, полученных от всех источников событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байт|Всего|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Count|Всего|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимум|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Average|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Count|Максимум|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|Нет измерений|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Count|Максимум|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|Нет измерений|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Всего|Количество сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Всего|Количество недопустимых сообщений, считанных из источника событий|Нет измерений|
|IngressReceivedBytes|Получено байт входящих данных|Байт|Всего|Количество байтов, считанных из источника событий|Нет измерений|
|IngressStoredBytes|Хранится байтов входящих данных|Байт|Всего|Общий размер событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressStoredEvents|Хранится событий входящих данных|Count|Всего|Количество сведенных событий, успешно обработанных и доступных для запросов|Нет измерений|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимум|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Нет измерений|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Average|Разница между порядковым номером последнего сообщения в очереди событий источника секции и порядковым номером сообщения, обрабатываемого во входящих сообщениях|Нет измерений|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Count|Максимум|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|Нет измерений|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Count|Максимум|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|Нет измерений|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. Вмвареклаудсимпле/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|дискреадбитесперсеконд|Disk Read Bytes/Sec|BytesPerSecond|Average|Средняя пропускная способность диска из-за операций чтения за период выборки.|Нет измерений|
|дисквритебитесперсеконд|Disk Write Bytes/Sec|BytesPerSecond|Average|Средняя пропускная способность диска из-за операций записи за период выборки.|Нет измерений|
|Число байтов, считанных с диска|Скорость чтения с диска|Байт|Всего|Общая пропускная способность диска из-за операций чтения за период выборки.|Нет измерений|
|Число байтов, записанных на диск|Скорость записи на диск|Байт|Всего|Общая пропускная способность диска из-за операций записи за период выборки.|Нет измерений|
|дискреадоператионс|Операции чтения с диска|Count|Всего|Число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Нет измерений|
|дисквритеоператионс|Операции записи на диск|Count|Всего|Число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|CountPerSecond|Average|Среднее число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|CountPerSecond|Average|Среднее число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Нет измерений|
|дискреадлатенци|Задержка чтения с диска|мс|Average|Общая задержка чтения. Сумма задержек чтения устройства и ядра.|Нет измерений|
|дискврителатенци|Задержка записи на диск|мс|Average|Общая задержка записи. Сумма задержек при записи в устройство и ядро.|Нет измерений|
|нетворкинбитесперсеконд|Сеть в байтах/с|BytesPerSecond|Average|Средняя пропускная способность сети для полученного трафика.|Нет измерений|
|нетворкаутбитесперсеконд|Сетевых исходящих байт/с|BytesPerSecond|Average|Средняя пропускная способность сети для передаваемого трафика.|Нет измерений|
|Вход сети|Сеть (входящий трафик)|Байт|Всего|Общая пропускная способность сети для полученного трафика.|Нет измерений|
|Выход сети|Сеть (исходящий трафик)|Байт|Всего|Общая пропускная способность сети для передаваемого трафика.|Нет измерений|
|MemoryUsed|Используемая память|Байт|Average|Объем памяти компьютера, используемой виртуальной машиной.|Нет измерений|
|меморигрантед|Предоставленная память|Байт|Average|Объем памяти, предоставленной виртуальной машине узлом. Память не предоставляется узлу до тех пор, пока она не будет затронута один раз и выделенная память может быть переключена или выведена из появления, если Вмкернел нуждается в памяти.|Нет измерений|
|меморяктиве|Память активна|Байт|Average|Объем памяти, используемой виртуальной машиной в прошлом маленьком окне времени. Это "истинное" количество памяти, которое в настоящее время требуется виртуальной машине. Дополнительная неиспользуемая память может быть переключена или повышена, что не влияет на производительность гостевой системы.|Нет измерений|
|Загрузка ЦП|Загрузка ЦП|Percent|Average|Загрузка ЦП. Это значение указывается в 100%, представляющем все ядра процессора в системе. Например, 2-сторонняя виртуальная машина, использующая 50% системы из четырех ядер, полностью использует два ядра.|Нет измерений|
|перцентажекпуреади|Процент готовности ЦП|мс|Всего|Время готовности — это время, затрачиваемое на ожидание доступности ЦП (ов) за последний интервал обновления.|Нет измерений|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|CpuPercentage|Процент ЦП|Percent|Average|Процент ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент памяти|Экземпляр|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Экземпляр|
|HttpQueueLength|Длина очереди HTTP|Count|Average|Длина очереди HTTP|Экземпляр|
|BytesReceived|Входящие данные:|Байт|Всего|Входящие данные|Экземпляр|
|BytesSent|Исходящие данные|Байт|Всего|Исходящие данные|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|CpuTime|Время ЦП|Секунды|Всего|Время ЦП|Экземпляр|
|Запрошено|Запрошено|Count|Всего|Запрошено|Экземпляр|
|BytesReceived|Входящие данные:|Байт|Всего|Входящие данные|Экземпляр|
|BytesSent|Исходящие данные|Байт|Всего|Исходящие данные|Экземпляр|
|Http101|HTTP 101|Count|Всего|HTTP 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|Http 403|Count|Всего|Http 403|Экземпляр|
|Http404|Http 404|Count|Всего|Http 404|Экземпляр|
|Http406|HTTP 406:|Count|Всего|Http 406|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx|Экземпляр|
|Http5xx|Ошибки сервера Http|Count|Всего|Ошибки сервера Http|Экземпляр|
|MemoryWorkingSet|Рабочий набор памяти|Байт|Average|Рабочий набор памяти|Экземпляр|
|AverageMemoryWorkingSet|Средний рабочий набор памяти|Байт|Average|Средний рабочий набор памяти|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа|Экземпляр|
|AppConnections|Подключения|Count|Average|Подключения|Экземпляр|
|Маркеры|Счетчик дескрипторов|Count|Average|Счетчик дескрипторов|Экземпляр|
|Потоки|Число потоков|Count|Average|Число потоков|Экземпляр|
|PrivateBytes|Частные байты|Байт|Average|Частные байты|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Всего|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байт|Всего|Входящие данные|Экземпляр|
|BytesSent|Исходящие данные|Байт|Всего|Исходящие данные|Экземпляр|
|Http5xx|Ошибки сервера Http|Count|Всего|Ошибки сервера Http|Экземпляр|
|MemoryWorkingSet|Рабочий набор памяти|Байт|Average|Рабочий набор памяти|Экземпляр|
|AverageMemoryWorkingSet|Средний рабочий набор памяти|Байт|Average|Средний рабочий набор памяти|Экземпляр|
|FunctionExecutionUnits|Единицы выполнения функции|МБ/МС|Всего|[Единицы выполнения функции](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Всего|Число выполнений функции|Экземпляр|
|PrivateBytes|Частные байты|Байт|Average|Частные байты|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Всего|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|CpuTime|Время ЦП|Секунды|Всего|Время ЦП|Экземпляр|
|Запрошено|Запрошено|Count|Всего|Запрошено|Экземпляр|
|BytesReceived|Входящие данные:|Байт|Всего|Входящие данные|Экземпляр|
|BytesSent|Исходящие данные|Байт|Всего|Исходящие данные|Экземпляр|
|Http101|HTTP 101|Count|Всего|HTTP 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|Http 403|Count|Всего|Http 403|Экземпляр|
|Http404|Http 404|Count|Всего|Http 404|Экземпляр|
|Http406|HTTP 406:|Count|Всего|Http 406|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx|Экземпляр|
|Http5xx|Ошибки сервера Http|Count|Всего|Ошибки сервера Http|Экземпляр|
|MemoryWorkingSet|Рабочий набор памяти|Байт|Average|Рабочий набор памяти|Экземпляр|
|AverageMemoryWorkingSet|Средний рабочий набор памяти|Байт|Average|Средний рабочий набор памяти|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Count|Всего|Единицы выполнения функции|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Всего|Число выполнений функции|Экземпляр|
|AppConnections|Подключения|Count|Average|Подключения|Экземпляр|
|Маркеры|Счетчик дескрипторов|Count|Average|Счетчик дескрипторов|Экземпляр|
|Потоки|Число потоков|Count|Average|Число потоков|Экземпляр|
|PrivateBytes|Частные байты|Байт|Average|Частные байты|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|BytesPerSecond|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|BytesPerSecond|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Average|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Average|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Average|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Average|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Всего|Сборок мусора поколения 2|Экземпляр|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|Запрошено|Запрошено|Count|Всего|Запрошено|Экземпляр|
|BytesReceived|Входящие данные:|Байт|Всего|Входящие данные|Экземпляр|
|BytesSent|Исходящие данные|Байт|Всего|Исходящие данные|Экземпляр|
|Http101|HTTP 101|Count|Всего|HTTP 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|Http 403|Count|Всего|Http 403|Экземпляр|
|Http404|Http 404|Count|Всего|Http 404|Экземпляр|
|Http406|HTTP 406:|Count|Всего|Http 406|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx|Экземпляр|
|Http5xx|Ошибки сервера Http|Count|Всего|Ошибки сервера Http|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Average|Среднее время ответа|Экземпляр|
|CpuPercentage|Процент ЦП|Percent|Average|Процент ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент памяти|Экземпляр|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Экземпляр|
|HttpQueueLength|Длина очереди HTTP|Count|Average|Длина очереди HTTP|Экземпляр|
|ActiveRequests|Активные запросы|Count|Всего|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Average|Общее число внешних интерфейсов|Нет измерений|
|SmallAppServicePlanInstances|Рабочие роли малого плана служб приложений|Count|Average|Рабочие роли малого плана служб приложений|Нет измерений|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Средняя|Рабочие роли среднего плана служб приложений|Нет измерений|
|LargeAppServicePlanInstances|Рабочие роли большого плана служб приложений|Count|Average|Рабочие роли большого плана служб приложений|Нет измерений|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|Размеры|
|---|---|---|---|---|---|
|WorkersTotal|Всего рабочих ролей|Count|Average|Всего рабочих ролей|Нет измерений|
|WorkersAvailable|Доступные рабочие роли|Count|Average|Доступные рабочие роли|Нет измерений|
|WorkersUsed|Занятых рабочих ролей|Count|Average|Занятых рабочих ролей|Нет измерений|
|CpuPercentage|Процент ЦП|Percent|Average|Процент ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Percent|Average|Процент памяти|Экземпляр|

## <a name="next-steps"></a>Следующие шаги
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создайте оповещения на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](diagnostic-logs-overview.md)
