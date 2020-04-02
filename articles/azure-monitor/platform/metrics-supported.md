---
title: Поддерживаемые метрики Azure Monitor, доступные для каждого типа ресурса
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 674321878cfce2d05189700a8b5118e233d9044d
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520703"
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor

Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor. Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Метрики организованы по пространству имен. Список служб и зон имен, принадлежащих им, можно найти [в ресурсах для служб Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) Для запроса и доступа к этим показателям программно, пожалуйста, используйте [апи-версию 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
> Список метрик платформы, экспортируемых через диагностические настройки, [см.](metrics-supported-export-diagnostic-settings.md)






## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Среднее|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Среднее|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|private_bytes_metric|Байты исключительного пользования|Байты|Среднее|Частные байты.|ServerResourceType|
|virtual_bytes_metric|Байт виртуальной памяти|Байты|Среднее|Виртуальные байты.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Среднее|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Среднее|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Среднее|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Среднее|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Среднее|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Среднее|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Среднее|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: текущие подключения|Count|Среднее|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Среднее|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: использование памяти|Байты|Среднее|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Среднее|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Среднее|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Среднее|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Среднее|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: квота|Байты|Среднее|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Count|Среднее|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Среднее|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Среднее|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Среднее|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Count|Среднее|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Count|Среднее|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Count|Среднее|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Count|Среднее|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Count|Среднее|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Count|Среднее|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Count|Среднее|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Count|Среднее|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Count|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Count|Среднее|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Count|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Среднее|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Count|Среднее|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Count|Среднее|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Count|Среднее|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Count|Среднее|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Среднее|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_private_bytes_metric|M Двигатель Частные Байты|Байты|Среднее|Использование частных байтов в процессах двигателя гибридного производства.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Двигатель Виртуальный Байты|Байты|Среднее|Виртуальные байты использования гибридных процессов двигателя.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Всего запросов шлюза (Обезвращено)|Count|Итог|Количество запросов шлюза - Используйте многомерную метрику запроса с измерением GatewayResponseCodeCategory|Местоположение,Хостимя|
|SuccessfulRequests|Успешные запросы шлюза (Обезвращенные)|Count|Итог|Количество успешных запросов шлюза - Вместо этого используйте метрику запроса с несколькими измерениями с измерением GatewayResponseCodeCategory|Местоположение,Хостимя|
|UnauthorizedRequests|Несанкционированные запросы шлюза (обезвращенные)|Count|Итог|Количество несанкционированных запросов шлюза - Вместо этого используйте метрику запроса с несколькими измерениями с измерением GatewayResponseCodeCategory|Местоположение,Хостимя|
|FailedRequests|Неудачные запросы шлюза (Обезумевший)|Count|Итог|Количество сбоев в запросах шлюза - Вместо этого используйте метрику запроса с несколькими измерениями с измерением GatewayResponseCodeCategory|Местоположение,Хостимя|
|OtherRequests|Другие запросы шлюза (Обезвращенные)|Count|Итог|Количество других запросов шлюза - Вместо этого используйте метрику запроса с несколькими измерениями с измерением GatewayResponseCodeCategory|Местоположение,Хостимя|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Среднее|Общая длительность запросов шлюза в миллисекундах|Местоположение,Хостимя|
|БэкендАсдлительно|Длительность запросов backend|Миллисекунды|Среднее|Длительность запросов backend в миллисекундах|Местоположение,Хостимя|
|Capacity|Capacity|Процент|Среднее|Метрика использования для службы ApiManagement|Расположение|
|EventHubTotalEvents|Всего событий EventHub|Count|Итог|Количество событий, отправленных на EventHub|Расположение|
|EventHubУспешныеСобытия|Успешные события EventHub|Count|Итог|Количество успешных событий EventHub|Расположение|
|EventHubTotalFailedСобытия|Неудачные события EventHub|Count|Итог|Количество неудачных событий EventHub|Расположение|
|EventHubRejectedEvents|Отклоненные события EventHub|Count|Итог|Количество отклоненных событий EventHub (неправильная конфигурация или несанкционированно)|Расположение|
|EventHubThrottledСобытия|Затмили события EventHub|Count|Итог|Количество задушенные события EventHub|Расположение|
|EventHubTimedoutEvents|Приуроченные события EventHub|Count|Итог|Количество приуроченных событий EventHub|Расположение|
|EventHubDroppedEvents|Выпавные события EventHub|Count|Итог|Количество событий, пропущенных из-за ограничения размера очереди, достигнуто|Расположение|
|EventHubTotalBytesСент|Размер событий EventHub|Байты|Итог|Общий размер событий EventHub в байтах|Расположение|
|Requests|Requests|Count|Итог|Метрики запроса шлюза с несколькими измерениями|Местоположение,Hostname,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCode,BackendResponseCodeCategory,GatewayResponseCode|
|Сетевая связь|Статус ресурсов подключения к сети (Предварительный просмотр)|Count|Итог|Состояние сетевой связи зависимых типов ресурсов из службы управления API|Местоположение,РесурсТип|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Общее количество входящих запросов http.|StatusCode|
|HttpIncomingRequestduration|HttpIncomingRequestduration|Count|Среднее|Задержка по запросу http.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Весна

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SystemCpuUsageПроцент|Процент использования процессора системы|Процент|Среднее|Недавнее использование cpu для всей системы|AppName,Pod|
|AppCpuUsageПроцент|Процент использования процессора app|Процент|Среднее|App JVM CPU Процент использования|AppName,Pod|
|AppMemoryЗавершенный|Назначена память приложения|Байты|Среднее|Память, присвоенная JVM в байтах|AppName,Pod|
|AppMemoryUsed|Используется память приложения|Байты|Среднее|Память приложения, используемая в байтах|AppName,Pod|
|AppMemoryMax|App Memory Max|Байты|Максимальная|Максимальное количество памяти в байтах, которые могут быть использованы для управления памятью|AppName,Pod|
|MaxOldGenMemorypoolBytes|Макс Доступный размер данных старого поколения|Байты|Среднее|Максимальный размер пула памяти старого поколения|AppName,Pod|
|OldGenMemorypoolBytes|Размер данных старого поколения|Байты|Среднее|Размер пула памяти старого поколения после полного GC|AppName,Pod|
|OldGenPromotedBytes|Содействие старому размеру данных поколения|Байты|Максимальная|Подсчет положительных увеличений размера пула памяти старого поколения до GC после GC|AppName,Pod|
|YoungGenPromotedBytes|Содействие молодому поколению размер данных|Байты|Максимальная|Приращено для увеличения размера пула памяти молодого поколения после одного GC до следующего|AppName,Pod|
|GCPauseTotalCount|GC Пауза граф|Count|Итог|GC Пауза граф|AppName,Pod|
|GCPauseTotalTime|GC Пауза Общее время|Миллисекунды|Итог|GC Пауза Общее время|AppName,Pod|
|ТомктатСентБайтс|Tomcat Всего отправлены байты|Байты|Итог|Tomcat Всего отправлены байты|AppName,Pod|
|ТомктатАтПолучаетБайт|Томктат Всего получил байты|Байты|Итог|Томктат Всего получил байты|AppName,Pod|
|TomcatRequestTotalTime|Tomcat Запрос Всего Таймс|Миллисекунды|Итог|Tomcat Запрос Всего Таймс|AppName,Pod|
|TomcatRequestTotalCount|Tomcat Запрос Всего Граф|Count|Итог|Tomcat Запрос Всего Граф|AppName,Pod|
|TomcatResponseAvgTime|Tomcat Запрос Среднее время|Миллисекунды|Среднее|Tomcat Запрос Среднее время|AppName,Pod|
|TomcatRequestMaxTime|Tomcat Запрос Макс Время|Миллисекунды|Максимальная|Tomcat Запрос Макс Время|AppName,Pod|
|TomcatОшибкаCount|Глобальная ошибка Tomcat|Count|Итог|Глобальная ошибка Tomcat|AppName,Pod|
|TomcatSessionActiveMaxCount|Томктат Сессия Макс Активный граф|Count|Итог|Томктат Сессия Макс Активный граф|AppName,Pod|
|TomcatSessionLiveMaxTime|Томктат Сессия Макс живое время|Миллисекунды|Максимальная|Томктат Сессия Макс живое время|AppName,Pod|
|TomcatSessionActiveCurrentCount|Томктат Сессия живой граф|Count|Итог|Томктат Сессия живой граф|AppName,Pod|
|TomcatSessionCreatedCount|Томктат Сессия Создан граф|Count|Итог|Томктат Сессия Создан граф|AppName,Pod|
|TomcatSessionExpiredCount|Tomcat Сессия истек граф|Count|Итог|Tomcat Сессия истек граф|AppName,Pod|
|TomcatSessionИзлученоеCount|Томктат Сессия Отклонено граф|Count|Итог|Томктат Сессия Отклонено граф|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Итог|Общее количество заданий|Runbook, Статус|
|TotalUpdateDeploymentDeploymentRuns|Total Update Deployment Runs (Общее количество запусков развертывания обновлений)|Count|Итог|Общее развертывание развертывания программного обеспечения|SoftwareUpdateConfigurationNameName,Статус|
|TotalUpdateDeploymentМашинаПробегRuns|Total Update Deployment Machine Runs (Общее количество запусков развертывания обновлений для компьютера)|Count|Итог|Всего машина развертывания обновления программного обеспечения выполняется в запуске развертывания обновления программного обеспечения|SoftwareUpdateConfigurationNameName,Статус,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Итог|Общее число выделенных ядер в учетной записи пакетной службы.|None|
|TotalNodeCount|Dedicated Node Count|Count|Итог|Общее число выделенных узлов в учетной записи пакетной службы.|None|
|LowPriorityCoreCount|LowPriority Core Count|Count|Итог|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|None|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Итог|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|None|
|CreatingNodeCount|Creating Node Count|Count|Итог|Количество создаваемых узлов.|None|
|StartingNodeCount|Starting Node Count|Count|Итог|Число узлов, которые запускаются.|None|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Итог|Число узлов, ожидающих завершения задачи запуска.|None|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Итог|Число узлов, на которых произошел сбой задачи запуска.|None|
|IdleNodeCount|Idle Node Count|Count|Итог|Количество узлов в неактивном состоянии.|None|
|OfflineNodeCount|Offline Node Count|Count|Итог|Количество узлов не в сети.|None|
|RebootingNodeCount|Rebooting Node Count|Count|Итог|Количество перезапускаемых узлов.|None|
|ReimagingNodeCount|Reimaging Node Count|Count|Итог|Число узлов, для которых пересоздается образ.|None|
|RunningNodeCount|Running Node Count|Count|Итог|Число работающих узлов.|None|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Итог|Количество узлов, покидающих пул.|None|
|UnusableNodeCount|Unusable Node Count|Count|Итог|Число узлов, непригодных для использования.|None|
|PreemptedNodeCount|Preempted Node Count|Count|Итог|Количество замещенных узлов.|None|
|TaskStartEvent|Task Start Events|Count|Итог|Общее число запущенных задач.|poolId,jobId|
|TaskCompleteEvent|Task Complete Events|Count|Итог|Общее число завершенных задач.|poolId,jobId|
|TaskFailEvent|Task Fail Events|Count|Итог|Общее число задач, завершившихся сбоем.|poolId,jobId|
|PoolCreateEvent|Pool Create Events|Count|Итог|Общее число созданных пулов.|poolId|
|PoolResizeStartEvent|Pool Resize Start Events|Count|Итог|Общее число запущенных задач изменения размера пула.|poolId|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Count|Итог|Общее число завершенных задач изменения размера пула.|poolId|
|PoolDeleteStartEvent|Pool Delete Start Events|Count|Итог|Общее число запущенных задач удаления пула.|poolId|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Count|Итог|Общее число завершенных задач удаления пула.|poolId|
|JobDeleteCompleteEvent|Job Delete Complete Events (События окончания удаления задания)|Count|Итог|Общее количество заданий, которые были успешно удалены.|jobId|
|JobDeleteStartEvent|Job Delete Start Events (События начала удаления задания)|Count|Итог|Общее количество заданий, для которых запрошено удаление.|jobId|
|JobDisableCompleteEvent|Job Disable Complete Events (События окончания отключения задания)|Count|Итог|Общее количество заданий, которые были успешно отключены.|jobId|
|JobDisableStartEvent|Job Disable Start Events (События начала отключения задания)|Count|Итог|Общее количество заданий, для которых запрошено отключение.|jobId|
|JobStartEvent|Job Start Events (События запуска задания)|Count|Итог|Общее количество заданий, которые были успешно запущены.|jobId|
|JobTerminateCompleteEvent|Job Terminate Complete Events (События окончания завершения задания)|Count|Итог|Общее количество заданий, которые были успешно завершены.|jobId|
|JobTerminateStartEvent|Job Terminate Start Events (События начала завершения задания)|Count|Итог|Общее количество заданий, для которых запрошено завершение.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/рабочие области

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Задание, отправленное|Задание, отправленное|Count|Итог|Количество представленных заданий|Сценарий,Название кластера|
|Завершена работа|Завершена работа|Count|Итог|Количество выполненных заданий|Сценарий,ГруппаИмя,ResultType|
|Всего узлов|Всего узлов|Count|Среднее|Количество общих узлов|Сценарий,Название кластера|
|Активные узлы|Активные узлы|Count|Среднее|Число работающих узлов.|Сценарий,Название кластера|
|Узлы безделья|Узлы безделья|Count|Среднее|Количество узлов в неактивном состоянии.|Сценарий,Название кластера|
|Непригодные узлы|Непригодные узлы|Count|Среднее|Число узлов, непригодных для использования.|Сценарий,Название кластера|
|Упреждаемые узлы|Упреждаемые узлы|Count|Среднее|Количество замещенных узлов.|Сценарий,Название кластера|
|Оставляя узлы|Оставляя узлы|Count|Среднее|Количество опрокинувших узлов|Сценарий,Название кластера|
|Всего ядер|Всего ядер|Count|Среднее|Количество общих ядер|Сценарий,Название кластера|
|Активные ядра|Активные ядра|Count|Среднее|Количество активных ядер|Сценарий,Название кластера|
|Ядра холостого хода|Ядра холостого хода|Count|Среднее|Количество холостых ядер|Сценарий,Название кластера|
|Непригодные для угобы|Непригодные для угобы|Count|Среднее|Количество непригодных ядер|Сценарий,Название кластера|
|Упреждаемые ядра|Упреждаемые ядра|Count|Среднее|Количество упреждающих ядер|Сценарий,Название кластера|
|Оставляя ядра|Оставляя ядра|Count|Среднее|Количество опрокинувших ядер|Сценарий,Название кластера|
|Процент использования квот|Процент использования квот|Count|Среднее|Процент использованных квот|Сценарий,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuUsageПроцентинвдвойнья|Процент использования процессора|Процент|Максимальная|Процент использования процессора|Узел|
|MemoryUsage|Использование памяти|Байты|Среднее|Использование памяти|Узел|
|MemoryLimit|Предел памяти|Байты|Среднее|Предел памяти|Узел|
|MemoryUsageПроцентинваубл|Процент использования памяти|Процент|Среднее|Процент использования памяти|Узел|
|ХранениеUsage данных|Использование хранилища|Байты|Среднее|Использование хранилища|Узел|
|IOReadBytes|IO Читать Байты|Байты|Итог|IO Читать Байты|Узел|
|IOWriteBytes|IO Написать байты|Байты|Итог|IO Написать байты|Узел|
|ПодключениеПринято|Принятые соединения|Count|Итог|Принятые соединения|Узел|
|ПодключениеОбработано|Обрабатываемые соединения|Count|Итог|Обрабатываемые соединения|Узел|
|ConnectionActive|Активные подключения|Count|Среднее|Активные подключения|Узел|
|ЗапросОбработано|Обработка запросов|Count|Итог|Обработка запросов|Узел|
|Обработанныеблоки|Обработанные блоки|Count|Итог|Обработанные блоки|Узел|
|ОбработанныеСделки|Обработанные транзакции|Count|Итог|Обработанные транзакции|Узел|
|Отложенные сделки|Отложенные транзакции|Count|Среднее|Отложенные транзакции|Узел|
|Очередные сделки|Очередные транзакции|Count|Среднее|Очередные транзакции|Узел|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Count|Максимальная||ShardId|
|totalcommandsprocessed|Всего операций|Count|Итог||ShardId|
|cachehits|Попаданий в кэш|Count|Итог||ShardId|
|cachemisses|Промахов в кэше|Count|Итог||ShardId|
|кэшемиссат|Кэш Мисс Оценить|Процент|кэшемиссат||ShardId|
|getcommands|Операций считывания|Count|Итог||ShardId|
|setcommands|Наборы|Count|Итог||ShardId|
|operationsPerSecond|Количество операций в секунду|Count|Максимальная||ShardId|
|evictedkeys|Исключенные ключи|Count|Итог||ShardId|
|totalkeys|Всего ключей|Count|Максимальная||ShardId|
|expiredkeys|Ключи с истекшим сроком действия|Count|Итог||ShardId|
|usedmemory|Используемая память|Байты|Максимальная||ShardId|
|usedmemorypercentage|Процент используемой памяти|Процент|Максимальная||ShardId|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||ShardId|
|serverLoad|Загрузка сервера|Процент|Максимальная||ShardId|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||ShardId|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||ShardId|
|percentProcessorTime|ЦП|Процент|Максимальная||ShardId|
|cacheLatency|Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))|Count|Среднее||ShardId|
|ошибки|ошибки|Count|Максимальная||ShardId,ОшибкаType|
|connectedclients0|Connected Clients (Shard 0)|Count|Максимальная||None|
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Итог||None|
|cachehits0|Cache Hits (Shard 0)|Count|Итог||None|
|cachemisses0|Cache Misses (Shard 0)|Count|Итог||None|
|getcommands0|Gets (Shard 0)|Count|Итог||None|
|setcommands0|Sets (Shard 0)|Count|Итог||None|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Count|Максимальная||None|
|evictedkeys0|Evicted Keys (Shard 0)|Count|Итог||None|
|totalkeys0|Total Keys (Shard 0)|Count|Максимальная||None|
|expiredkeys0|Expired Keys (Shard 0)|Count|Итог||None|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||None|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||None|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||None|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||None|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||None|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||None|
|connectedclients1|Connected Clients (Shard 1)|Count|Максимальная||None|
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Итог||None|
|cachehits1|Cache Hits (Shard 1)|Count|Итог||None|
|cachemisses1|Cache Misses (Shard 1)|Count|Итог||None|
|getcommands1|Gets (Shard 1)|Count|Итог||None|
|getcommands1|Sets (Shard 1)|Count|Итог||None|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Count|Максимальная||None|
|evictedkeys1|Evicted Keys (Shard 1)|Count|Итог||None|
|totalkeys1|Total Keys (Shard 1)|Count|Максимальная||None|
|expiredkeys1|Expired Keys (Shard 1)|Count|Итог||None|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||None|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||None|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||None|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||None|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||None|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||None|
|connectedclients2|Connected Clients (Shard 2)|Count|Максимальная||None|
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Итог||None|
|cachehits2|Cache Hits (Shard 2)|Count|Итог||None|
|cachemisses2|Cache Misses (Shard 2)|Count|Итог||None|
|getcommands2|Gets (Shard 2)|Count|Итог||None|
|getcommands2|Sets (Shard 2)|Count|Итог||None|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Count|Максимальная||None|
|evictedkeys2|Evicted Keys (Shard 2)|Count|Итог||None|
|totalkeys2|Total Keys (Shard 2)|Count|Максимальная||None|
|expiredkeys2|Expired Keys (Shard 2)|Count|Итог||None|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||None|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||None|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||None|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||None|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||None|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||None|
|connectedclients3|Connected Clients (Shard 3)|Count|Максимальная||None|
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Итог||None|
|cachehits3|Cache Hits (Shard 3)|Count|Итог||None|
|cachemisses3|Cache Misses (Shard 3)|Count|Итог||None|
|getcommands3|Gets (Shard 3)|Count|Итог||None|
|setcommands3|Sets (Shard 3)|Count|Итог||None|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Count|Максимальная||None|
|evictedkeys3|Evicted Keys (Shard 3)|Count|Итог||None|
|totalkeys3|Total Keys (Shard 3)|Count|Максимальная||None|
|expiredkeys3|Expired Keys (Shard 3)|Count|Итог||None|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||None|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||None|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||None|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||None|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||None|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||None|
|connectedclients4|Connected Clients (Shard 4)|Count|Максимальная||None|
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Итог||None|
|cachehits4|Cache Hits (Shard 4)|Count|Итог||None|
|cachemisses4|Cache Misses (Shard 4)|Count|Итог||None|
|getcommands4|Gets (Shard 4)|Count|Итог||None|
|setcommands4|Sets (Shard 4)|Count|Итог||None|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Count|Максимальная||None|
|evictedkeys4|Evicted Keys (Shard 4)|Count|Итог||None|
|totalkeys4|Total Keys (Shard 4)|Count|Максимальная||None|
|expiredkeys4|Expired Keys (Shard 4)|Count|Итог||None|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||None|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||None|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||None|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||None|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||None|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||None|
|connectedclients5|Connected Clients (Shard 5)|Count|Максимальная||None|
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Итог||None|
|cachehits5|Cache Hits (Shard 5)|Count|Итог||None|
|cachemisses5|Cache Misses (Shard 5)|Count|Итог||None|
|getcommands5|Gets (Shard 5)|Count|Итог||None|
|getcommands5|Sets (Shard 5)|Count|Итог||None|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Count|Максимальная||None|
|evictedkeys5|Evicted Keys (Shard 5)|Count|Итог||None|
|totalkeys5|Total Keys (Shard 5)|Count|Максимальная||None|
|expiredkeys5|Expired Keys (Shard 5)|Count|Итог||None|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||None|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||None|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||None|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||None|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||None|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||None|
|connectedclients6|Connected Clients (Shard 6)|Count|Максимальная||None|
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Итог||None|
|cachehits6|Cache Hits (Shard 6)|Count|Итог||None|
|cachemisses6|Cache Misses (Shard 6)|Count|Итог||None|
|getcommands6|Gets (Shard 6)|Count|Итог||None|
|setcommands6|Sets (Shard 6)|Count|Итог||None|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Count|Максимальная||None|
|evictedkeys6|Evicted Keys (Shard 6)|Count|Итог||None|
|totalkeys6|Total Keys (Shard 6)|Count|Максимальная||None|
|expiredkeys6|Expired Keys (Shard 6)|Count|Итог||None|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||None|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||None|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||None|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||None|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||None|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||None|
|connectedclients7|Connected Clients (Shard 7)|Count|Максимальная||None|
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Итог||None|
|cachehits7|Cache Hits (Shard 7)|Count|Итог||None|
|cachemisses7|Cache Misses (Shard 7)|Count|Итог||None|
|getcommands7|Gets (Shard 7)|Count|Итог||None|
|setcommands7|Sets (Shard 7)|Count|Итог||None|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Count|Максимальная||None|
|evictedkeys7|Evicted Keys (Shard 7)|Count|Итог||None|
|totalkeys7|Total Keys (Shard 7)|Count|Максимальная||None|
|expiredkeys7|Expired Keys (Shard 7)|Count|Итог||None|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||None|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||None|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||None|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||None|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||None|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||None|
|connectedclients8|Connected Clients (Shard 8)|Count|Максимальная||None|
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Итог||None|
|cachehits8|Cache Hits (Shard 8)|Count|Итог||None|
|cachemisses8|Cache Misses (Shard 8)|Count|Итог||None|
|getcommands8|Gets (Shard 8)|Count|Итог||None|
|setcommands8|Sets (Shard 8)|Count|Итог||None|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Count|Максимальная||None|
|evictedkeys8|Evicted Keys (Shard 8)|Count|Итог||None|
|totalkeys8|Total Keys (Shard 8)|Count|Максимальная||None|
|expiredkeys8|Expired Keys (Shard 8)|Count|Итог||None|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||None|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||None|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||None|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||None|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||None|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||None|
|connectedclients9|Connected Clients (Shard 9)|Count|Максимальная||None|
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Итог||None|
|cachehits9|Cache Hits (Shard 9)|Count|Итог||None|
|cachemisses9|Cache Misses (Shard 9)|Count|Итог||None|
|getcommands9|Gets (Shard 9)|Count|Итог||None|
|setcommands9|Sets (Shard 9)|Count|Итог||None|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Count|Максимальная||None|
|evictedkeys9|Evicted Keys (Shard 9)|Count|Итог||None|
|totalkeys9|Total Keys (Shard 9)|Count|Максимальная||None|
|expiredkeys9|Expired Keys (Shard 9)|Count|Итог||None|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||None|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||None|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||None|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||None|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||None|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||None|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpoliciepolicies

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Итог|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|Название политики,ПравилоИмя, Действие|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|None|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая Хранилищем Azure для обработки успешного запроса, в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов, сделанных службе хранения или указанной операции API, за миллисекунды. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Count|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|None|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемого индексом ADLS Gen2 (Иерархический) в байтах.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая Хранилищем Azure для обработки успешного запроса, в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов, сделанных службе хранения или указанной операции API, за миллисекунды. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый Хранилищем таблиц учетной записи хранения, в байтах.|None|
|TableCount|Количество таблиц|Count|Среднее|Количество таблиц в Хранилище таблиц учетной записи хранения.|None|
|TableEntityCount|Количество сущностей таблиц|Count|Среднее|Количество сущностей таблиц в Хранилище таблиц учетной записи хранения.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая Хранилищем Azure для обработки успешного запроса, в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов, сделанных службе хранения или указанной операции API, за миллисекунды. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемого службой файлов учетной записи хранилища в байтах.|FileShare|
|FileCount|Количество файлов|Count|Среднее|Количество файлов в службе файла учетной записи хранилища.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Count|Среднее|Количество файлов в службе файла учетной записи хранилища.|None|
|FileShareSnapshotCount|Количество снимков файла|Count|Среднее|Количество снимков, присутствующих на акции в Службе файлов учетной записи хранилища.|FileShare|
|FileShareSnapshotSize|Размер снимка файла доля|Байты|Среднее|Объем хранилища, используемого снимками в службе файла учетной записи хранилища в байтах.|FileShare|
|FileShareКвота|Размер квоты доли файла|Байты|Среднее|Верхний предел объема хранилища, который может быть использован службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация, FileShare|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName,Аутентификация, FileShare|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName,Аутентификация, FileShare|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая Хранилищем Azure для обработки успешного запроса, в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|GeoType, ApiName,Аутентификация, FileShare|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов, сделанных службе хранения или указанной операции API, за миллисекунды. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName,Аутентификация, FileShare|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName,Аутентификация, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|None|
|QueueCount|Количество очередей|Count|Среднее|Количество очередей в службе очередей учетной записи хранения.|None|
|QueueMessageCount|Количество сообщений очереди|Count|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая Хранилищем Azure для обработки успешного запроса, в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов, сделанных службе хранения или указанной операции API, за миллисекунды. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Итог|Общее число вызовов.|ApiName,OperationName,Регион|
|SuccessfulCalls|Successful Calls|Count|Итог|Число успешных вызовов.|ApiName,OperationName,Регион|
|TotalErrors|Total Errors|Count|Итог|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName,OperationName,Регион|
|BlockedCalls|Blocked Calls|Count|Итог|Число вызовов, превысивших ограничение скорости или квоты.|ApiName,OperationName,Регион|
|ServerErrors|Server Errors|Count|Итог|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName,OperationName,Регион|
|ClientErrors|Client Errors|Count|Итог|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName,OperationName,Регион|
|DataIn|Входящие данные:|Байты|Итог|Размер входящих данных в байтах.|ApiName,OperationName,Регион|
|DataOut|Выходные данные|Байты|Итог|Размер исходящих данных в байтах.|ApiName,OperationName,Регион|
|Задержка|Задержка|MilliSeconds|Среднее|Время задержки в миллисекундах.|ApiName,OperationName,Регион|
|TotalTokenCalls|Всего вызовов токенов|Count|Итог|Общее число вызовов токенов.|ApiName,OperationName,Регион|
|CharactersTranslated|Преобразованные символы|Count|Итог|Общее количество символов во входящем текстовом запросе.|ApiName,OperationName,Регион|
|ХарактеристикиОбученные|Персонажи обучены|Count|Итог|Общее количество подготовленных персонажей.|ApiName,OperationName,Регион|
|SpeechSessionDuration|Длительность речи|Секунды|Итог|Общая длительность речи в секундах.|ApiName,OperationName,Регион|
|TotalTransactions|Всего транзакций|Count|Итог|Общее число транзакций.|None|
|ProcessedImages|Обработанные изображения|Count|Итог| Количество транзакций для обработки изображений.|ApiName,FeatureName,Канал, Регион|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть в билле (Унипраженные)|Байты|Итог|Количество оплачиваемых байт, полученных на всех сетевых интерфейсах виртуальной машиной (s) (Incoming Traffic) (Deprecated)|None|
|Сеть (исходящий трафик)|Сеть out Billable (Унипрачено)|Байты|Итог|Количество оплачиваемых байтов на всех сетевых интерфейсах виртуальной машины (s) (Исходящий трафик) (Deprecated)|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байты, считываемые с диска в период мониторинга|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Байты, написанные на диск в период мониторинга|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Диск данных Читайте Байты/Сек [(Обезвращенный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Диск данных Написать Байты / Sec [(Обезвреженный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операции чтения диска данных/Sec [(Обезвращено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операции записи диска данных/Sec [(Обезвреженные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|ИД слота|
|Длина очереди в расчете на диск|[Диск данных QD [(Обезвращенный)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|OS Disk Read Bytes/Sec [(Обезображено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|OS Disk Написать Байты / Sec [(Обезображенные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операции чтения диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операции записи диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|None|
|Длина очереди на диск ОС|ОС Диск ЗД [(Депрекционированный)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|None|
|Диск данных Читайте Байты/сек|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|LUN|
|Диск данных Написать Байты / сек|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|LUN|
|Операции чтения диска данных/Sec|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|LUN|
|Операции записи диска данных/Sec|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|LUN|
|Длина очереди диска данных|Глубина очереди диска данных (Предварительный просмотр)|Count|Среднее|Длина очереди дисков данных|LUN|
|OS Disk Read Bytes/sec|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|None|
|OS Disk Написать Байты / сек|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|None|
|Операции чтения диска Os/Sec|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|None|
|Операции записи диска OS/Sec|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|None|
|Длина очереди диска ОС|Глубина очереди диска ОС (Предварительный просмотр)|Count|Среднее|Длина очереди дисков ОС|None|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это количество текущих потоков в входящий направлении (трафик, идущий в ВМ)|None|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это количество текущих потоков в исходящих направлениях (трафик, выходящий из ВМ)|None|
|Входящие потоки Максимальная скорость создания|Входящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, идущий в ВМ)|None|
|Исходящие потоки Максимальная скорость создания|Исходящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий из ВМ)|None|
|Премиум кэш диска данных Читать Хит|Премиум кэш диска данных Читать хит (Предварительный просмотр)|Процент|Среднее|Премиум кэш диска данных Читать Хит|LUN|
|Премиум данных диска Кэш Читать Мисс|Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)|Процент|Среднее|Премиум данных диска Кэш Читать Мисс|LUN|
|Премиум OS диск кэш Читать Хит|Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Хит|None|
|Премиум OS диск кэш Читать Мисс|Премиум OS диск кэш Читать мисс (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Мисс|None|
|Сеть в общей сложности|Сеть в общей сложности|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Сеть Out Всего|Сеть Out Всего|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Сеть (входящий трафик)|Сеть в билле (Унипраженные)|Байты|Итог|Количество оплачиваемых байт, полученных на всех сетевых интерфейсах виртуальной машиной (s) (Incoming Traffic) (Deprecated)|VMName|
|Сеть (исходящий трафик)|Сеть out Billable (Унипрачено)|Байты|Итог|Количество оплачиваемых байтов на всех сетевых интерфейсах виртуальной машины (s) (Исходящий трафик) (Deprecated)|VMName|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байты, считываемые с диска в период мониторинга|VMName|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Байты, написанные на диск в период мониторинга|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Диск данных Читайте Байты/Сек [(Обезвращенный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Диск данных Написать Байты / Sec [(Обезвреженный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операции чтения диска данных/Sec [(Обезвращено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операции записи диска данных/Sec [(Обезвреженные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных QD [(Обезвращенный)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|OS Disk Read Bytes/Sec [(Обезображено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|OS Disk Написать Байты / Sec [(Обезображенные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операции чтения диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операции записи диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|None|
|Длина очереди на диск ОС|ОС Диск ЗД [(Депрекционированный)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|None|
|Диск данных Читайте Байты/сек|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|LUN, VMName|
|Диск данных Написать Байты / сек|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|LUN, VMName|
|Операции чтения диска данных/Sec|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|LUN, VMName|
|Операции записи диска данных/Sec|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|LUN, VMName|
|Длина очереди диска данных|Глубина очереди диска данных (Предварительный просмотр)|Count|Среднее|Длина очереди дисков данных|LUN, VMName|
|OS Disk Read Bytes/sec|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|VMName|
|OS Disk Написать Байты / сек|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|VMName|
|Операции чтения диска Os/Sec|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|VMName|
|Операции записи диска OS/Sec|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|VMName|
|Длина очереди диска ОС|Глубина очереди диска ОС (Предварительный просмотр)|Count|Среднее|Длина очереди дисков ОС|VMName|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это количество текущих потоков в входящий направлении (трафик, идущий в ВМ)|VMName|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это количество текущих потоков в исходящих направлениях (трафик, выходящий из ВМ)|VMName|
|Входящие потоки Максимальная скорость создания|Входящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, идущий в ВМ)|VMName|
|Исходящие потоки Максимальная скорость создания|Исходящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий из ВМ)|VMName|
|Премиум кэш диска данных Читать Хит|Премиум кэш диска данных Читать хит (Предварительный просмотр)|Процент|Среднее|Премиум кэш диска данных Читать Хит|LUN, VMName|
|Премиум данных диска Кэш Читать Мисс|Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)|Процент|Среднее|Премиум данных диска Кэш Читать Мисс|LUN, VMName|
|Премиум OS диск кэш Читать Хит|Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Хит|VMName|
|Премиум OS диск кэш Читать Мисс|Премиум OS диск кэш Читать мисс (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Мисс|VMName|
|Сеть в общей сложности|Сеть в общей сложности|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Сеть Out Всего|Сеть Out Всего|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть в билле (Унипраженные)|Байты|Итог|Количество оплачиваемых байт, полученных на всех сетевых интерфейсах виртуальной машиной (s) (Incoming Traffic) (Deprecated)|None|
|Сеть (исходящий трафик)|Сеть out Billable (Унипрачено)|Байты|Итог|Количество оплачиваемых байтов на всех сетевых интерфейсах виртуальной машины (s) (Исходящий трафик) (Deprecated)|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байты, считываемые с диска в период мониторинга|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Байты, написанные на диск в период мониторинга|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Диск данных Читайте Байты/Сек [(Обезвращенный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Диск данных Написать Байты / Sec [(Обезвреженный)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операции чтения диска данных/Sec [(Обезвращено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операции записи диска данных/Sec [(Обезвреженные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных QD [(Обезвращенный)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|OS Disk Read Bytes/Sec [(Обезображено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|OS Disk Написать Байты / Sec [(Обезображенные)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операции чтения диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операции записи диска OS/Sec [(Унипрачено)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|None|
|Длина очереди на диск ОС|ОС Диск ЗД [(Депрекционированный)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|None|
|Диск данных Читайте Байты/сек|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга|LUN|
|Диск данных Написать Байты / сек|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга|LUN|
|Операции чтения диска данных/Sec|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга|LUN|
|Операции записи диска данных/Sec|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга|LUN|
|Длина очереди диска данных|Глубина очереди диска данных (Предварительный просмотр)|Count|Среднее|Длина очереди дисков данных|LUN|
|OS Disk Read Bytes/sec|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Sec считывается с одного диска в период мониторинга для диска ОС|None|
|OS Disk Написать Байты / сек|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байты/Сек, написанные на одном диске в период мониторинга диска ОС|None|
|Операции чтения диска Os/Sec|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение IOPS с одного диска в период мониторинга для диска ОС|None|
|Операции записи диска OS/Sec|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись IOPS с одного диска в период мониторинга для диска ОС|None|
|Длина очереди диска ОС|Глубина очереди диска ОС (Предварительный просмотр)|Count|Среднее|Длина очереди дисков ОС|None|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это количество текущих потоков в входящий направлении (трафик, идущий в ВМ)|None|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это количество текущих потоков в исходящих направлениях (трафик, выходящий из ВМ)|None|
|Входящие потоки Максимальная скорость создания|Входящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, идущий в ВМ)|None|
|Исходящие потоки Максимальная скорость создания|Исходящие потоки Максимальная скорость создания|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий из ВМ)|None|
|Премиум кэш диска данных Читать Хит|Премиум кэш диска данных Читать хит (Предварительный просмотр)|Процент|Среднее|Премиум кэш диска данных Читать Хит|LUN|
|Премиум данных диска Кэш Читать Мисс|Премиум данных диска Кэш Читать Мисс (Предварительный просмотр)|Процент|Среднее|Премиум данных диска Кэш Читать Мисс|LUN|
|Премиум OS диск кэш Читать Хит|Премиум OS Диск Кэш Читать Хит (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Хит|None|
|Премиум OS диск кэш Читать Мисс|Премиум OS диск кэш Читать мисс (Предварительный просмотр)|Процент|Среднее|Премиум OS диск кэш Читать Мисс|None|
|Сеть в общей сложности|Сеть в общей сложности|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Сеть Out Всего|Сеть Out Всего|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Count|Среднее|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Среднее|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байты|Среднее|Количество байт, полученных по сети в секунду.|None|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байты|Среднее|Количество байт, переданных по сети в секунду.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalPullCount|Общий подсчет потяните|Count|Среднее|Количество вытягиваний изображений в общей сложности|None|
|УспешныйPullCount|Успешный потяните графа|Count|Среднее|Количество успешных вытягиваний изображений|None|
|TotalPushCount|Всего push граф|Count|Среднее|Количество нажатий изображения в общей сложности|None|
|УспешныйPushCount|Успешный push Граф|Count|Среднее|Количество успешных нажатий изображения|None|
|RunDuration|Длительность выполнения|Миллисекунды|Итог|Длительность выполнения в миллисекундах|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Count|Среднее|Общее количество доступных ядер ЦП в управляемом кластере|None|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байты|Среднее|Общий объем доступной памяти в управляемом кластере|None|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Count|Среднее|Число модулей pod в состоянии готовности|namespace, стручок|
|kube_node_status_condition|Состояния для различных условий узла|Count|Среднее|Состояния для различных условий узла|состояние,статус,статус2,узла|
|kube_pod_status_phase|Число модулей pod по фазам|Count|Среднее|Число модулей pod по фазам|фаза, пространство имен, стручок|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|УспешныеЗапросы|Выполненные запросы|Count|Итог|Успешные запросы, сделанные пользовательским поставщиком|HttpMethod,Callpath,StatusCode|
|FailedRequests|Невыполненные запросы|Count|Итог|Получает доступные журналы для поставщиков пользовательских ресурсов|HttpMethod,Callpath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/DataBoxEdgeDevices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|NICReadThroughput|Проликвная пропускная готовность (сеть)|Байт/с|Среднее|Считываемая пропускная стоимость сетевого интерфейса на устройстве за отчетный период для всех томов в шлюзе.|InstanceName|
|NICWriteThroughput|Запись пропускной записи (сеть)|Байт/с|Среднее|Пропускная часть сетевого интерфейса на устройстве за отчетный период для всех томов в шлюзе.|InstanceName|
|CloudReadThroughputPerShare|Прокрутка облачной загрузки (доля)|Байт/с|Среднее|Пропускная часть загрузки в Azure из доли за отчетный период.|Share (Предоставить общий доступ)|
|CloudUploadThroughputPerShare|Пропускная часть облачной загрузки (доля)|Байт/с|Среднее|Пропускная часть загрузки в Azure от доли за отчетный период.|Share (Предоставить общий доступ)|
|БайтыЗагруженыДляИКЛуперПоделитьь|Облачные байты загружены (доля)|Байты|Среднее|Общее количество байтов, загруженных в Azure из доли за отчетный период.|Share (Предоставить общий доступ)|
|Общая вместимость|Общая емкость|Байты|Среднее|Общая емкость|None|
|Доступнаяемкость|Доступная емкость|Байты|Среднее|Имеющаяся емкость в байтах в течение отчетного периода.|None|
|ОблакоUploadThroughput|Пропускная часть облачной загрузки|Байт/с|Среднее|В течение отчетного периода облачное облако загружает пропускную пропускную связь в Azure.|None|
|CloudReadThroughput|Пропускная часть облачной загрузки|Байт/с|Среднее|В течение отчетного периода пропускная связь облака загружает в Azure.|None|
|БайтсЗагруженоОблако Облака|Облачные байты загружены (устройство)|Байты|Среднее|Общее количество байтов, загруженных в Azure с устройства в отчетный период.|None|
|HyperVVirtualProcessorUtilization|Край вычислить - Процентный процессор|Процент|Среднее|Процентное использование процессора|InstanceName|
|ГиперВПамятоутилизация|Край вычисления - Использование памяти|Процент|Среднее|Количество оперативной памяти в использовании|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/Datacatalogs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Распределение активовПоклассификация|Распределение активов по классификации|Count|Итог|Указывает количество активов с определенной классификацией, т.е. они классифицируются с этой меткой.|Классификация,Источник|
|Распределение активовПохранениетипом|Распределение активов по типу хранилища|Count|Итог|Указывает количество активов с определенным типом хранилища.|StorageType|
|NumberOfAssetsWithClassifications|Количество активов, по крайней мере одной классификации|Count|Среднее|Указывает количество активов, по крайней мере одной классификации тегов.|None|
|Сканотменено|Сканирование отменено|Count|Итог|Указывает количество отмененных сканирований.|None|
|СканированиеЗавершено|Сканирование завершено|Count|Итог|Указывает количество успешно завершенных сканирований.|None|
|Сканнесусис|Сканирование не удалось|Count|Итог|Указывает на количество сканов, которые не были сканировались.|None|
|ScanTimeTaken|Время сканирования, занесено|Секунды|Итог|Указывает общее время сканирования за считанные секунды.|None|
|КаталогАктивныепользователи|Ежедневные активные пользователи|Count|Итог|Количество активных пользователей ежедневно|None|
|КаталогИспользование|Распределение использования по операции|Count|Итог|Укажите количество операций, которые пользователь делает в каталоге, т.е. доступ, поиск, глоссарий.|Операция|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Count|Итог||конвейерИмя,активностьИмя|
|SuccessfulRuns.|успешные циклы выполнения.|Count|Итог||конвейерИмя,активностьИмя|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Count|Итог||ОтказТип,Имя|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Count|Итог||ОтказТип,Имя|
|ТрубопроводОтмененныеЗаистые|Отмененный конвейер выполняет метрики|Count|Итог||ОтказТип,Имя|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Count|Итог||ActivityType,PipelineName,FailureType,Name|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Count|Итог||ActivityType,PipelineName,FailureType,Name|
|АктивностьОтмененныеБега|Отмененные действия выполняет метрики|Count|Итог||ActivityType,PipelineName,FailureType,Name|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Count|Итог||Имя,ОтказТип|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Count|Итог||Имя,ОтказТип|
|TriggerCancelledRuns|Отмененные триггерные запуски метрик|Count|Итог||Имя,ОтказТип|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Процент|Среднее||ИнтеграцияRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Среднее||ИнтеграцияRuntimeName,NodeName|
|ИнтеграцияRuntimeAverageTaskUpDelay|Длительность очереди времени выполнения интеграции|Секунды|Среднее||ИнтеграцияRuntimeName|
|ИнтеграцияВывремяКиДлина|Интеграция длины очереди выполнения|Count|Среднее||ИнтеграцияRuntimeName|
|ИнтеграцияRuntimeAvailableNodeNumber|Интеграция времени выполнения доступных узлов кол|Count|Среднее||ИнтеграцияRuntimeName|
|MaxAllowedResourceCount|Максимально допустимые субъекты считаются|Count|Максимальная||None|
|MaxAllowedFactorySizeingbunitsunits|Максимально допустимый размер фабрики (единица ГБ)|Count|Максимальная||None|
|Ресурсный счет|Общее количество сущностей|Count|Максимальная||None|
|FactorySizeingbunitsunits|Общий размер фабрики (единица ГБ)|Count|Максимальная||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Итог|Количество успешно выполненных заданий.|None|
|JobEndedFailure|Failed Jobs|Count|Итог|Количество невыполненных заданий.|None|
|JobEndedCancelled|Cancelled Jobs|Count|Итог|Количество отмененных заданий.|None|
|JobAUEndedSuccess|Successful AU Time|Секунды|Итог|Общее время AU успешно выполненных заданий.|None|
|JobAUEndedFailure|Failed AU Time|Секунды|Итог|Общее время AU невыполненных заданий.|None|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Итог|Общее время AU отмененных заданий.|None|
|JobStage|Вакансии на сцене|Count|Итог|Количество заданий на каждом этапе.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|None|
|DataWritten|Записанные данные|Байты|Итог|Общий объем данных, записанных в учетной записи.|None|
|DataRead|Данных прочитано|Байты|Итог|Общий объем данных, прочитанных в учетной записи.|None|
|WriteRequests|Запросы на запись|Count|Итог|Количество запросов на запись данных в учетную запись.|None|
|ReadRequests|Запросы на чтение|Count|Итог|Количество запросов на чтение данных для учетной записи.|None|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/учетные записи

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ShareCount|Отправленные акции|Count|Максимальная|Количество отправленных акций на счет|ShareName|
|ShareSubscriptionCount|Полученные акции|Count|Максимальная|Количество полученных акций на счете|ShareSubscriptionName|
|УспешныеПересхронизации|Отправлено Совместное снимки успешно|Count|Count|Количество отправленных снимков в аккаунте|None|
|Неудачнаяперехационизация|Отправленные неудавные снимки|Count|Count|Количество неудавшихся моментальных снимков отсылки к отправке в учетной записи|None|
|SucceededShareSubscriptionСинхронизонизации|Полученные снимки успешно гостеприра для обмена|Count|Count|Количество полученных снимков в аккаунте|None|
|FailedShareПодпискаСинхронизации|Полученная доля Неудачные снимки|Count|Count|Количество полученных невыполненных моментов в учетной записи|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|None|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|None|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|None|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Среднее|Максимальный объем хранилища для журнала сервера|None|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|None|
|seconds_behind_master|Задержка репликации в секундах|Count|Максимальная|Задержка репликации в секундах|None|
|backup_storage_used|Используется резервное хранилище|Байты|Среднее|Используется резервное хранилище|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|None|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|None|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|None|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальная|Максимальный объем хранилища для журнала сервера|None|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|None|
|seconds_behind_master|Задержка репликации в секундах|Count|Максимальная|Задержка репликации в секундах|None|
|backup_storage_used|Используется резервное хранилище|Байты|Среднее|Используется резервное хранилище|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|None|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|None|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|None|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальная|Максимальный объем хранилища для журнала сервера|None|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|None|
|backup_storage_used|Используется резервное хранилище|Байты|Среднее|Используется резервное хранилище|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|None|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимальная|Реплика отставание в секундах|None|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|Максимальная|Отставание в байтах самой отстающей реплики|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreS'L/serversv2

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|Iops|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Count|Среднее|Операции io в секунду|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|None|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreS'L/singleservers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|Iops|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Count|Среднее|Операции io в секунду|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|None|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|None|
|connections_succeeded|Успешные соединения|Count|Итог|Успешные соединения|None|
|maximum_used_transactionIDs|Максимально используемые иди транзакций|Count|Среднее|Максимально используемые иди транзакций|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Итог|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|None|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Итог|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|None|
|c2d.commands.egress.complete.success|Поставки сообщений C2D завершены|Count|Итог|Количество поставок сообщений об лакт-устройствуспешно, успешно завершенных устройством|None|
|c2d.commands.egress.abandon.success|C2D сообщения отказались|Count|Итог|Количество сообщений об облаке и устройства, оставленных устройством|None|
|c2d.commands.egress.reject.success|Сообщения C2D отклонены|Count|Итог|Количество сообщений об лакони-устройства, отклоненных устройством|None|
|C2DСообщенияИ|C2D Сообщения истек (предварительный просмотр)|Count|Итог|Количество просроченных сообщений об об лакеи к устройству|None|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Count|Итог|Число устройств, зарегистрированных в Центре Интернета вещей.|None|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Count|Итог|Число устройств, подключенных к Центру Интернета вещей.|None|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Count|Итог|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|None|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Count|Итог|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|None|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Count|Итог|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |None|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Count|Итог|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|None|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|None|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|None|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|None|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|None|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|None|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|None|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|None|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events).|None|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events).|None|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|None|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|None|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байты|Итог|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|None|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|None|
|EventGridDeliveries|Доставка сетки событий (предварительный просмотр)|Count|Итог|Количество событий IoT Hub, опубликованных в Event Grid. Используйте измерение результата для количества успешных и невыполненных запросов. Измерение EventType показываетhttps://aka.ms/ioteventgrid)тип события ( .|ResourceId,Результат,EventType|
|EventGridЛатенция|Задержка сетки событий (предварительный просмотр)|Миллисекунды|Среднее|Средняя задержка (миллисекунды) с момента сгенерированного события Iot Hub до момента публикации события в Event Grid. Это число является средним между всеми типами событий. Используйте измерение EventType, чтобы увидеть задержку определенного типа событий.|ResourceId,EventType|
|Доставкапо|Доставка поставок (предварительный просмотр)|Миллисекунды|Итог|Количество раз, когда Концентратор Пытался доставить сообщения во все конечные точки с помощью реаутинга. Чтобы увидеть количество успешных или неудачных попыток, используйте измерение результата. Чтобы увидеть причину сбоя, например, недействительный, отброшенный или осиротевший, используйте измерение FailureReasonCategory. Вы также можете использовать измерения EndpointName и EndpointType, чтобы понять, сколько сообщений было доставлено в различные конечные точки. Значение метрики увеличивается на один для каждой попытки доставки, в том числе, если сообщение доставлено в несколько конечных точек или если сообщение доставляется в одну и ту же конечную точку несколько раз.|ResourceId,EndpointType,EndpointName,FailureReasonКатегория,Результат,RoutingSource|
|RoutingDeliveryLatency|Задержка доставки в райти (предварительный просмотр)|Миллисекунды|Среднее|Средняя задержка (миллисекунды) между проникновением сообщений в Концентратор IoT и телеметрическим сообщением в конечную точку. Для понимания задержки в различных конечных точках можно использовать измерения EndpointName и EndpointType.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Итог|Число всех успешных операций чтения с двойников, инициированных устройством.|None|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Итог|Число всех неудачных операций чтения с двойников, инициированных устройством.|None|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|None|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Итог|Число всех успешных обновлений двойников, инициированных устройством.|None|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Итог|Число всех неудачных обновлений двойников, инициированных устройством.|None|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|None|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Итог|Число всех успешных вызовов прямых методов.|None|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Итог|Число всех неудачных вызовов прямых методов.|None|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|None|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|None|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Итог|Число всех успешных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Итог|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Итог|Число всех успешных обновлений двойников, инициированных из серверной части.|None|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Итог|Число всех неудачных обновлений двойников, инициированных из серверной части.|None|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|None|
|twinQueries.success|Успешные запросы двойников.|Count|Итог|Число всех успешных запросов двойников.|None|
|twinQueries.failure|Неудачные запросы двойников.|Count|Итог|Количество всех неудачных запросов двойников.|None|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Среднее|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|None|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Итог|Количество всех успешных созданий заданий обновления двойников.|None|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Итог|Количество всех неудачных операций создания заданий обновления двойников.|None|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Итог|Количество всех успешных операций создания заданий вызова прямых методов.|None|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Итог|Количество всех неудачных операций создания заданий вызова прямых методов.|None|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Итог|Количество всех успешных вызовов для получения списка заданий.|None|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Итог|Количество всех неудачных вызовов для получения списка заданий.|None|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Итог|Количество всех успешных вызовов для отмены заданий.|None|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Итог|Количество всех неудачных вызовов для отмены заданий.|None|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Итог|Количество всех успешных вызовов для запроса заданий.|None|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Итог|Количество всех неудачных вызовов для запроса заданий.|None|
|jobs.completed|Завершенные задания|Count|Итог|Количество всех выполненных заданий.|None|
|jobs.failed|Неудачные задания.|Count|Итог|Количество всех неудачных заданий.|None|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Итог|Количество ошибок регулирования из-за регулирования пропускной способности устройства|None|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Среднее|Количество сообщений, использованных сегодня|None|
|deviceDataUsage|Общее использование данных устройства|Байты|Итог|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|None|
|deviceDataUsageV2|Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))|Байты|Итог|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|None|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Count|Среднее|Число устройств, зарегистрированных в Центре Интернета вещей.|None|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Count|Среднее|Число устройств, подключенных к Центру Интернета вещей.|None|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Count|Итог|Метрики для операций конфигурации|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Count|Итог|Количество попыток регистрации устройств|ProvisioningServiceName,IotHubName,Статус|
|DeviceAssignments|Назначенные устройства|Count|Итог|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Попытки аттестации|Count|Итог|Количество попыток аттестации устройств|ProvisioningServiceName,Статус,Протокол|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ДобавитьРегион|Добавлено в регион|Count|Count|Добавлено в регион|Регион|
|AvailableStorage|Доступная служба хранилища|Байты|Итог|Общее доступное хранилище, о доступном на 5 минуте детализации|CollectionName,DatabaseName,Регион|
|CassandraConnectionClosures|Отключение связи Cassandra|Count|Итог|Количество подключений Cassandra, которые были закрыты, сообщили в 1 минуту детализации|APIType,Регион,ЗакрытиеПричина|
|CassandraKeyspaceУдалить|Кассандра Keyspace Удалено|Count|Count|Кассандра Keyspace Удалено|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughPutUpdate|Кассандра Keyspace Пропускная информация Обновлено|Count|Count|Кассандра Keyspace Пропускная информация Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Кассандра Keyspace Обновлено|Count|Count|Кассандра Keyspace Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Расходы запросов по Cassandra|Count|Итог|RUs, потребляемые для запросов Cassandra|APIType,DatabaseName,CollectionName,Регион,OperationType,ResourceType|
|CassandraRequests|Запросы по Cassandra|Count|Count|Количество запросов Кассандры|APIType,DatabaseName,CollectionName,Регион,OperationType,ResourceType,ОшибкаКода|
|CassandraTableDelete|Кассандра Таблица Удалена|Count|Count|Кассандра Таблица Удалена|Название ресурсов,ChildResourcename,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughPutUpdate|Кассандра Таблица Пропускная информация Обновлено|Count|Count|Кассандра Таблица Пропускная информация Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Кассандра Таблица Обновлено|Count|Count|Кассандра Таблица Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Созданиесчета|Создана учетная запись|Count|Count|Создана учетная запись|None|
|DataUsage|Использование данных|Байты|Итог|Общее использование данных, зарегистрированное при детализации в 5 минут|CollectionName,DatabaseName,Регион|
|УдалитьУчетная запись|Учетная запись удалена|Count|Count|Учетная запись удалена|None|
|DocumentCount|Число документов|Count|Итог|Общее количество документов, зарегистрированное на 5 минутдетности|CollectionName,DatabaseName,Регион|
|DocumentQuota|Квота на документы|Байты|Итог|Общая квота хранения, зарегистрированная на 5 минутдетности|CollectionName,DatabaseName,Регион|
|GremlinDatabaseDelete|База данных Гремлин удалена|Count|Count|База данных Гремлин удалена|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughPutUpdate|Гремлин база данных Пропускная часть Обновлено|Count|Count|Гремлин база данных Пропускная часть Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Обновлена база данных Гремлина|Count|Count|Обновлена база данных Гремлина|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Гремлин Граф удален|Count|Count|Гремлин Граф удален|Название ресурсов,ChildResourcename,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughPutUpdate|Гремлин Граф Пропускная часть Обновлено|Count|Count|Гремлин Граф Пропускная часть Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Гремлин График Обновлено|Count|Count|Гремлин График Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndexUsage|Использование индексов|Байты|Итог|Общее использование индекса, зарегистрированное на 5 минутдетности|CollectionName,DatabaseName,Регион|
|MetadataRequests|Запросы метаданных|Count|Count|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|База данныхИмя,CollectionName,Регион,Статус-код,Роль|
|MongoCollectionУдалить|Коллекция Монго удалена|Count|Count|Коллекция Монго удалена|Название ресурсов,ChildResourcename,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughPutUpdate|Монго Коллекция Пропускная часть Обновлено|Count|Count|Монго Коллекция Пропускная часть Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Коллекция Монго Обновлено|Count|Count|Коллекция Монго Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate|Монго База данных Обновлено|Count|Count|Монго База данных Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseУдалить|База данных Mongo удалена|Count|Count|База данных Mongo удалена|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughPutUpdate|Монго база данных Пропускная часть Обновлено|Count|Count|Монго база данных Пропускная часть Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Запросить оплату Mongo|Count|Итог|Использованные единицы запросов Mongo|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод,Статус|
|MongoRequests|Запросы Mongo|Count|Count|Количество выполненных запросов Mongo|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод,Статус|
|MongoRequestsCount|Коэффициент запроса Mongo|Число/с|Среднее|Монго запрос граф а в секунду|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод|
|MongoRequestsУдалить|Коэффициент запроса на удаление Mongo|Число/с|Среднее|Запрос на удаление Mongo в секунду|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод|
|MongoRequestsInsert|Коэффициент запроса на вставку Mongo Insert|Число/с|Среднее|Количество вставки монго в секунду|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод|
|МонгоЗапросыКЕри|Коэффициент запроса запроса Mongo|Число/с|Среднее|Запрос запроса Mongo В секунду|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод|
|MongoRequestsUpdate|Коэффициент запроса на обновление Mongo|Число/с|Среднее|Запрос на обновление Mongo в секунду|База данныхИмя,CollectionName,Регион,CommandName,ОшибкаКод|
|НормализованноепотреблениеRU|Нормализованное потребление RU|Процент|Максимальная|Максимальный процент потребления RU в минуту|CollectionName,DatabaseName,Регион|
|ProvisionedThroughput|Подготовленная пропускная способность|Count|Максимальная|Подготовленная пропускная способность|База данныхИмя,CollectionName|
|РегионФайловер|Регион не удалось более|Count|Count|Регион не удалось более|None|
|УдалитьРегион|Регион удален|Count|Count|Регион удален|Регион|
|ReplicationLatency|Задержка репликации P99|MilliSeconds|Среднее|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|ИсточникРегион,TargetRegion|
|СерверСайдЛатенция|Задержка серверной стороны|MilliSeconds|Среднее|Задержка серверной стороны|База данныхName,Collectionname,Регион,ConnectionMode,OperationType,PublicAPIType|
|ServiceAvailability|Доступность службы|Процент|Среднее|Учетные запросы доступности на один час, день или месяц детализации|None|
|SqlContainerУдалить|Контейнер Sql удален|Count|Count|Контейнер Sql удален|Название ресурсов,ChildResourcename,ApiKind,ApiKindResourceType,OperationType|
|SqlContainerThroughPutUpdate|Sql Контейнер Пропускная информация Обновлено|Count|Count|Sql Контейнер Пропускная информация Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Кзл Контейнер Обновлено|Count|Count|Кзл Контейнер Обновлено|ResourceName,ChildResourcename,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseУдалить|База данных Sql удалена|Count|Count|База данных Sql удалена|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughPutUpdate|Sql База данных Пропускная информация Обновлено|Count|Count|Sql База данных Пропускная информация Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Sql База данных Обновлено|Count|Count|Sql База данных Обновлено|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|ТаблицаУкУдаление|Таблица AzureTable удалена|Count|Count|Таблица AzureTable удалена|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|ТаблицаТаблицАКввпути|Обновлена пропускная связь таблицы AzureTable|Count|Count|Обновлена пропускная связь таблицы AzureTable|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|ТаблицаОбновление|Обновлена таблица AzureTable|Count|Count|Обновлена таблица AzureTable|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotalRequestUnits|Общее количество единиц запросов|Count|Итог|Использованные единицы запросов|База данныхИмя,CollectionName,Регион,Статус-код,OperationType,Статус|
|TotalRequests|Общее количество запросов|Count|Count|Количество выполненных запросов|База данныхИмя,CollectionName,Регион,Статус-код,OperationType,Статус|
|ОбновлениеAccountKeys|Обновлено ключи учетной записи|Count|Count|Обновлено ключи учетной записи|KeyType|
|ОбновлениеУчетсетиНастройки|Настройки сети учетных записей Обновлены|Count|Count|Настройки сети учетных записей Обновлены|None|
|ОбновлениеСчетаРепликтитиВАтыНастройки|Параметры репликации учетной записи обновлены|Count|Count|Параметры репликации учетной записи обновлены|None|
|ОбновлениеДиагностикаНастройки|Обновлены настройки диагностики учетной записи|Count|Count|Обновлены настройки диагностики учетной записи|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/услуги

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Транзакционный счет|Граф транзакций|Count|Count|Общий объем транзакций|Транзакционный счет|
|SuccessCount|Число успехов|Count|Count|Подсчет успешных транзакций|SuccessCount|
|ОтказCount|Число сбоев|Count|Count|Невыполненные транзакции|ОтказCount|
|Успешноела|Задержка успеха|MilliSeconds|Среднее|Задержка успешных транзакций|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/домены

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|Раздел|
|PublishFailCount|Публикация неудачных событий|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|Тема,ОшибкаType,Ошибка|
|ПубликацияУспехАвайте|Публикация задержки успеха|Миллисекунды|Итог|Публикация задержки успеха в миллисекундах|None|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|Тема,EventSubscriptionName,DomainEventПодпискАИмя|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Тема,EventSubscriptionName,DomainEventПодпискА,Ошибка,ОшибкаType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|Тема,EventSubscriptionName,DomainEventПодпискАИмя|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Тема,EventSubscriptionName,DomainEventПодпискАИмя|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|Тема,EventSubscriptionName,DomainEventПодпискаName,DropReason|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|Тема,EventSubscriptionName,DomainEventПодпискаName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|None|
|PublishFailCount|Публикация неудачных событий|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ОшибкаType,Ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|None|
|ПубликацияУспехАвайте|Публикация задержки успеха|Миллисекунды|Итог|Публикация задержки успеха в миллисекундах|None|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|Name EventSubscription|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка,ОшибкаType,EventПодпискаИмя|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|Name EventSubscription|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Name EventSubscription|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|DropReason,EventПодпискаИмя|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/SystemTopics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|None|
|PublishFailCount|Публикация неудачных событий|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ОшибкаType,Ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|None|
|ПубликацияУспехАвайте|Публикация задержки успеха|Миллисекунды|Итог|Публикация задержки успеха в миллисекундах|None|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|Name EventSubscription|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка,ОшибкаType,EventПодпискаИмя|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|Name EventSubscription|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Name EventSubscription|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|DropReason,EventПодпискаИмя|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|None|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка,ОшибкаType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|None|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|None|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|DropReason|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|None|
|PublishFailCount|Публикация неудачных событий|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ОшибкаType,Ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|None|
|ПубликацияУспехАвайте|Публикация задержки успеха|Миллисекунды|Итог|Публикация задержки успеха в миллисекундах|None|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Выполненные запросы для Microsoft.EventHub.|EntityName,OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.EventHub.|EntityName,OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.EventHub.|EntityName,OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Итог|Ошибки превышения квоты для Microsoft.EventHub.|EntityName,OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.EventHub.|EntityName,OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байты|Итог|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байты|Итог|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Среднее|Число активных подключений для Microsoft.EventHub.|None|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Count|Итог|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Count|Итог|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байты|Итог|Записанные байты для Microsoft.EventHub.|EntityName|
|Размер|Размер|Байты|Среднее|Размер EventHub в байтах.|EntityName|
|INREQS|Входящие запросы (обезвращенные)|Count|Итог|Всего входящих запросов на пространство имен (Deprecated)|None|
|SUCCREQ|Успешные запросы (обезвращенные)|Count|Итог|Всего успешных запросов на пространство имен (Deprecated)|None|
|FAILREQ|Неудачные запросы (обезумевший)|Count|Итог|Всего невыполненных запросов на пространство имен (Обезумевший)|None|
|SVRBSY|Ошибки сервера заняты (Deprecated)|Count|Итог|Всего ошибок сервера для пространства имен (Deprecated)|None|
|INTERR|Внутренние ошибки сервера (обезвращенные)|Count|Итог|Всего внутренних ошибок сервера для пространства имен (Deprecated)|None|
|MISCERR|Другие ошибки (обезвращенные)|Count|Итог|Всего невыполненных запросов на пространство имен (Обезумевший)|None|
|INMSGS|Входящие Сообщения (устаревшие) (Устаревшие)|Count|Итог|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Пожалуйста, используйте входящие сообщения метрики вместо (Обезвращенные)|None|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Count|Итог|Всего входящих сообщений для пространства имен (Обезвращено)|None|
|OUTMSGS|Исходящие Сообщения (устаревшие) (Устаревшие)|Count|Итог|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Пожалуйста, используйте исходящие сообщения метрики вместо (Обезвращенный)|None|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Count|Итог|Всего исходящих сообщений для пространства имен (Deprecated)|None|
|EHINMBS|Входящие байты (устаревшие) (Обезображенные)|Байты|Итог|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Пожалуйста, используйте входящие байты метрики вместо (Обезвращенный)|None|
|EHINBYTES|Входящие байты (не рекомендуется)|Байты|Итог|Входящие входящие сообщения событийной пропускной перевалки для пространства имен (Deprecated)|None|
|EHOUTMBS|Исходящие байты (устаревшие) (Обезображенные)|Байты|Итог|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Пожалуйста, используйте исходящие байты метрики вместо (Обезвращенный)|None|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байты|Итог|Пропускная пропускная часть исходящих сообщений Event Hub для пространства имен (Deprecated)|None|
|EHABL|Архивные сообщения невыполненной работы (Депрекатированные)|Count|Итог|Архивные сообщения Event Hub в невыполненной работе для пространства имен (Deprecated)|None|
|EHAMSGS|Архивные сообщения (обезвращенные)|Count|Итог|Концентратор событий архивирует сообщения в пространстве имен (Обезвращено)|None|
|EHAMBS|Пропускная пропускная часть архивного сообщения (Обезвращенная)|Байты|Итог|Пропускная пропускная часть сообщения Event Hub в пространстве имен (Deprecated)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Выполненные запросы для Microsoft.EventHub.|OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.EventHub.|OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Итог|Ошибки превышения квоты для Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.EventHub.|OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.EventHub.|None|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.EventHub.|None|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.EventHub.|None|
|IncomingBytes|Входящие байты.|Байты|Итог|Входящие байты для Microsoft.EventHub.|None|
|OutgoingBytes|Исходящие байты.|Байты|Итог|Исходящие байты для Microsoft.EventHub.|None|
|ActiveConnections|ActiveConnections|Count|Среднее|Число активных подключений для Microsoft.EventHub.|None|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.EventHub.|None|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.EventHub.|None|
|CaptureBacklog|Невыполненная работа записи.|Count|Итог|Невыполненная работа записи для Microsoft.EventHub.|None|
|CapturedMessages|Записанные сообщения.|Count|Итог|Записанные сообщения для Microsoft.EventHub.|None|
|CapturedBytes|Записанные байты.|Байты|Итог|Записанные байты для Microsoft.EventHub.|None|
|ЦП|ЦП|Процент|Максимальная|Использование ЦП для кластера концентратора событий в процентах|Роль|
|AvailableMemory|Доступная память|Процент|Максимальная|Доступна память для кластера концентратора событий в процентах от общей памяти.|Роль|
|Размер|Размер EventHub в байтах.|Байты|Среднее|Размер EventHub в байтах.|Роль|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Count|Итог|Число запросов к шлюзу|HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Count|Итог|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|HttpStatus|
|NumActiveWorkers|Количество активных работников|Count|Максимальная|Количество активных работников|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Count|Среднее|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Count|Среднее|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Count|Среднее|Емкость, передаваемая для автомасштабирования при выполнении.|None|
|ScaleActionsInitiated|Инициированные действия масштабирования|Count|Итог|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|доступностьРезультат/доступностьПроцент|Доступность|Процент|Среднее|Процент успешно завершенных тестов доступности|доступностьРезультат/имя, доступностьResult/местоположение|
|доступностьРезультаты/счет|Тесты доступности|Count|Count|Подсчет тестов доступности|доступностьРезультат/имя, доступностьРезультат/местоположение, доступностьResult/успех|
|availabilityResults/duration|Продолжительность теста доступности|MilliSeconds|Среднее|Продолжительность теста доступности|доступностьРезультат/имя, доступностьРезультат/местоположение, доступностьResult/успех|
|browserTimings/networkDuration|Время подключения к сети при загрузке страницы|MilliSeconds|Среднее|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|None|
|browserTimings/processingDuration|Время обработки клиента|MilliSeconds|Среднее|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|None|
|browserTimings/receiveDuration|Время получения ответа|MilliSeconds|Среднее|Время от первого до последнего байта или до отключения.|None|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Среднее|Время от установки сетевого подключения до получения первого байта.|None|
|browserTimings/totalDuration|Время загрузки страницы в браузере|MilliSeconds|Среднее|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|None|
|dependencies/count|Вызовы зависимостей|Count|Count|Число вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип,зависимость/производительностьBucket,зависимость/успех,зависимость/цель,зависимость/результатКод,операция/синтетический,облако/рольInstance,облако/роль|
|dependencies/duration|Длительность зависимости|MilliSeconds|Среднее|Длительность вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип,зависимость/производительностьBucket,зависимость/успех,зависимость/цель,зависимость/результатКод,операция/синтетический,облако/рольInstance,облако/роль|
|dependencies/failed|Сбои вызовов зависимости|Count|Count|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|зависимость/тип,зависимость/производительностьBucket,зависимость/успех,зависимость/цель,зависимость/результатКод,операция/синтетический,облако/рольInstance,облако/роль|
|pageViews/count|Просмотры страниц|Count|Count|Число просмотров страниц.|операция/синтетическая,облачность/рольИмя|
|pageViews/duration|Время загрузки страницы для просмотра|MilliSeconds|Среднее|Время загрузки страницы для просмотра|операция/синтетическая,облачность/рольИмя|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Среднее|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue (HTTP-запросы в очереди приложений)|Count|Среднее|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|Число/с|Среднее|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|Число/с|Среднее|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|Байт/с|Среднее|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Обработка ЦП|Процент|Среднее|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Процессорное время|Процент|Среднее|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Объем доступной памяти|Байты|Среднее|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Количество байтов исключительного использования процессов|Байты|Среднее|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|Время ответа от сервера|MilliSeconds|Среднее|Время с момента получения HTTP-запроса до завершения отправки ответа.|запрос/производительностьBucket,запрос/результатКод,операция/синтетический,облако/рольInstance,запрос/успех,облако/рольName|
|requests/count|Запросы сервера|Count|Count|Число выполненных запросов HTTP.|запрос/производительностьBucket,запрос/результатКод,операция/синтетический,облако/рольInstance,запрос/успех,облако/рольName|
|requests/failed|Failed requests (Неудачные запросы)|Count|Count|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|запрос/производительностьBucket,запрос/результатКод,запрос/успех,операция/синтетический, облако/рольInstance,облако/рольName|
|запросы/тариф|Скорость запроса сервера|Число/с|Среднее|Скорость запросов сервера в секунду|запрос/производительностьBucket,запрос/результатКод,операция/синтетический,облако/рольInstance,запрос/успех,облако/рольName|
|exceptions/count|Исключения|Count|Count|Общее число всех неперехваченных исключений.|облако/рольНамис,облако/рольInstance,клиент/тип|
|exceptions/browser|Исключения браузера|Count|Count|Число необработанных исключений в браузере.|клиент/изгойСервер,облако/рольИмя|
|exceptions/server|Исключения сервера|Count|Count|Число неперехваченных исключений, созданных в серверном приложении.|клиент/isServer,облако/рольName,облако/рольInstance|
|traces/count|Трассировки|Count|Count|Число документов трассировки|трассировка/тяжестьLevel,операция/синтетический, облако/рольName,облако/рольInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|connectedDeviceCount|Всего подключенных устройств|Count|Среднее|Количество устройств, подключенных к IoT Central|None|
|c2d.property.read.success|Успешные данные по свойство устройств читаются из IoT Central|Count|Итог|Подсчет всех успешных считываемок свойств, инициированных от IoT Central|None|
|c2d.property.read.failure|Неудавающееся свойство устройства читается из Центра IoT|Count|Итог|Подсчет всех неудавшихся считываемок, инициированных из IoT Central|None|
|d2c.property.read.success|Успешные чтения свойств устройств с устройств|Count|Итог|Подсчет всех успешных считываемых свойств, инициированных с устройств|None|
|d2c.property.read.failure|Неудавенное свойство устройства читается с устройств|Count|Итог|Подсчет всех неисправных считываемых свойств, инициированных с устройств|None|
|c2d.property.update.success|Успешные обновления свойств устройств от IoT Central|Count|Итог|Количество всех успешных обновлений свойств, инициированных от IoT Central|None|
|c2d.property.update.failure|Неудавные обновления свойств устройств от IoT Central|Count|Итог|Количество всех неудавшихся обновлений свойств, инициированных из IoT Central|None|
|d2c.property.update.success|Успешные обновления свойств устройств с устройств|Count|Итог|Количество всех успешных обновлений свойств, инициированных с устройств|None|
|d2c.property.update.failure.failure|Неудавные обновления свойств устройств с устройств|Count|Итог|Количество всех неудавшихся обновлений свойств, инициированных с устройств|None|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Count|Count|Общее число попаданий в API службы|ActivityType,ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунды|Среднее|Общая задержка запросов к API службы|ActivityType,ActivityName,StatusCode,StatusCode, StatusCodeClass|
|ServiceApiResult|Всего результатов для API службы|Count|Count|Общее число результатов для API службы|ActivityType,ActivityName,StatusCode,StatusCode, StatusCodeClass|
|НасыщенностьОбуви|Общее насыщение Убежища|Процент|Среднее|Используемая емкость хранилища|ActivityType,ActivityName,TransactionType|
|Доступность|Общая доступность Убежища|Процент|Среднее|Доступность запросов Убежища|ActivityType,ActivityName,StatusCode,StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|КахеУтилизация|Использование кэша|Процент|Среднее|Уровень загрузки в области кластера|None|
|QueryDuration|Query duration (Длительность запросов)|Миллисекунды|Среднее|Длительность запросов в считанные секунды|QueryStatus|
|Заглатываетустиюутилизацию|Использование приема|Процент|Среднее|Доля использованных слотов приема данных в кластере|None|
|Keepalive|Сохраняйте жизнь|Count|Среднее|Проверка работоспособности показывает, что кластер отвечает на запросы|None|
|ПроглатываниеVolumeInMB|Объем приема (в МБ)|Count|Итог|Общий объем данных, принятых в кластере (в МБ)|База данных|
|ПроглатительностьЛаустьInSeconds|Задержка приема (в секундах)|Секунды|Среднее|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|None|
|EventsProcessedForEventHubs|Обработка событий (для концентраторов событий/IoT)|Count|Итог|Количество событий, обрабатываемых кластером при прогутании из концентратора Event/IoT|EventStatus|
|IngestionResult|Результат приема|Count|Count|Число операций приема данных|IngestionResultDetails|
|ЦП|ЦП|Процент|Среднее|Уровень загрузки ЦП|None|
|Непрерывныйэкспортнизоборонэкспорт|Непрерывный экспорт - нюм экспортированных записей|Count|Итог|Количество экспортированных записей, выпущенных за каждый артефакт хранения, написанный во время операции по экспорту|ContinuousExportName,База данных|
|Экспортутилизация|Утилизация экспорта|Процент|Максимальная|Утилизация экспорта|None|
|НепрерывныйЭкспортВожиданиСчет|Непрерывный экспорт в ожидании графа|Count|Максимальная|Количество ожидающих непрерывных экспортных заданий, готовых к исполнению|None|
|НепрерывныйэкспортМаксЛатнесСМинутами|Непрерывный экспорт Макс Запотына|Count|Максимальная|Опоздание (в считанные минуты), о которых сообщается о непрерывных экспортных рабочих местах в кластере|None|
|НепрерывныйЭкспортРезультат|Непрерывный результат экспорта|Count|Count|Указывает, удалось или не удалось непрерывный экспорт или не удалось|НепрерывныйЭкспортНамиг,Результат,База данных|
|StreamingIngestDuration|Продолжительность потоковой передачи|Миллисекунды|Среднее|Продолжительность потоковой передачи в миллисекундах|None|
|StreamingIngdataRate|Скорость потоковой передачи данных|Count|Среднее|Скорость потоковой передачи данных (МБ в секунду)|None|
|SteamingIngestRequestRate|Скорость потоковой передачи запроса|Count|RateRequestsPerSecond|Скорость потоковой передачи запроса (запросы в секунду)|None|
|StreamingIngestResults|Результат потоковой передачи|Count|Среднее|Результат потоковой передачи|Результат|
|TotalNumberOfConcurrentЗапросы|Общее количество одновременных запросов|Count|Итог|Общее количество одновременных запросов|None|
|TotalNumberOfThrottledЗапросы|Общее количество задушенные запросы|Count|Итог|Общее количество задушенные запросы|None|
|TotalNumberOfThrottledCommands|Общее количество задушенные команды|Count|Итог|Общее количество задушенные команды|CommandType|
|TotalNumberOfExtents|Общее количество размеров|Count|Итог|Общее количество объемов данных|None|
|InstanceCount|Число экземпляров|Count|Среднее|Общее количество экземпляров|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итог|Число запущенных выполнений рабочего процесса.|None|
|RunsCompleted|Runs Completed|Count|Итог|Число завершенных выполнений рабочего процесса.|None|
|RunsSucceeded|Runs Succeeded|Count|Итог|Число успешно завершенных выполнений рабочего процесса.|None|
|RunsFailed|Runs Failed|Count|Итог|Число выполнений рабочего процесса, завершившихся сбоем.|None|
|RunsCancelled|Runs Cancelled|Count|Итог|Число отмененных выполнений рабочего процесса.|None|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|None|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|None|
|RunThrottledEvents|Run Throttled Events|Count|Итог|Число событий регулирования действия или триггера рабочего процесса.|None|
|RunStartThrottledСобытия|Выполнить события, затмеваемые|Count|Итог|Количество запуска рабочего процесса начинается с задушенные события.|None|
|RunFailurePercentage|Run Failure Percentage|Процент|Итог|Процент выполнений рабочего процесса, завершившихся сбоем.|None|
|ActionsStarted|Actions Started |Count|Итог|Число запущенных действий рабочего процесса.|None|
|ActionsCompleted|Actions Completed |Count|Итог|Число завершенных действий рабочего процесса.|None|
|ActionsSucceeded|Actions Succeeded |Count|Итог|Число успешно выполненных действий рабочего процесса.|None|
|ActionsFailed|Actions Failed |Count|Итог|Число действий рабочего процесса, завершившихся сбоем.|None|
|ActionsSkipped|Actions Skipped |Count|Итог|Число пропущенных действий рабочего процесса.|None|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|None|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|None|
|ActionThrottledEvents|Action Throttled Events|Count|Итог|Число событий регулирования действия рабочего процесса.|None|
|TriggersStarted|Triggers Started |Count|Итог|Число запущенных триггеров рабочего процесса.|None|
|TriggersCompleted|Triggers Completed |Count|Итог|Число завершенных триггеров рабочего процесса.|None|
|TriggersSucceeded|Triggers Succeeded |Count|Итог|Число успешно выполненных триггеров рабочего процесса.|None|
|TriggersFailed|Triggers Failed |Count|Итог|Число триггеров рабочего процесса, завершившихся со сбоем.|None|
|TriggersSkipped|Triggers Skipped|Count|Итог|Число пропущенных триггеров рабочего процесса.|None|
|TriggersFired|Triggers Fired |Count|Итог|Число активированных триггеров рабочего процесса.|None|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|None|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|None|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|None|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итог|Число событий регулирования триггера рабочего процесса.|None|
|BillableActionExecutions|Billable Action Executions|Count|Итог|Число выполненных действий рабочего процесса, за которые выставляется счет.|None|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Итог|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|None|
|TotalBillableExecutions|Total Billable Executions|Count|Итог|Число выполнений рабочего процесса, за которые выставляется счет.|None|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Итог|Число выполнений внутренних операций, за которые выставляется счет.|None|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Итог|Число операций стандартного соединителя, за которые выставляется счет.|None|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Итог|Число операций с использованием хранилища, за которые выставляется счет.|None|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итог|Число запущенных выполнений рабочего процесса.|None|
|RunsCompleted|Runs Completed|Count|Итог|Число завершенных выполнений рабочего процесса.|None|
|RunsSucceeded|Runs Succeeded|Count|Итог|Число успешно завершенных выполнений рабочего процесса.|None|
|RunsFailed|Runs Failed|Count|Итог|Число выполнений рабочего процесса, завершившихся сбоем.|None|
|RunsCancelled|Runs Cancelled|Count|Итог|Число отмененных выполнений рабочего процесса.|None|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|None|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|None|
|RunThrottledEvents|Run Throttled Events|Count|Итог|Число событий регулирования действия или триггера рабочего процесса.|None|
|RunStartThrottledСобытия|Выполнить события, затмеваемые|Count|Итог|Количество запуска рабочего процесса начинается с задушенные события.|None|
|RunFailurePercentage|Run Failure Percentage|Процент|Итог|Процент выполнений рабочего процесса, завершившихся сбоем.|None|
|ActionsStarted|Actions Started |Count|Итог|Число запущенных действий рабочего процесса.|None|
|ActionsCompleted|Actions Completed |Count|Итог|Число завершенных действий рабочего процесса.|None|
|ActionsSucceeded|Actions Succeeded |Count|Итог|Число успешно выполненных действий рабочего процесса.|None|
|ActionsFailed|Actions Failed |Count|Итог|Число действий рабочего процесса, завершившихся сбоем.|None|
|ActionsSkipped|Actions Skipped |Count|Итог|Число пропущенных действий рабочего процесса.|None|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|None|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|None|
|ActionThrottledEvents|Action Throttled Events|Count|Итог|Число событий регулирования действия рабочего процесса.|None|
|TriggersStarted|Triggers Started |Count|Итог|Число запущенных триггеров рабочего процесса.|None|
|TriggersCompleted|Triggers Completed |Count|Итог|Число завершенных триггеров рабочего процесса.|None|
|TriggersSucceeded|Triggers Succeeded |Count|Итог|Число успешно выполненных триггеров рабочего процесса.|None|
|TriggersFailed|Triggers Failed |Count|Итог|Число триггеров рабочего процесса, завершившихся со сбоем.|None|
|TriggersSkipped|Triggers Skipped|Count|Итог|Число пропущенных триггеров рабочего процесса.|None|
|TriggersFired|Triggers Fired |Count|Итог|Число активированных триггеров рабочего процесса.|None|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|None|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|None|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|None|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итог|Число событий регулирования триггера рабочего процесса.|None|
|ИнтеграцияServiceяСредаПроцессОбработка|Использование процессора рабочего процесса для среды службы интеграции|Процент|Среднее|Использование процессора рабочего процесса для среды службы интеграции.|None|
|ИнтеграцияServiceяОкружающаясредаПроцессПамятиИспользование|Использование памяти рабочего процесса для среды службы интеграции|Процент|Среднее|Использование памяти рабочего процесса для среды службы интеграции.|None|
|ИнтеграцияServiceяСредаКоннекорProcessorUsage|Использование процессора соединения для среды интеграционного обслуживания|Процент|Среднее|Использование процессора соединения для среды служб интеграции.|None|
|ИнтеграцияServiceяСредаКоннекорПамятьИспользование|Использование памяти соединения для среды службы интеграции|Процент|Среднее|Использование памяти соединения для среды службы интеграции.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Отмененные забеги|Отмененные забеги|Count|Итог|Количество отмененных запусков для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Отмена запросных запусков|Отмена запросных запусков|Count|Итог|Количество запусков, где была запрошена отмена для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Завершено работает|Завершено работает|Count|Итог|Количество успешно выполненных запусков для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|циклы выполнения со сбоем;|циклы выполнения со сбоем;|Count|Итог|Количество сбой запусков для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Завершение забегов|Завершение забегов|Count|Итог|Количество ввода заданных заданий для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Не отвечая забеги|Не отвечая забеги|Count|Итог|Количество запусков, не отвечающих за это рабочее пространство|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Не запущенные запуски|Не запущенные запуски|Count|Итог|Количество запусков в незапущенном состоянии для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Подготовка забегов|Подготовка забегов|Count|Итог|Количество запусков, которые готовятся к этому рабочему пространству|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Запуски подготовки|Запуски подготовки|Count|Итог|Количество запусков, которые готовятся к данному рабочему пространству|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Очередные забеги|Очередные забеги|Count|Итог|Количество запусков, которые стоят в очереди для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Запущенные запуски|Запущенные запуски|Count|Итог|Количество запущенных запусков для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Запуск исбы|Запуск исбы|Count|Итог|Количество запущенных запусков для этого рабочего пространства|Сценарий,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|ошибки|ошибки|Count|Итог|Количество ошибок выполнения в этом рабочем пространстве|Сценарий|
|Предупреждения|Предупреждения|Count|Итог|Количество предупреждений о запуске в этом рабочем пространстве|Сценарий|
|Модель Регистр Аймон преуспел|Модель Регистр Аймон преуспел|Count|Итог|Количество регистраций моделей, которые преуспели в этом рабочем пространстве|Сценарий|
|Модель Регистр не удалось|Модель Регистр не удалось|Count|Итог|Количество регистраций моделей, которые не удалось в этом рабочем пространстве|Сценарий,Статус-код|
|Модель развертывания запущен|Модель развертывания запущен|Count|Итог|Количество развертываний моделей, запущенных в этом рабочем пространстве|Сценарий|
|Модель Развертывание Успешно|Модель Развертывание Успешно|Count|Итог|Количество развертываний моделей, которые преуспели в этом рабочем пространстве|Сценарий|
|Развертывание модели не удалось|Развертывание модели не удалось|Count|Итог|Количество развертываний моделей, которые не удалось сделать в этом рабочем пространстве|Сценарий,Статус-код|
|Всего узлов|Всего узлов|Count|Среднее|Количество общих узлов. Эта сумма включает в себя некоторые из активных узлов, простоя узлов, непригодных узлов, premepted узлов, Оставляя узлы|Сценарий,Название кластера|
|Активные узлы|Активные узлы|Count|Среднее|Количество узлов Acitve. Это узлы, которые активно работают.|Сценарий,Название кластера|
|Узлы безделья|Узлы безделья|Count|Среднее|Количество простаивающих узлов. Простаивающие узлы являются узлами, которые не работают над любыми заданиями, но могут принять новую работу, если таковые имеются.|Сценарий,Название кластера|
|Непригодные узлы|Непригодные узлы|Count|Среднее|Количество непригодных узлов. Непригодные узлы не функционируют из-за некоторой неразрешимой проблемы. Azure будет перерабатывать эти узлы.|Сценарий,Название кластера|
|Упреждаемые узлы|Упреждаемые узлы|Count|Среднее|Количество упреждаемых узлов. Эти узлы являются узлами с низким приоритетом, которые отнимаются из доступного пула узлов.|Сценарий,Название кластера|
|Оставляя узлы|Оставляя узлы|Count|Среднее|Количество остывших узлов. Оставляя узлы являются узлами, которые только что закончили обработку задания и пойдут в состояние простоя.|Сценарий,Название кластера|
|Всего ядер|Всего ядер|Count|Среднее|Количество общих ядер|Сценарий,Название кластера|
|Активные ядра|Активные ядра|Count|Среднее|Количество активных ядер|Сценарий,Название кластера|
|Ядра холостого хода|Ядра холостого хода|Count|Среднее|Количество холостых ядер|Сценарий,Название кластера|
|Непригодные для угобы|Непригодные для угобы|Count|Среднее|Количество непригодных ядер|Сценарий,Название кластера|
|Упреждаемые ядра|Упреждаемые ядра|Count|Среднее|Количество упреждающих ядер|Сценарий,Название кластера|
|Оставляя ядра|Оставляя ядра|Count|Среднее|Количество опрокинувших ядер|Сценарий,Название кластера|
|Процент использования квот|Процент использования квот|Count|Среднее|Процент использованных квот|Сценарий,ClusterName,VmFamilyName,VmPriority|
|CpuUtilization|CpuUtilization|Count|Среднее|CPU (Preview) (ЦП (предварительная версия))|Сценарий,runId,NodeId,CreatedTime|
|GpuUtilization|GpuUtilization|Count|Среднее|GPU (Предварительный просмотр)|Сценарий,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|ApiCategory,Apiname,ResultType,ResponseCode|
|Доступность|Доступность|Процент|Среднее|Наличие AA|Апикатегория, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Исходящие|Исходящие|Байты|Итог|Объем данных egress, в байтах.|OutputFormat|
|SuccessE2ELatency|Успех до конца Задержки|Миллисекунды|Среднее|Средняя задержка успешных запросов в миллисекундах.|OutputFormat|
|Requests|Requests|Count|Итог|Запросы в конечную точку потоковой передачи.|OutputFormat,HttpstatusCode,Ошибкакод|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Активная квокты|Квота активов|Count|Среднее|Сколько активов разрешено для текущего счета медиа-службы|None|
|АктивЫ|Подсчет активов|Count|Среднее|Сколько активов уже создано в текущем аккаунте медиа-службы|None|
|АктивКваиспользованныйПроцент|Квота активов используется процент|Процент|Среднее|Процент активов, используемых в текущем счете медиа-службы|None|
|КонтентКейПолитикиКвота|Квота политики ключевой контента|Count|Среднее|Сколько ключей содержимого разрешено для текущей учетной записи медиа-службы|None|
|ContentKeyPolicyCount|Количество ключевых политик содержимого|Count|Среднее|Сколько ключевых политик контента уже создано в текущей учетной записи медиа-службы|None|
|ContentKeyPolicyИспользованныйПроцент|Квота политики ключевых содержимого используется в процентах|Процент|Среднее|Политика ключей содержимого используется в процентах в текущей учетной записи медиа-услуг|None|
|Потоковая политика|Квота политики потоковой передачи|Count|Среднее|Сколько политик потоковой передачи разрешено для текущей учетной записи медиа-службы|None|
|StreamingPolicyCount|Количество потоков политики|Count|Среднее|Сколько политик потоковой передачи уже создано в текущей учетной записи медиа-службы|None|
|StreamingPolicy's|Квота политики потоковой передачи используется в процентах|Процент|Среднее|Политика потоковой передачи используется в процентах в текущей учетной записи медиа-службы|None|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|АктивыПреобразованы|Активы конвертированы|Count|Итог|Общее количество переконвертированных активов|AppId,Resourceid,SDKVersion|
|ActiveRenderingСессии|Сеансы активного рендеринга|Count|Итог|Общее количество активных сеансов рендеринга|AppId,Resourceid,SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageReadLatency|Средняя задержка чтения|MilliSeconds|Среднее|Средняя задержка чтения в миллисекундах на операцию|None|
|AverageWriteLatency|Средняя задержка записи|MilliSeconds|Среднее|Средняя задержка записи в миллисекундах на операцию|None|
|VolumeLogicalSize|Объем потребляемый размер|Байты|Среднее|Логический размер тома (используемые байты)|None|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|Байты|Среднее|Размер всех моментальных снимков в томе|None|
|ReadIops|Read iops (Число операций ввода-вывода в секунду при чтении)|Число/с|Среднее|Число операций ввода-вывода в секунду при чтении|None|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|Число/с|Среднее|Число операций ввода-вывода в секунду при записи|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Бассейн, выделенный для размера объема|Байты|Среднее|Используемый выделенный размер пула|None|
|VolumePoolTotalLogicalSize|Бассейн Потребляемый размер|Байты|Среднее|Сумма логического размера всех томов, входящих в пул|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Байты|Итог|Число байт, отправленных сетевым интерфейсом|None|
|BytesReceivedRate|Получено байт|Байты|Итог|Число байт, полученных сетевым интерфейсом|None|
|PacketsSentRate|Отправлено пакетов|Count|Итог|Число пакетов, отправленных сетевым интерфейсом|None|
|PacketsReceivedRate|Получено пакетов|Count|Итог|Число пакетов, полученных сетевым интерфейсом|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Среднее|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|ФронтендИПадрес,ФронтендПорт|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Count|Среднее|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType,BackendPort,FrontendIPАдрес,FrontendPort,BackendIPАдрес|
|ByteCount|Количество байтов|Count|Итог|Общее количество байтов, переданных за период времени|FrontendIPАдрес,FrontendPort,Направление|
|PacketCount|Количество пакетов|Count|Итог|Общее количество пакетов, переданных за период времени|FrontendIPАдрес,FrontendPort,Направление|
|SYNCount|Количество пакетов SYN|Count|Итог|Общее количество пакетов SYN, переданных за период времени|FrontendIPАдрес,FrontendPort,Направление|
|SnatConnectionCount|Количество подключений SNAT|Count|Итог|Общее количество подключений SNAT, созданных за период времени|FrontendIPАдрес,BackendIPАдрес,СоединениеГосударство|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Count|Итог|Общее число портов SNAT, выделенных за период времени|FrontendIPАдрес,BackendIPАдрес,ПротоколТип,ОжиданиеУдаление|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Count|Итог|Общее количество портов SNAT, использованных за период времени|FrontendIPАдрес,BackendIPАдрес,ПротоколТип,ОжиданиеУдаление|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Итог|Количество запросов, выполненных для зоны DNS|None|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Количество наборов записей в зоне DNS|None|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PacketsInDDoS|Входящие пакеты (атака DDoS)|Число/с|Максимальная|Входящие пакеты (атака DDoS)|None|
|PacketsDroppedDDoS|Удаленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты (атака DDoS)|None|
|PacketsForwardedDDoS|Перенаправленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты (атака DDoS)|None|
|TCPPacketsInDDoS|Входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Входящие пакеты TCP (атака DDoS)|None|
|TCPPacketsDroppedDDoS|Удаленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты TCP (атака DDoS)|None|
|TCPPacketsForwardedDDoS|Перенаправленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты TCP (атака DDoS)|None|
|UDPPacketsInDDoS|Входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Входящие пакеты UDP (атака DDoS)|None|
|UDPPacketsDroppedDDoS|Удаленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты UDP (атака DDoS)|None|
|UDPPacketsForwardedDDoS|Перенаправленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты UDP (атака DDoS)|None|
|BytesInDDoS|Входящие байты (атака DDoS)|Байт/с|Максимальная|Входящие байты (атака DDoS)|None|
|BytesDroppedDDoS|Удаленные входящие байты (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты (атака DDoS)|None|
|BytesForwardedDDoS|Перенаправленные входящие байты (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты (атака DDoS)|None|
|TCPBytesInDDoS|Входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Входящие байты TCP (атака DDoS)|None|
|TCPBytesDroppedDDoS|Удаленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты TCP (атака DDoS)|None|
|TCPBytesForwardedDDoS|Перенаправленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты TCP (атака DDoS)|None|
|UDPBytesInDDoS|Входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Входящие байты UDP (атака DDoS)|None|
|UDPBytesDroppedDDoS|Удаленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты UDP (атака DDoS)|None|
|UDPBytesForwardedDDoS|Перенаправленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты UDP (атака DDoS)|None|
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Count|Максимальная|Выполняется ли атака DDoS|None|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|None|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|None|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|None|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Среднее|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Count|Итог|Общее количество байтов, переданных за период времени|Порт,Дирекция|
|PacketCount|Количество пакетов|Count|Итог|Общее количество пакетов, переданных за период времени|Порт,Дирекция|
|SynCount|Количество пакетов SYN|Count|Итог|Общее количество пакетов SYN, переданных за период времени|Порт,Дирекция|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Время в пути для Pings к VM|MilliSeconds|Среднее|Время в пути для Pings отправлено в пункт назначения VM|ИсточникЗаказчикАдрес,НаправлениеЗаказчикАдрес|
|PingMeshProbesFailedPercent|Неудачные пинги для VM|Процент|Среднее|Процент от числа неудачных Pings в общей сложности отправлены Pings назначения VM|ИсточникЗаказчикАдрес,НаправлениеЗаказчикАдрес|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ПриложениеРулехит|Правила применения хит кол|Count|Итог|Количество ударов по правилам приложения|Статус,Причина, Протокол|
|NetworkRuleHit|Сетевые правила хит кол|Count|Итог|Количество ударов по правилам Сети|Статус,Причина, Протокол|
|БрандмауэрЗдоровье|Состояние здоровья брандмауэра|Процент|Среднее|Состояние здоровья брандмауэра|Статус,Причина|
|ДанныеОбработанные данные|Обработанные данные|Байты|Итог|Общий объем данных, обрабатываемых Брандмауэром|None|
|SNATPortUtilization|Использование порта SNAT|Процент|Среднее|Использование порта SNAT|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Среднее|Количество байтов в секунду, обрабатываемых шлюзом приложений|None|
|UnhealthyHostCount|Количество неработоспособных узлов|Count|Среднее|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Count|Среднее|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Count|Итог|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Запросы в минуту на здорового хозяина|Count|Среднее|Среднее количество запросов в минуту на здоровый бэкэнд-хост в бассейне|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Count|Итог|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Count|Итог|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Count|Итог|Число текущих установленных подключений к шлюзу приложений|None|
|НовыеподключенияВВсекунда|Новые соединения в секунду|Число/с|Среднее|Новые соединения в секунду, установленные с помощью шлюза приложения|None|
|CpuUtilization|Использование ЦП|Процент|Среднее|Текущее использование процессора шлюза приложения|None|
|ЕмкостьЕдиницы|Единицы текущего емкости|Count|Среднее|Потребляемые единицы емкости|None|
|FixedBillableЕмкостьЕдиницы|Фиксированные единицы платежеспособной емкости|Count|Среднее|Единицы минимальной емкости, которые будут заряжены|None|
|РасчетныебиллеммощностиЕдиницы|Расчетные единицы билльоспособности|Count|Среднее|Расчетная мощность единиц, которые будут заряжены|None|
|Вычислительные единицы|Текущие вычислительные единицы|Count|Среднее|Потребляемые вычислительные единицы|None|
|БэкендОтветСтатус|Статус ответа бэкэнда|Count|Итог|Количество кодов ответов HTTP, генерируемых членами бэкэнда. Это не включает в себя коды ответов, генерируемые шлюзом приложения.|BackendServer,Backendpool,BackendHttpSetting,HttpstatusGroup|
|TlsПротокол|Протокол клиента TLS|Count|Итог|Количество запросов TLS и не-TLS, инициированных клиентом, установиввшим подключение к шлюзу приложения. Для просмотра распределения протоколов TLS отфильтруйте по измерению протокола TLS.|Слушатель, TlsProtocol|
|BytesSent|Отправлено байт|Байты|Итог|Общее количество байтов, отправленных порталом приложений клиентам|Прослушиватель|
|BytesReceived|Получено байт|Байты|Итог|Общее количество байтов, полученных Порталом приложений от клиентов|Прослушиватель|
|ClientRtt|Клиент RTT|MilliSeconds|Среднее|Среднее время в пути между клиентами и шлюзом приложения. Эта метрика показывает, сколько времени требуется для установления подтверждений связей и возврата|Прослушиватель|
|ПриложениеGatewayTotalTime|Приложение шлюз Общее время|MilliSeconds|Среднее|Среднее время, необходимое для обработки запроса и отправки его ответа. Это рассчитывается как среднее значение интервала с момента, когда шлюз приложения получает первый байт запроса HTTP до времени завершения операции отправки ответа. Важно отметить, что это обычно включает время обработки шлюза приложения, время, когда пакеты запросов и ответов перемещаются по сети, и время, которое сервер бэкэнда принял для ответа.|Прослушиватель|
|БэкендКоннети|Бэкэнд Время подключения|MilliSeconds|Среднее|Время, затраченное на создание соединения с сервером бэкэнда|Слушатель,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Бэкэнд Первый байт Время ответа|MilliSeconds|Среднее|Интервал времени между началом создания соединения с сервером бэкэнда и получением первого байта заголовка ответа, приближение времени обработки сервера бэкэнда|Слушатель,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Бэкэнд Последний байт Время ответа|MilliSeconds|Среднее|Интервал времени между началом создания соединения с сервером бэкэнда и получением последнего байта тела реагирования|Слушатель,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Веб-приложение Firewall v1 Общее распределение правил|Count|Итог|Веб-приложение Firewall v1 Полное распределение правил для входящего трафика|ПравилоГруппа,ПравилоId|
|Заблокированный Счет|Веб-приложение Firewall v1 Заблокированное распределение правил запросов|Count|Итог|Web Application Firewall v1 заблокировал распределение правил запросов|ПравилоГруппа,ПравилоId|
|ЗаблокированныйReqCount|Веб-приложение Firewall v1 Заблокированные запросы Отсчет|Count|Итог|Веб-приложение Firewall v1 заблокированные запросы подсчитывают|None|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|Байт/с|Среднее|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|None|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|Байт/с|Среднее|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|None|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Count|Максимальная|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Среднее|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName,RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Итог|Исходящие байты в туннеле|ConnectionName,RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Итог|Входящие байты в туннеле|ConnectionName,RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Count|Итог|Количество исходящих пакетов в туннеле|ConnectionName,RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Count|Итог|Количество входящих пакетов в туннеле|ConnectionName,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Count|Итог|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName,RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Count|Итог|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Среднее|Уровень Rx Light в dBm|Линк,Лейн|
|TxLightLevel|TxLightLevel|Count|Среднее|Уровень света Tx в dBm|Линк,Лейн|
|Админгосударство|Админгосударство|Count|Среднее|Админ состояние порта|Ссылка|
|Линейныйпротокол|Линейныйпротокол|Count|Среднее|Статус линейного протокола порта|Ссылка|
|PortBitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Ссылка|
|PortBitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Ссылка|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|PeeringType|
|GlobalReachBitsInВторая|GlobalReachBitsInВторая|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|PeeredCircuitSKey|
|BgpДоступность|Доступность Bgp|Процент|Среднее|Доступность BGP от MSEE ко всем коллегам.|PeeringType,Peer|
|ArpAvailability|Доступность Arp|Процент|Среднее|Доступность ARP от MSEE ко всем коллегам.|PeeringType,Peer|
|КросикаплиInPerSecond|DroppedInBitsPerSecond|Число/с|Среднее|Входные биты данных упали в секунду|None|
|КросидБитсOutPerSecond|ВыпавшийOutBitsPerSecond|Число/с|Среднее|Количество данных, выпавших на произвол, снизилось в секунду|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|None|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|None|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|СоединениеИмя|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|СоединениеИмя|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Count|Итог|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Count|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Процент|Среднее|Доля проб мониторинга подключения с ошибками, в процентах|None|
|AverageRoundtripMs|Avg. Время в оба конца (ms)|MilliSeconds|Среднее|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|None|
|ЧекиНейдПроцент|Проверки Не удалось Процент (Предварительный просмотр)|Процент|Среднее|% проверок мониторинга подключения не удалось|SourceАдрес,Sourcename,SourceResourceid,SourceType,Протокол,Адрес назначения,назначение,пункт назначения,destinationresourceid,destinationType,destinationport,TestGroupName,TestConfigurationName|
|RoundTriptimeMs|Время в оба конца (ms) (Предварительный просмотр)|MilliSeconds|Среднее|Время в пути в миллисекундах для проверки мониторинга подключения|SourceАдрес,Sourcename,SourceResourceid,SourceType,Протокол,Адрес назначения,назначение,пункт назначения,destinationresourceid,destinationType,destinationport,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Count|Итог|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|Httpstatus,Httpstatusgroup,ClientRegion,ClientCountry|
|RequestSize|Размер запроса|Байты|Итог|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|Httpstatus,Httpstatusgroup,ClientRegion,ClientCountry|
|ResponseSize|Размер ответа|Байты|Итог|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|Httpstatus,Httpstatusgroup,ClientRegion,ClientCountry|
|BillableResponseSize|Размер оплачиваемого ответа|Байты|Итог|Количество оплачиваемых байт (минимум 2KB за запрос), отправленных в качестве ответов от прокси HTTP/S клиентам.|Httpstatus,Httpstatusgroup,ClientRegion,ClientCountry|
|BackendRequestCount|Число запросов к серверному компоненту|Count|Итог|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|Httpstatus,Httpstatusgroup,Backend|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Среднее|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Серверная часть|
|TotalLatency|Total Latency (Общая задержка)|MilliSeconds|Среднее|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|Httpstatus,Httpstatusgroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Процент|Среднее|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Бэкэнд,БэкендПул|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Итог|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|Название политики,ПравилоИмя, Действие|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsзоны

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Итог|Количество запросов, поданных для частной зоны DNS|None|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Количество рекордных наборов в частной зоне DNS|None|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Процент пропускной способности комплекта, используемая частной зоной DNS|None|
|VirtualNetworkLinkCount|Виртуальная сеть ссылка граф|Count|Максимальная|Количество виртуальных сетей, связанных с частной зоной DNS|None|
|VirtualNetworkLinkЕмкостьУтилизация|Использование виртуальной сетевой связи|Процент|Максимальная|Процент емкости виртуальной сетевой связи, используемой частной зоной DNS|None|
|ВиртуальнаясетьСРегистрацияЛинктом|Виртуальная сеть Регистрация Ссылка Граф|Count|Максимальная|Количество виртуальных сетей, подключенных к частной зоне DNS с включенной автоматической регистрацией|None|
|ВиртуальнаясетьСРегистрационнаяспособностьУтилизация|Использование виртуальной сети регистрации ссылок|Процент|Максимальная|Процент виртуальной сетевой связи с авторегистрационной емкостью, используемым частной зоной DNS|None|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Итог|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |None|
|registration.create|Операции создания регистрации|Count|Итог|Число всех успешных операций создания регистраций.|None|
|registration.update|Операции обновления регистрации|Count|Итог|Число всех успешных операций обновления регистраций.|None|
|registration.get|Операции чтения регистрации|Count|Итог|Число всех успешных запросов регистрации.|None|
|registration.delete|Операции удаления регистрации|Count|Итог|Число всех успешных операций удаления регистраций.|None|
|incoming|Входящие сообщения|Count|Итог|Число всех успешно отправленных вызовов API. |None|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Итог|Отменено запланированных push-уведомлений|None|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Count|Итог|Отменено запланированных push-уведомлений|None|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Итог|Запланированных уведомлений в состоянии ожидания|None|
|installation.all|Операции управления установкой|Count|Итог|Операции управления установкой|None|
|installation.get|Операции получения установки|Count|Итог|Операции получения установки|None|
|installation.upsert|Операции создания или обновления установки|Count|Итог|Операции создания или обновления установки|None|
|installation.patch|Операции исправления установки|Count|Итог|Операции исправления установки|None|
|installation.delete|Операции удаления установки|Count|Итог|Операции удаления установки|None|
|outgoing.allpns.success|Успешные уведомления|Count|Итог|Общее число успешных уведомлений.|None|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Итог|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|None|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Итог|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|None|
|outgoing.allpns.channelerror|Ошибки канала|Count|Итог|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|None|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Итог|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|None|
|outgoing.wns.success|Успешные уведомления WNS|Count|Итог|Общее число успешных уведомлений.|None|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|None|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|None|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Итог|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|None|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|None|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Итог|Windows Live недоступна.|None|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Итог|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|None|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Итог|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|None|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Итог|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|None|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Итог|Полезные данные уведомления слишком большие (состояние WNS: 413).|None|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|None|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|None|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|None|
|outgoing.wns.pnserror|Ошибки WNS|Count|Итог|Уведомление не доставлено из-за ошибок связи с WNS.|None|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Итог|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|None|
|outgoing.apns.success|Успешные уведомления APNS|Count|Итог|Общее число успешных уведомлений.|None|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Итог|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|None|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Итог|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|None|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|None|
|outgoing.apns.pnserror|Ошибки APNS|Count|Итог|Количество неудачных отправлений из-за ошибок связи с APNS.|None|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Итог|Общее число успешных уведомлений.|None|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|None|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Итог|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|None|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|None|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Итог|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|None|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|None|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Итог|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|None|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Итог|Количество неудачных отправлений из-за ошибок связи с GCM.|None|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|None|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Итог|Общее число успешных уведомлений.|None|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|None|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|None|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|None|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Итог|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|None|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Итог|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|None|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Итог|Количество неудачных отправлений из-за ошибок связи с MPNS.|None|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|notificationhub.pushes|Все исходящие уведомления|Count|Итог|Все исходящие уведомления из центра уведомлений.|None|
|incoming.all.requests|Все входящие запросы|Count|Итог|Общее количество входящих запросов для концентратора уведомлений|None|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Итог|Общее количество неудачных входящих запросов для концентратора уведомлений|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Процент свободных индексных дескрипторов|Count|Среднее|Average_% Free Inodes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Free Space|Процент свободного места|Count|Среднее|Average_% Free Space|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Inodes|Процент используемых индексных дескрипторов|Count|Среднее|Average_% Used Inodes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Space|Процент используемого места|Count|Среднее|Average_% Used Space|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Read Bytes/sec|Скорость чтения с диска (байт/с) |Count|Среднее|Average_Disk Read Bytes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Reads/sec|Операций чтения с диска в секунду |Count|Среднее|Average_Disk Reads/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Count|Среднее|Average_Disk Transfers/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Write Bytes/sec| Скорость записи на диск (байт/с)|Count|Среднее|Average_Disk Write Bytes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Writes/sec| Операций записи на диск в секунду|Count|Среднее|Average_Disk Writes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Megabytes|Свободно мегабайт|Count|Среднее|Average_Free Megabytes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Logical Disk Bytes/sec|Скорость обмена с логическим диском (байт/с)|Count|Среднее|Average_Logical Disk Bytes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Memory|Процент доступной памяти|Count|Среднее|Average_% Available Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Swap Space|Процент доступной области подкачки|Count|Среднее|Average_% Available Swap Space|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Memory|Процент используемой памяти|Count|Среднее|Average_% Used Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Swap Space|Процент используемой области подкачки|Count|Среднее|Average_% Used Swap Space|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Memory|Доступный объем памяти в МБ|Count|Среднее|Average_Available MBytes Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Swap|Доступный объем подкачки в МБ|Count|Среднее|Average_Available MBytes Swap|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Reads/sec|Операций чтения со страницы в секунду|Count|Среднее|Average_Page Reads/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Writes/sec|Операций записи на страницу в секунду|Count|Среднее|Average_Page Writes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pages/sec|Страниц в секунду|Count|Среднее|Average_Pages/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used MBytes Swap Space|Используемая области подкачки в МБ|Count|Среднее|Average_Used MBytes Swap Space|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory MBytes|Используемый объем памяти в МБ|Count|Среднее|Average_Used Memory MBytes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Transmitted|Всего передано байт|Count|Среднее|Average_Total Bytes Transmitted|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Received|Всего получено байт|Count|Среднее|Average_Total Bytes Received|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes|Всего байт|Count|Среднее|Average_Total Bytes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Packets Transmitted|Всего передано пакетов|Count|Среднее|Average_Total Packets Transmitted|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Packets Received|Всего получено пакетов|Count|Среднее|Average_Total Packets Received|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Rx Errors|Всего ошибок Rx|Count|Среднее|Average_Total Rx Errors|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Tx Errors|Всего ошибок Tx|Count|Среднее|Average_Total Tx Errors|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Collisions|Всего конфликтов|Count|Среднее|Average_Total Collisions|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. время чтения с диска (с)|Avg. Диск сек/ Читать|Count|Среднее|Average_Avg. время чтения с диска (с)|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. время обращения к диску (с)|Avg. Диск сек/Передача|Count|Среднее|Average_Avg. время обращения к диску (с)|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. время записи на диск (с)|Avg. Диск сек/ Запись|Count|Среднее|Average_Avg. время записи на диск (с)|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Physical Disk Bytes/sec|Скорость обмена с физическим диском (байт/с)|Count|Среднее|Average_Physical Disk Bytes/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct Privileged Time|Процент времени работы в привилегированном режиме|Count|Среднее|Average_Pct Privileged Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct User Time|Процент времени пользователя|Count|Среднее|Average_Pct User Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory kBytes|Используемая память в КБ|Count|Среднее|Average_Used Memory kBytes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Virtual Shared Memory|Виртуальная общая память|Count|Среднее|Average_Virtual Shared Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% DPC Time|Процент времени DPC|Count|Среднее|Average_% DPC Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Idle Time|Процент времени простоя|Count|Среднее|Average_% Idle Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Interrupt Time|Процент времени прерывания|Count|Среднее|Average_% Interrupt Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% IO Wait Time|Процент времени ожидания ввода-вывода|Count|Среднее|Average_% IO Wait Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Nice Time|Процент времени работы с низким приоритетом|Count|Среднее|Average_% Nice Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Privileged Time|Процент времени работы в привилегированном режиме|Count|Среднее|Average_% Privileged Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Processor Time|% загруженности процессора|Count|Среднее|Average_% Processor Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% User Time|Процент времени пользователя|Count|Среднее|Average_% User Time|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Physical Memory|Объем свободной физической памяти|Count|Среднее|Average_Free Physical Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Space in Paging Files|Объем свободного места в файлах подкачки|Count|Среднее|Average_Free Space in Paging Files|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Virtual Memory|Объем свободной виртуальной памяти|Count|Среднее|Average_Free Virtual Memory|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processes|Процессы|Count|Среднее|Average_Processes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Size Stored In Paging Files|Объем, сохраненный в файлах подкачки|Count|Среднее|Average_Size Stored In Paging Files|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Uptime|Время доступности|Count|Среднее|Average_Uptime|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Users|Пользователи|Count|Среднее|Average_Users|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Count|Среднее|Average_Current Disk Queue Length|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes|Доступный объем в МБ|Count|Среднее|Average_Available MBytes|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Count|Среднее|Average_% Committed Bytes In Use|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Received/sec|Полученных байтов/с|Count|Среднее|Average_Bytes Received/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Sent/sec|Отправленных байтов/с|Count|Среднее|Average_Bytes Sent/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Total/sec|Всего байтов/с|Count|Среднее|Average_Bytes Total/sec|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processor Queue Length|Длина очереди процессора|Count|Среднее|Average_Processor Queue Length|Компьютер,ObjectName,InstanceName,CounterPath,SourceSystem|
|Пульс|Пульс|Count|Итог|Пульс|Компьютер,OSType,Версия,ИсточникComputerId|
|Update|Update|Count|Среднее|Update|Компьютер,Продукт, Классификация,ОбновлениеГосударство, Необязательно, Утверждено|
|Событие|Событие|Count|Среднее|Событие|Источник,EventLog,Компьютер,СобытиеКатегория,EventLevel,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.peering/peeringServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ПрефиксЛатичность|Задержка префикса|Миллисекунды|Среднее|Медианная задержка префикса|ПрефиксИмя|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Доступность сеанса V4|Процент|Среднее|Доступность сессии V4|ConnectionId|
|СессияДоступностьV6|Доступность сеанса V6|Процент|Среднее|Доступность сессии V6|ConnectionId|
|IngressTrafficRate|Скорость входа трафика|BitsPerSecond|Среднее|Скорость движения ingress в битах в секунду|ConnectionId|
|EgressTrafficRate|Коэффициент трафика на выход|BitsPerSecond|Среднее|Скорость трафика egress в битах в секунду|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунды|Среднее|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Среднее|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Count|Итог|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байты|Среднее|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|Нет измерений|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/счета

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Распределение активовПоклассификация|Распределение активов по классификации|Count|Итог|Указывает количество активов с определенной классификацией, т.е. они классифицируются с этой меткой.|Классификация,Источник,РесурсИД|
|Распределение активовПохранениетипом|Распределение активов по типу хранилища|Count|Итог|Указывает количество активов с определенным типом хранилища.|StorageType,ResourceId|
|КаталогАктивныепользователи|Ежедневные активные пользователи|Count|Итог|Количество активных пользователей ежедневно|ResourceId|
|КаталогИспользование|Распределение использования по операции|Count|Итог|Укажите количество операций, которые пользователь делает в каталоге, т.е. доступ, поиск, глоссарий.|Операция,РесурсИД|
|NumberOfAssetsWithClassifications|Количество активов, по крайней мере одной классификации|Count|Среднее|Указывает количество активов, по крайней мере одной классификации тегов.|ResourceId|
|Сканотменено|Сканирование отменено|Count|Итог|Указывает количество отмененных сканирований.|ResourceId|
|СканированиеЗавершено|Сканирование завершено|Count|Итог|Указывает количество успешно завершенных сканирований.|ResourceId|
|Сканнесусис|Сканирование не удалось|Count|Итог|Указывает на количество сканов, которые не были сканировались.|ResourceId|
|ScanTimeTaken|Время сканирования, занесено|Секунды|Итог|Указывает общее время сканирования за считанные секунды.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Итог|Выполненные ListenerConnections для Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Итог|ClientError в ListenerConnections для Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Итог|ServerError в ListenerConnections для Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-Success|SenderConnections-Success|Count|Итог|Выполненные SenderConnections для Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Итог|ClientError в SenderConnections для Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Итог|ServerError в SenderConnections для Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Итог|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Итог|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Итог|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Итог|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Итог|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Итог|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Итог|Всего SenderDisconnects для Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Среднее|Среднее время задержки поиска для службы поиска.|None|
|SearchQueriesPerSecond|Search queries per second|Число/с|Среднее|Число поисковых запросов в секунду для службы поиска.|None|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Среднее|Процент отрегулированных поисковых запросов для службы поиска.|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Общее количество успешных запросов для пространства имен|EntityName,OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.ServiceBus.|EntityName,OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.ServiceBus.|EntityName,OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.ServiceBus.|EntityName,OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Count|Итог|Число активных подключений для Microsoft.ServiceBus.|None|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.ServiceBus.|EntityName|
|Размер|Размер|Байты|Среднее|Размер очереди или раздела в байтах.|EntityName|
|Сообщения|Число сообщений в очереди или разделе.|Count|Среднее|Число сообщений в очереди или разделе.|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе.|Count|Среднее|Число активных сообщений в очереди или разделе.|EntityName|
|DeadletteredСообщения|Подсчет мертвых букв сообщений в очереди / Тема.|Count|Среднее|Подсчет мертвых букв сообщений в очереди / Тема.|EntityName|
|ЗапланированныеСообщения|Подсчет запланированных сообщений в очереди/теме.|Count|Среднее|Подсчет запланированных сообщений в очереди/теме.|EntityName|
|NamespaceCpuUsage|ЦП|Процент|Максимальная|Сервис автобус премиум namespace процессора метрики использования.|Реплика|
|NamespaceMemoryUsage|Использование памяти|Процент|Максимальная|Служба автобус премиум имя пространства использования метрики.|Реплика|
|CPUXNS|Процессор (Обезображенный)|Процент|Максимальная|Сервис автобус премиум namespace процессора метрики использования. Эта метрика лишена. Пожалуйста, используйте вместо этого метрика процессора (NamespaceCpuUsage).|Реплика|
|WSXNS|Использование памяти (Обезвоболенно)|Процент|Максимальная|Служба автобус премиум имя пространства использования метрики. Использовать эту метрику не рекомендуется. Пожалуйста, используйте метрику использования памяти (NamespaceMemoryUsage).|Реплика|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/приложения

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ВыделеноCpu|ВыделеноCpu|Count|Среднее|Cpu, выделенный для этого контейнера в милли-ядрах|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|ВыделеннаяПамять|ВыделеннаяПамять|Байты|Среднее|Память, выделенная для этого контейнера в MB|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Среднее|Фактическое использование процессора в милли-ядрах|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|Фактическаяпамять|Фактическаяпамять|Байты|Среднее|Фактическое использование памяти в МБ|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|CpuUtilization|CpuUtilization|Процент|Среднее|Использование процессора для этого контейнера в процентах от|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|ПамятьУтилизация|ПамятьУтилизация|Процент|Среднее|Использование процессора для этого контейнера в процентах от|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName|
|ПриложениеСтатус|ПриложениеСтатус|Count|Среднее|Статус приложения Сервисная сетка|Имя приложения,Статус|
|СервисСтатус|СервисСтатус|Count|Среднее|Состояние здоровья службы в приложении Service Fabric Mesh|Имя приложения,Статус,Имя сервиса|
|СервисРепликАстампром|СервисРепликАстампром|Count|Среднее|Состояние работоспособности реплики службы в приложении Service Fabric Mesh|ПриложениеИмя,Статус,ServiceName,ServiceReplicaName|
|КонтейнерСтатус|КонтейнерСтатус|Count|Среднее|Состояние контейнера в приложении Service Fabric Mesh|ПриложениеИмя,ServiceName,CodePackageName,ServiceReplicaName,Статус|
|ПерезапускCount|ПерезапускCount|Count|Среднее|Перезапуск отсчета контейнера в приложении Service Fabric Mesh|ПриложениеИмя,Статус,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Count|Максимальная|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Количество сообщений|Count|Итог|Общее количество сообщений.|None|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байты|Итог|Входящий трафик службы|None|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байты|Итог|Исходящий трафик службы|None|
|UserErrors|Ошибки пользователей|Процент|Максимальная|Процент ошибок пользователей|None|
|SystemErrors|Системные ошибки|Процент|Максимальная|Процент системных ошибок|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|None|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|None|
|log_write_percent|Log IO percentage|Процент|Среднее|Процент регистрации IO. Не применяется к хранилищам данных.|None|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент ДТУ. Применяется к базам данных на базе данных DTU.|None|
|носителей.|Место, занятое данными|Байты|Максимальная|Используемое пространство данных. Не применяется к хранилищам данных.|None|
|connection_successful|Успешные подключения|Count|Итог|Успешные подключения|None|
|connection_failed|Неудачные подключения|Count|Итог|Неудачные подключения|None|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Итог|Заблокировано брандмауэром|None|
|взаимоблокировка|Взаимоблокировки|Count|Итог|Взаимоблокировок. Не применяется к хранилищам данных.|None|
|storage_percent|Используемое пространство данных|Процент|Максимальная|Пространство данных используется процентов. Не применяется к хранилищам данных или гипермасштабным базам данных.|None|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранения В памяти OLTP. Не применяется к хранилищам данных.|None|
|workers_percent|Workers percentage|Процент|Среднее|Процент работников. Не применяется к хранилищам данных.|None|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент сеансов. Не применяется к хранилищам данных.|None|
|dtu_limit|Лимит DTU|Count|Среднее|DTU Limit. Применяется к базам данных на базе данных DTU.|None|
|dtu_used|DTU used|Count|Среднее|DTU используется. Применяется к базам данных на базе данных DTU.|None|
|cpu_limit|Лимит процессора|Count|Среднее|Лимит процессора. Применяется к базам данных на основе vCore.|None|
|cpu_used|Используемый процессор|Count|Среднее|Используется процессор. Применяется к базам данных на основе vCore.|None|
|dwu_limit|Лимит DWU|Count|Максимальная|Лимит DWU. Применяется только к хранилищам данных.|None|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|Процент DWU. Применяется только к хранилищам данных.|None|
|dwu_used|DWU used|Count|Максимальная|DWU используется. Применяется только к хранилищам данных.|None|
|cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Процент|Максимальная|Кэш хит процент. Применяется только к хранилищам данных.|None|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Процент|Максимальная|Кэш использовал процент. Применяется только к хранилищам данных.|None|
|sqlserver_process_core_percent|Основные проценты основных процессов сервера Сервера|Процент|Максимальная|Использование процессора в процентах от процесса S'L DB. Не применяется к хранилищам данных.|None|
|sqlserver_process_memory_percent|Процент памяти процесса обработки серверов S'L|Процент|Максимальная|Использование памяти в процентах от процесса S'L DB. Не применяется к хранилищам данных.|None|
|tempdb_data_size|Темпб данных файл размер килобайт|Count|Максимальная|Темп файла данных Размер килобайт. Не применяется к хранилищам данных.|None|
|tempdb_log_size|Tempdb журнал файл размер килобайт|Count|Максимальная|Tempdb журнал файл размер килобайт. Не применяется к хранилищам данных.|None|
|tempdb_log_used_percent|Tempdb Процент Журнала Используется|Процент|Максимальная|Tempdb Процент Журнала Используется. Не применяется к хранилищам данных.|None|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Процент|Среднее|Местный процент соблазнительных. Применяется только к хранилищам данных.|None|
|app_cpu_billed|App Процессор выставлен счет|Count|Итог|App Процессор выставлен счет. Применяется к базам данных без серверов.|None|
|app_cpu_percent|Процент процессора приложения|Процент|Среднее|Процент процессора приложения. Применяется к базам данных без серверов.|None|
|app_memory_percent|Процент памяти приложения|Процент|Среднее|Процент памяти приложения. Применяется к базам данных без серверов.|None|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделено хранилище данных. Не применяется к хранилищам данных.|None|
|memory_usage_percent|Процент памяти|Процент|Максимальная|Процент памяти. Применяется только к хранилищам данных.|None|
|dw_backup_size_gb|Размер хранилища данных|Count|Итог|Размер хранилища данных состоит из размера данных и журнала транзакций. Метрика учитывается в части 'Хранение' вашего счета. Применяется только к хранилищам данных.|None|
|dw_snapshot_size_gb|Размер моментального хранения|Count|Итог|Размер моментального хранения — это размер дополнительных изменений, захваченных моментальными снимками для создания пользовательских и автоматических точек восстановления. Метрика учитывается в части 'Хранение' вашего счета. Применяется только к хранилищам данных.|None|
|dw_geosnapshot_size_gb|Размер хранилища аварийного восстановления|Count|Итог|Размер хранилища аварийного восстановления отражается в вашем счете как «Хранилище аварийного восстановления». Применяется только к хранилищам данных.|None|
|wlg_allocation_relative_to_system_percent|Распределение группрабочей рабочей нагрузки по системным процентам|Процент|Максимальная|Выделение процентной доли ресурсов по отношению ко всей системе на одну рабочую группу. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Распределение группрабочей рабочей нагрузки на процент ресурсов крышки|Процент|Максимальная|Выделение процентной доли ресурсов по отношению к указанному лимиту ресурсов на рабочую группу. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|wlg_active_queries|Активные запросы группы рабочей нагрузки|Count|Итог|Активные запросы в группе рабочей нагрузки. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|wlg_queued_queries|Запросы группрабочей рабочей нагрузки в очереди|Count|Итог|Запросы в очереди в группе рабочей нагрузки. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|active_queries|Активные запросы|Count|Итог|Активные запросы во всех группах рабочей нагрузки. Применяется только к хранилищам данных.|None|
|queued_queries|Запросы в очереди|Count|Итог|Запросы в очередях во всех группах рабочей нагрузки. Применяется только к хранилищам данных.|None|
|wlg_active_queries_timeouts|Тайм-ауты группы рабочей нагрузки|Count|Итог|Запросы, приуроченные к группе рабочей нагрузки. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|wlg_effective_min_resource_percent|Эффективный мин-процент ресурсов|Процент|Максимальная|Минимальный процент ресурсов, зарезервированных и изолированных для группы рабочей нагрузки, с учетом минимального уровня обслуживания. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|wlg_effective_cap_resource_percent|Эффективный процент ресурсов крышки|Процент|Максимальная|Твердый предел процентной доли ресурсов, допустимой для рабочей группы, с учетом эффективного процента ресурсов Мин, выделяемых для других групп рабочей нагрузки. Применяется только к хранилищам данных.|Название рабочей группы нагрузки,IsuserDefined|
|full_backup_size_bytes|Полный размер хранилища резервного копирования|Байты|Максимальная|Совокупный полный размер хранения резервного копирования. Применяется к базам данных на основе vCore. Не применяется к базам данных Hyperscale.|None|
|diff_backup_size_bytes|Дифференциальный размер хранилища резервного копирования|Байты|Максимальная|Совокупный размер резервного копирования. Применяется к базам данных на основе vCore. Не применяется к базам данных Hyperscale.|None|
|log_backup_size_bytes|Размер хранилища резервного копирования журнала|Байты|Максимальная|Совокупный размер хранилища резервного копирования журнала. Применяется к базам данных на основе vCore и Hyperscale.|None|
|snapshot_backup_size_bytes|Размер хранилища резервного копирования моментального снимка|Байты|Максимальная|Совокупный размер хранилища резервного копирования моментального снимка. Применяется к базам данных Hyperscale.|None|
|base_blob_size_bytes|Размер базового кабы|Байты|Максимальная|Базовый размер хранилища капли. Применяется к базам данных Hyperscale.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|None|
|database_cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|DatabaseResourceId|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|None|
|database_physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|DatabaseResourceId|
|log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|None|
|database_log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|DatabaseResourceId|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент ДТУ. Применяется к эластичным бассейнам на основе DTU.|None|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|DatabaseResourceId|
|storage_percent|Используемое пространство данных|Процент|Среднее|Используемое пространство данных|None|
|workers_percent|Workers percentage|Процент|Среднее|Workers percentage|None|
|database_workers_percent|Workers percentage|Процент|Среднее|Workers percentage|DatabaseResourceId|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|None|
|database_sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|DatabaseResourceId|
|eDTU_limit|eDTU limit|Count|Среднее|eDTU предел. Применяется к эластичным бассейнам на основе DTU.|None|
|storage_limit|Максимальный размер данных|Байты|Среднее|Максимальный размер данных|None|
|eDTU_used|eDTU used|Count|Среднее|eDTU используется. Применяется к эластичным бассейнам на основе DTU.|None|
|database_eDTU_used|eDTU used|Count|Среднее|eDTU used|DatabaseResourceId|
|storage_used|Место, занятое данными|Байты|Среднее|Место, занятое данными|None|
|database_storage_used|Место, занятое данными|Байты|Среднее|Место, занятое данными|DatabaseResourceId|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранилища выполняющейся в памяти OLTP|None|
|cpu_limit|Лимит процессора|Count|Среднее|Лимит процессора. Применяется к эластичным бассейнам на основе vCore.|None|
|database_cpu_limit|Лимит процессора|Count|Среднее|Лимит процессора|DatabaseResourceId|
|cpu_used|Используемый процессор|Count|Среднее|Используется процессор. Применяется к эластичным бассейнам на основе vCore.|None|
|database_cpu_used|Используемый процессор|Count|Среднее|Используемый процессор|DatabaseResourceId|
|sqlserver_process_core_percent|Основные проценты основных процессов сервера Сервера|Процент|Максимальная|Использование процессора в процентах от процесса S'L DB. Применяется к эластичным бассейнам.|None|
|sqlserver_process_memory_percent|Процент памяти процесса обработки серверов S'L|Процент|Максимальная|Использование памяти в процентах от процесса S'L DB. Применяется к эластичным бассейнам.|None|
|tempdb_data_size|Темпб данных файл размер килобайт|Count|Максимальная|Темпб данных файл размер килобайт|None|
|tempdb_log_size|Tempdb журнал файл размер килобайт|Count|Максимальная|Tempdb журнал файл размер килобайт|None|
|tempdb_log_used_percent|Tempdb Процент Журнала Используется|Процент|Максимальная|Tempdb Процент Журнала Используется|None|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|None|
|database_allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|DatabaseResourceId|
|allocated_data_storage_percent|Пространство данных, выделенное в процентах|Процент|Максимальная|Пространство данных, выделенное в процентах|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|База данныхРесурсИд,ЭластичныйПулРесурсИд|
|storage_used|Место, занятое данными|Байты|Среднее|Место, занятое данными|ElasticPoolResourceId|
|database_storage_used|Место, занятое данными|Байты|Среднее|Место, занятое данными|База данныхРесурсИд,ЭластичныйПулРесурсИд|
|dtu_used|DTU used|Count|Среднее|DTU used|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Count|Среднее|Virtual core count (Число виртуальных ядер)|None|
|avg_cpu_percent|Average CPU percentage (Средний процент использования ЦП)|Процент|Среднее|Average CPU percentage (Средний процент использования ЦП)|None|
|reserved_storage_mb|Зарезервированное дисковое пространство|Count|Среднее|Зарезервированное дисковое пространство|None|
|storage_space_used_mb|Использованное дисковое пространство|Count|Среднее|Использованное дисковое пространство|None|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Count|Среднее|IO requests count (Количество запросов ввода-вывода)|None|
|io_bytes_read|Количество считанных байт ввода-вывода|Байты|Среднее|Количество считанных байт ввода-вывода|None|
|io_bytes_written|Количество записанных байт ввода-вывода|Байты|Среднее|Количество записанных байт ввода-вывода|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Count|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|None|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемого индексом ADLS Gen2 (Иерархический) в байтах.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый Хранилищем таблиц учетной записи хранения, в байтах.|None|
|TableCount|Количество таблиц|Count|Среднее|Количество таблиц в Хранилище таблиц учетной записи хранения.|None|
|TableEntityCount|Количество сущностей таблиц|Count|Среднее|Количество сущностей таблиц в Хранилище таблиц учетной записи хранения.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемого службой файлов учетной записи хранилища в байтах.|FileShare|
|FileCount|Количество файлов|Count|Среднее|Количество файлов в службе файла учетной записи хранилища.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Count|Среднее|Количество файлов в службе файла учетной записи хранилища.|None|
|FileShareSnapshotCount|Количество моментальных снимков файла|Count|Среднее|Количество снимков, присутствующих на акции в Службе файлов учетной записи хранилища.|FileShare|
|FileShareSnapshotSize|Размер снимка файла общего обмена|Байты|Среднее|Объем хранилища, используемого снимками в службе файла учетной записи хранилища в байтах.|FileShare|
|FileShareКвота|Размер квоты доли файла|Байты|Среднее|Верхний предел объема хранилища, который может быть использован службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация, FileShare|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName,Аутентификация, FileShare|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName,Аутентификация, FileShare|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName,Аутентификация, FileShare|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName,Аутентификация, FileShare|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName,Аутентификация, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|None|
|QueueCount|Количество очередей|Count|Среднее|Количество очередей в службе очередей учетной записи хранения.|None|
|QueueMessageCount|Количество сообщений очереди|Count|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|None|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType,GeoType,ApiName,Аутентификация|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName, Аутентификация|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName, Аутентификация|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName, Аутентификация|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName, Аутентификация|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName, Аутентификация|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/кэши

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Клиентиопс|Всего клиентов IOPS|Count|Среднее|Скорость операций клиентских файлов, обрабатываемых кэшом.|None|
|КлиентскаяЛапланция|Средняя задержка клиента|Миллисекунды|Среднее|Средняя задержка операций клиентского файла в кэше хранения.|None|
|ClientReadIOPS|Клиент Читать IOPS|Число/с|Среднее|Клиент читает операции в секунду.|None|
|ClientReadThroughput|Средняя пропускная готовность кэша|Байт/с|Среднее|Клиент считывал скорость передачи данных.|None|
|ClientWriteIOPS|Клиент Написать IOPS|Число/с|Среднее|Клиент записывает операции в секунду.|None|
|ClientWriteThroughput|Средняя пропускная запись кэша|Байт/с|Среднее|Клиент пишет скорость передачи данных.|None|
|ClientMetadataReadIOPS|Метаданные клиента Читать IOPS|Число/с|Среднее|Скорость операций клиентского файла, отправляемых в кэш, за исключением считываемых данных, не изменяет постоянное состояние.|None|
|ClientMetadataWriteIOPS|Метаданные клиента пишут IOPS|Число/с|Среднее|Скорость операций клиентского файла, отправляемых в кэш, за исключением записей данных, изменяет постоянное состояние.|None|
|КлиентЛокИОСПС|Клиентская блокировка IOPS|Число/с|Среднее|Операции блокировки файла клиента в секунду.|None|
|ХранениеЦелевоеЗдоровье|Целевое здоровье хранилища|Count|Среднее|Boolean результаты проверки подключения между целями кэша и хранения.|None|
|Время доступности|Время доступности|Count|Среднее|Boolean результаты проверки подключения между кэшом и системой мониторинга.|None|
|ХранениеTargetIOPS|Всего храненияЦелевой IOPS|Count|Среднее|Скорость всех операций файлов, отправляемых кэшу в определенный Target.|ХранениеTarget|
|ХранениеTargetWriteIOPS|ХранениеЦелевой Написать IOPS|Count|Среднее|Скорость записи файла операций кэша отправляет сяртам в определенный StorageTarget.|ХранениеTarget|
|ХранениеTargetAsyncWriteThroughput|ХранениеTarget Асинхронная Пропускная запись|Байт/с|Среднее|Скорость асинхронно записывает данные в определенный DataTarget. Это оппортунистические пишет, что не вызывает клиентов, чтобы заблокировать.|ХранениеTarget|
|StorageTargetSyncWriteThroughput|StorageTarget Синхронная пропускная запись|Байт/с|Среднее|Скорость синхронно записи данных к определенному StorageTarget. Эти записи, которые вызывают клиентов, чтобы заблокировать.|ХранениеTarget|
|ХранениеTargetTotalWriteThroughput|StorageTarget Всего Написать Пропускная часть|Байт/с|Среднее|Общая скорость, которую Кэш записывает данные в определенный Target.|ХранениеTarget|
|ХранениеTargetLatency|Задержка хранилищаTarget|Миллисекунды|Среднее|Средняя задержка в оба конца всех операций файла, отправляемых кэшу в partricular StorageTarget.|ХранениеTarget|
|ХранениеTargetMetadataReadIOPS|ХранениеТархик Метаданные Читать IOPS|Число/с|Среднее|Скорость операций файлов, которые не изменяют постоянное состояние и исключая операцию чтения, которую кэш отправляет в определенный StorageTarget.|ХранениеTarget|
|ХранениеTargetMetadataWriteIOPS|Метаданные StorageTarget пишут IOPS|Число/с|Среднее|Скорость операций файлов, которые изменяют постоянное состояние и исключающие операцию записи, которую кэш отправляет в определенный StorageTarget.|ХранениеTarget|
|ХранениеTargetReadIOPS|ХранениеЦелевой читать IOPS|Число/с|Среднее|Скорость считываемых файлов, отправляемых кейсом в определенный Target.|ХранениеTarget|
|ХранениеTargetReadAheadThroughput|ХранениеTarget Читать впереди пропускной|Байт/с|Среднее|Скорость кэша оппортунистически считывает данные из StorageTarget.|ХранениеTarget|
|ХранениеTargetFillThroughput|StorageTarget Заполнить пропускную емкость|Байт/с|Среднее|Скорость чтения кэша данных из StorageTarget для обработки промаха кэша.|ХранениеTarget|
|ХранениеTargetTotalReadPutPut|StorageTarget Всего Читать Пропускную часть|Байт/с|Среднее|Общая скорость, которую кэш считывает данные из определенного Сайта Хранения.|ХранениеTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Результат сеанса синхронизации|Count|Среднее|Метрика, которая регистрирует значение 1 каждый раз, когда конечная точка сервера успешно завершает сеанс синхронизации с точкой облачного конца|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Синхронизация файлов|Count|Итог|Граф файлов синхронизирован|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionItemОшибкиСчет|Несинхронизирующиеся файлы|Count|Итог|Подсчет файлов не синхронизировался|SyncGroupName,ServerEndpointName,SyncDirection|
|ХранениеSyncbatchTransferredBy|Синхронизировано байт|Байты|Итог|Общий размер файла, передаваемый для сеансов синхронизации|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Состояние сервера онлайн|Count|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер успешно записывает сердцебиение с помощью Cloud Endpoint|ServerName|
|ХранениеSyncRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итог|Общий размер данных, отозванных сервером|ServerName|
|StorageRecalledRecalledTotalNetworkBytes|Размер облачного уровня отзыва|Байты|Итог|размер отозванных данных;|SyncGroupName,ServerName|
|ХранениеSyncrecallThroughputputPerperSecond|Пропускная пропускная часть облачного уровня отзыва|Байт/с|Среднее|Размер пропускной вывехивания данных|SyncGroupName,ServerName|
|StorageSyncRecalledNetworkBy-application|Размер облачного уровня отзыва по приложению|Байты|Итог|Размер данных, отозванных приложением|SyncGroupName,ServerName,Имя приложенияИмя|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Синхронизация файлов|Count|Итог|Граф файлов синхронизирован|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionItemОшибкИСчет|Несинхронизирующиеся файлы|Count|Итог|Подсчет файлов не синхронизировался|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncgroupBatchTransferredBytes|Синхронизировано байт|Байты|Итог|Общий размер файла, передаваемый для сеансов синхронизации|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ServerEndpointSessionSessionAppliedFilesCount|Синхронизация файлов|Count|Итог|Граф файлов синхронизирован|ServerEndpointName,SyncDirection|
|ServerEndpointSessionSessionItemОшибки|Несинхронизирующиеся файлы|Count|Итог|Подсчет файлов не синхронизировался|ServerEndpointName,SyncDirection|
|СерверEndpointBatchTransferredBytes|Синхронизировано байт|Байты|Итог|Общий размер файла, передаваемый для сеансов синхронизации|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|СерверHeartbeat|Состояние сервера онлайн|Count|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер успешно записывает сердцебиение с помощью Cloud Endpoint|ServerResourceId,ServerName|
|ServerRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итог|Общий размер данных, отозванных сервером|ServerResourceId,ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|LogicalName,PartitionId|
|InputEvents|Входные события|Count|Итог|Входные события|LogicalName,PartitionId|
|InputEventBytes|Байты входного события|Байты|Итог|Байты входного события|LogicalName,PartitionId|
|LateInputEvents|События позднего ввода|Count|Итог|События позднего ввода|LogicalName,PartitionId|
|OutputEvents|Выходные события|Count|Итог|Выходные события|LogicalName,PartitionId|
|ConversionErrors|Ошибки преобразования данных|Count|Итог|Ошибки преобразования данных|LogicalName,PartitionId|
|ошибки|Ошибки среды выполнения|Count|Итог|Ошибки среды выполнения|LogicalName,PartitionId|
|DroppedOrAdjustedEvents|Неупорядоченные события|Count|Итог|Неупорядоченные события|LogicalName,PartitionId|
|AMLCalloutRequests|Запросы функций|Count|Итог|Запросы функций|LogicalName,PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Итог|Неудачные запросы функций|LogicalName,PartitionId|
|AMLCalloutInputEvents|События функций|Count|Итог|События функций|LogicalName,PartitionId|
|DeserializationError|Ошибки десериализации входа|Count|Итог|Ошибки десериализации входа|LogicalName,PartitionId|
|EarlyInputEvents|Ранние входные события|Count|Итог|Ранние входные события|LogicalName,PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимальная|Предельная задержка|LogicalName,PartitionId|
|ВхотозентыИсточникиНазад|Необработанные входные события|Count|Максимальная|Необработанные входные события|LogicalName,PartitionId|
|ВводСобытийИсточникИВТорых|Полученные входные источники|Count|Итог|Полученные входные источники|LogicalName,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/рабочие пространства

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|ОркестрацияPipelineRunsEnded|Трубопроводные прогона закончились|Count|Итог|Количество запусков конвейера оркестровки, которые удалось, не удалось, или были отменены|Результат,FailureType,Pipeline|
|ОркестровкаАктивностьRunsEnded|Завершены забеги активности|Count|Итог|Граф оркестровки деятельности, которые удалось, не удалось, или были отменены|Результат,FailureType,Активность,АктивностьТип,Трубопровод|
|ОркестровыеТриггерыЗавершены|Триггеры закончились|Count|Итог|Граф триггеров оркестровки, которые преуспели, потерпели неудачу или были отменены|Результат,НеудачаТип,Триггер|
|СЗЛОнСпросЛогинПопытки|Попытки входа|Count|Итог|Граф попыток входа, которые были успешно выполнены или не увенчались успехом|Результат|
|СЗЛОнСпросЗапросыЗавершено|Запросы закончились|Count|Итог|Подсчет запросов, которые удалось, не удалось, или были отменены|Результат|
|СЗЛОНСпросКЕВириОбработанныеБайты|Обработанные данные|Байты|Итог|Объем данных, обрабатываемых запросами|None|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/рабочие пространства/bigDataPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|SparkJobsEnded|Завершенные приложения|Count|Итог|Граф завершенных заявок|JobType,JobResult|
|Основные возможности|Емкость ядер|Count|Максимальная|Емкость ядер|None|
|MemoryCapacityGB|Емкость памяти (GB)|Count|Максимальная|Емкость памяти (GB)|None|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/рабочие пространства/sqlPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DWULimit|Лимит DWU|Count|Максимальная|Цель уровня обслуживания пула S'L|None|
|DWUUsed|DWU used|Count|Максимальная|Представляет собой высокоуровневое представление использования в пуле S'L. Измеряется по лимиту DWU и проценту DWU|None|
|DWUUsedПроцент|DWU использованный процент|Процент|Максимальная|Представляет собой высокоуровневое представление использования в пуле S'L. Измеряется, принимая максимум между процентом процессора и процентом io данных|None|
|СоединенияЗаблокированыПожарные|Соединения, заблокированные брандмауэром|Count|Итог|Количество соединений, заблокированных правилами брандмауэра. Пересмотреть политики управления доступом для пула S'L и контролировать эти соединения, если количество|None|
|АдаптивныйCacheHitPercent|Процент адаптивного кэша|Процент|Максимальная|Измеряет, насколько хорошо рабочие нагрузки используют адаптивный кэш. Используйте эту метрику с процентной метрикой кэша, чтобы определить, следует ли масштабировать для дополнительной емкости или перезапускать рабочие нагрузки для гидратации кэша|None|
|АдаптивныйCacheUsedПроцент|Используемый процент адаптивного кэша|Процент|Максимальная|Измеряет, насколько хорошо рабочие нагрузки используют адаптивный кэш. Используйте эту метрику с кэшом, используемым в процентном порядке, чтобы определить, следует ли масштабировать для дополнительной емкости или перезапускать рабочие нагрузки для гидратации кэша|None|
|МестноеTempDBUsedПроцент|Местный темпб используется процент|Процент|Максимальная|Локальное использование tempdb во всех вычислительных узлах - значения испускаются каждые пять минут|None|
|MemoryUsedПроцент|Процент использованной памяти|Процент|Максимальная|Использование памяти во всех узлах в пуле S'L|None|
|Соединения|Соединения|Count|Итог|Количество входов в пул S'L|Результат|
|WLGActiveЗапросы|Активные запросы группы рабочей нагрузки|Count|Итог|Активные запросы в группе рабочей нагрузки. Использование этой метрики нефильтрованное и неразделенное отображает все активные запросы, работающие в системе|IsUserDefined, Рабочая группа|
|WLGActiveЗапросыTimeouts|Тайм-ауты группы рабочей нагрузки|Count|Итог|Запросы для группы рабочей нагрузки, которые приурочены. Тайм-ауты запросов, о которых сообщает эта метрика, — это только после того, как запрос начал выполнять (он не включает время ожидания из-за блокировки или ожидания ресурса)|IsUserDefined, Рабочая группа|
|WLGAllocationbySystemПроцент|Распределение группрабочей рабочей нагрузки по системным процентам|Процент|Максимальная|Процентное распределение ресурсов по отношению ко всей системе|IsUserDefined, Рабочая группа|
|WLGAllocationbyMaxResourceПроцент|Распределение группы рабочей нагрузки на максимальный процент ресурсов|Процент|Максимальная|Отображает процентное распределение ресурсов по отношению к проценту ресурсов эффективного ограничения на рабочую группу. Этот показатель обеспечивает эффективное использование рабочей группы|IsUserDefined, Рабочая группа|
|WLGEffectiveCapResourcePercent|Эффективный процент ресурсов крышки|Процент|Максимальная|Эффективный процент ресурсов ограничения для рабочей группы. Если есть другие группы рабочей нагрузки с min_percentage_resource > 0, effective_cap_percentage_resource снижается пропорционально|IsUserDefined, Рабочая группа|
|wlg_effective_min_resource_percent|Эффективный мин-процент ресурсов|Процент|Минимальные|Эффективная настройка процентного процента мин-ресурсов позволила учитывать уровень обслуживания и параметры рабочей группы. Эффективная min_percentage_resource может быть скорректирована выше на более низких уровнях обслуживания|IsUserDefined, Рабочая группа|
|WLGКейлид-запросы|Запросы группрабочей рабочей нагрузки в очереди|Count|Итог|Совокупный подсчет запросов в очереди после достижения максимального лимита параллелизма|IsUserDefined, Рабочая группа|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итог|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|None|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итог|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|None|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итог|Количество байтов, полученных от всех источников событий|None|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итог|Общий размер событий, успешно обработанных и доступных для запросов|None|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итог|Количество сведенных событий, успешно обработанных и доступных для запросов|None|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|None|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Среднее|Разница между числом последовательности последнего enqueued-сообщения в разделении источника событий и числом сообщений, обрабатываемых в Ingress|None|
|ТеплыйХранениеМаксСвойства|Теплый Хранения Макс Свойства|Count|Максимальная|Максимальное количество свойств, используемых, разрешенных окружающей средой для S1/S2 SKU, и максимальное количество свойств, разрешенных Теплым Магазином для PAYG SKU|None|
|ТеплыйХранениеСвойства|Теплые используемые свойства хранения |Count|Максимальная|Количество свойств, используемых средой для S1/S2 SKU, и количество свойств, используемых Теплым Магазином для PAYG SKU|None|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итог|Количество сообщений, считанных из источника событий|None|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итог|Количество недопустимых сообщений, считанных из источника событий|None|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итог|Количество байтов, считанных из источника событий|None|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итог|Общий размер событий, успешно обработанных и доступных для запросов|None|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итог|Количество сведенных событий, успешно обработанных и доступных для запросов|None|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|None|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Среднее|Разница между числом последовательности последнего enqueued-сообщения в разделении источника событий и числом сообщений, обрабатываемых в Ingress|None|
|ТеплыйХранениеМаксСвойства|Теплый Хранения Макс Свойства|Count|Максимальная|Максимальное количество свойств, используемых, разрешенных окружающей средой для S1/S2 SKU, и максимальное количество свойств, разрешенных Теплым Магазином для PAYG SKU|None|
|ТеплыйХранениеСвойства|Теплые используемые свойства хранения |Count|Максимальная|Количество свойств, используемых средой для S1/S2 SKU, и количество свойств, используемых Теплым Магазином для PAYG SKU|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Байт/с|Среднее|Средняя пропускная плата диска за счет считываемых операций в течение периода выборки.|None|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Байт/с|Среднее|Средняя пропускная запись диска за счет операций записи в течение периода выборки.|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Общая пропускная их пропускная готовность за счет считываемых операций в течение периода выборки.|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Общая пропускная запись диска за счет операций записи в течение периода выборки.|None|
|DiskReadOperations|Операции чтения дисков|Count|Итог|Количество операций считываемого итоговым данным в предыдущем периоде выборки. Обратите внимание, что эти операции могут быть переменного размера.|None|
|DiskWriteОперации|Операции записи диска|Count|Итог|Количество операций записи io в предыдущем периоде выборки. Обратите внимание, что эти операции могут быть переменного размера.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Среднее число операций считываемого io в предыдущем периоде выборки. Обратите внимание, что эти операции могут быть переменного размера.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Среднее число операций записи io записи в предыдущем периоде выборки. Обратите внимание, что эти операции могут быть переменного размера.|None|
|DiskReadLatency|Задержка чтения с диска|Миллисекунды|Среднее|Общая задержка чтения. Сумма устройства и ядра считывается в просрили.|None|
|DiskWriteLatency|Задержка записи на диск|Миллисекунды|Среднее|Полная задержка записи. Сумма устройства и ядра пишут опоздания.|None|
|NetworkInBytesPerSecond|Сеть в байтах/Секе|Байт/с|Среднее|Средняя пропускная плата сети для полученного трафика.|None|
|NetworkOutBytesPerSecond|Сеть out Байты/Sec|Байт/с|Среднее|Средняя пропускная плата сети для передаваемого трафика.|None|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Общая пропускная их часть сети для полученного трафика.|None|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Общая пропускная информация сети для передаваемого трафика.|None|
|MemoryUsed|Используемая память|Байты|Среднее|Количество памяти машины, которая используется VM.|None|
|ПамятьПредоставленная|Память предоставлена|Байты|Среднее|Сумма памяти, предоставленная VM хостом. Память не предоставляется хосте, пока она не коснулась один раз и предоставленная память может быть заменена или раздувается, если VMkernel нуждается в памяти.|None|
|MemoryActive|Память Активная|Байты|Среднее|Объем памяти, используемой VM в прошлом небольшое окно времени. Это "истинное" число того, сколько памяти VM в настоящее время нуждается. Дополнительная неиспользованная память может быть заменена или раздута без влияния на производительность гостя.|None|
|Percentage CPU|Percentage CPU|Процент|Среднее|Использование процессора. Это значение сообщается со 100% представляющих все процессорные ядра в системе. Например, двухсторонний VM, использующий 50% четырехъядерной системы, полностью использует два ядра.|None|
|ПроцентCpuReady|Процентный cPU Готов|Миллисекунды|Итог|Готовность времени — это время, затрагивающее ожидание того, что процессор (ы) станет доступным в прошлом интервале обновления.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Count|Среднее|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Среднее|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|TcpSynSent|TCP Син отправлено|Count|Среднее|TCP Син отправлено|Экземпляр|
|TcpSynReceived|TCP Syn Получено|Count|Среднее|TCP Syn Получено|Экземпляр|
|TcpУстановлено|TCP Создан|Count|Среднее|TCP Создан|Экземпляр|
|TcpFinWait1|TCP Фин Ожидание 1|Count|Среднее|TCP Фин Ожидание 1|Экземпляр|
|TcpFinWait2|TCP Фин Ожидание 2|Count|Среднее|TCP Фин Ожидание 2|Экземпляр|
|TcpClosing|Закрытие TCP|Count|Среднее|Закрытие TCP|Экземпляр|
|TcpCloseWait|TCP Закрыть Ожидание|Count|Среднее|TCP Закрыть Ожидание|Экземпляр|
|TcpLastAck|TCP Последний Ack|Count|Среднее|TCP Последний Ack|Экземпляр|
|TcpTimeWait|TCP Время Ожидание|Count|Среднее|TCP Время Ожидание|Экземпляр|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

> [!NOTE]
> **Использование файловой системы** является новой метрикой, развернутой по всему миру, никаких данных не ожидается, если вы не попали в белый список для частного предварительного просмотра.

> [!IMPORTANT]
> **Среднее время отклика** будет униточено, чтобы избежать путаницы с метрическими агрегациями. Используйте **время отклика** в качестве замены.

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итог|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Итог|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итог|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итог|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итог|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итог|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итог|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итог|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итог|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итог|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итог|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|ResponseTime|Время ответа|Секунды|Итог|Время ответа|Экземпляр|
|AverageResponseTime|Среднее время отклика (унипражено)|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|AppConnections|Соединения|Count|Среднее|Соединения|Экземпляр|
|Маркеры|Счетчик дескрипторов|Count|Среднее|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итог|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итог|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итог|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итог|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итог|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итог|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Итог|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Итог|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Итог|Сборок мусора поколения 2|Экземпляр|
|HealthCheckStatus|Состояние проверки работоспособности|Count|Среднее|Состояние проверки работоспособности|Экземпляр|
|FileSystemUsage|Использование файловой системы|Байты|Среднее|Использование файловой системы|None|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итог|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Итог|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итог|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итог|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итог|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итог|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итог|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итог|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итог|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итог|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итог|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|HttpResponseTime|Время ответа|Секунды|Среднее|Время ответа|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Count|Итог|Function Execution Units|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Итог|Function Execution Count|Экземпляр|
|AppConnections|Соединения|Count|Среднее|Соединения|Экземпляр|
|Маркеры|Счетчик дескрипторов|Count|Среднее|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Итог|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Итог|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Итог|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Итог|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Итог|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Итог|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Count|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Count|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Count|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Count|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Count|Итог|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Count|Итог|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Count|Итог|Сборок мусора поколения 2|Экземпляр|
|HealthCheckStatus|Состояние проверки работоспособности|Count|Среднее|Состояние проверки работоспособности|Экземпляр|
|FileSystemUsage|Использование файловой системы|Байты|Среднее|Использование файловой системы|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|Requests|Requests|Count|Итог|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Итог|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Итог|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Итог|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Итог|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Итог|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Итог|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Итог|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Итог|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итог|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Count|Среднее|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Среднее|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Count|Итог|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Среднее|Общее число внешних интерфейсов|None|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Count|Среднее|Рабочие роли плана службы приложений уровня "Малый"|None|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Среднее|Рабочие процессы плана службы приложений уровня "Средний"|None|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Count|Среднее|Рабочие процессы плана службы приложений уровня "Крупный"|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Count|Среднее|Общее количество рабочих процессов|None|
|WorkersAvailable|Доступные рабочие процессы|Count|Среднее|Доступные рабочие процессы|None|
|WorkersUsed|Использованные рабочие процессы|Count|Среднее|Использованные рабочие процессы|None|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
## <a name="next-steps"></a>Дальнейшие действия
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создание оповещений на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](platform-logs-overview.md)
