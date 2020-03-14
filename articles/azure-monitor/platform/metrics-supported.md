---
title: Поддерживаемые метрики Azure Monitor, доступные для каждого типа ресурса
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275390"
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor

Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor. Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Метрики упорядочены по пространствам имен. Список служб и пространств имен, принадлежащих им, см. в статье [поставщики ресурсов для служб Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Чтобы запросить данные метрик и получить к ним доступ программно, используйте [версию api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) .

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
> Список метрик платформы, экспортируемых с помощью параметров диагностики, см. в [этой статье](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Число|Среднее|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Среднее|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|private_bytes_metric|Байты исключительного пользования|Байты|Среднее|Байтов исключительного назначения.|ServerResourceType|
|virtual_bytes_metric|Байт виртуальной памяти|Байты|Среднее|Число виртуальных байтов.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Число|Среднее|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Среднее|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Число|Среднее|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Число|Среднее|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Число|Среднее|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Число|Среднее|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Число|Среднее|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: текущие подключения|Число|Среднее|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Число|Среднее|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: использование памяти|Байты|Среднее|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Среднее|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Среднее|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Среднее|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Среднее|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Квота|Память: квота|Байты|Среднее|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Число|Среднее|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Среднее|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Среднее|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Среднее|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Среднее|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Число|Среднее|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Число|Среднее|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Число|Среднее|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Число|Среднее|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Число|Среднее|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Число|Среднее|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Число|Среднее|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Число|Среднее|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Число|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Число|Среднее|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Число|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Число|Среднее|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Число|Среднее|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Число|Среднее|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Число|Среднее|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Число|Среднее|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Среднее|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_private_bytes_metric|Байт исключительного числа модулей M|Байты|Среднее|Использование байтов исключительного пользования процессом гибридного механизма.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Виртуальный байт модуля M|Байты|Среднее|Использование виртуальной памяти процессами гибридного механизма.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Всего запросов к шлюзу (устарело)|Число|Всего|Число запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с помощью измерения Гатевайреспонсекодекатегори.|Расположение, имя узла|
|SuccessfulRequests|Успешные запросы к шлюзу (не рекомендуется)|Число|Всего|Число успешных запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу (устарели)|Число|Всего|Число неавторизованных запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|FailedRequests|Неудачные запросы к шлюзу (не рекомендуется)|Число|Всего|Число сбоев в запросах к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|OtherRequests|Другие запросы к шлюзу (не рекомендуется)|Число|Всего|Число других запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|Duration|Общая длительность запросов шлюза|Миллисекунд|Среднее|Общая длительность запросов шлюза в миллисекундах|Расположение, имя узла|
|баккенддуратион|Длительность внутренних запросов|Миллисекунд|Среднее|Длительность внутренних запросов в миллисекундах|Расположение, имя узла|
|Capacity|Capacity|Процент|Среднее|Метрика использования для службы ApiManagement|Location|
|евенсубтоталевентс|Всего событий EventHub|Число|Всего|Число событий, отправленных в EventHub|Location|
|евенсубсукцессфулевентс|Успешные события EventHub|Число|Всего|Число успешных событий EventHub|Location|
|евенсубтоталфаиледевентс|События EventHub, завершившиеся сбоем|Число|Всего|Число неудачных событий EventHub|Location|
|евенсубрежектедевентс|Отклоненные события EventHub|Число|Всего|Число отклоненных событий EventHub (неправильная конфигурация или несанкционированный доступ)|Location|
|евенсубсроттледевентс|Регулируемые события EventHub|Число|Всего|Число регулируемых событий EventHub|Location|
|евенсубтимедаутевентс|Истекло время ожидания событий EventHub|Число|Всего|Число событий с превышением времени ожидания EventHub|Location|
|евенсубдроппедевентс|Удалены события EventHub|Число|Всего|Число пропущенных событий из-за ограничения размера очереди|Location|
|евенсубтоталбитессент|Размер событий EventHub|Байты|Всего|Общий размер событий EventHub в байтах|Location|
|Requests|Requests|Число|Всего|Метрики запросов шлюза с несколькими измерениями|Расположение, имя узла, Ластеррорреасон, Баккендреспонсекоде, Гатевайреспонсекоде, Баккендреспонсекодекатегори, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. Аппконфигуратион/Конфигуратионсторес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|хттпинкомингрекуесткаунт|хттпинкомингрекуесткаунт|Число|Число|Общее число входящих HTTP-запросов.|None|
|фаиледхттпрекуесткаунт|фаиледхттпрекуесткаунт|Число|Число|Неудачные HTTP-запросы.|None|
|хттпинкомингрекуестдуратион|хттпинкомингрекуестдуратион|Число|Среднее|Задержка HTTP-запроса.|None|


## <a name="microsoftappplatformspring"></a>Microsoft. Аппплатформ/пружина

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|системкпуусажеперцентаже|Процент использования процессора системой|Процент|Среднее|Последняя загрузка ЦП для всей системы|AppName, Pod|
|аппкпуусажеперцентаже|Процент использования ЦП приложением|Процент|Среднее|Процент использования ЦП приложением ВИРТУАЛЬНОЙ машины Java|AppName, Pod|
|аппмеморикоммиттед|Назначенная память приложения|Байты|Среднее|Память, назначенная ВИРТУАЛЬНОЙ машины Java в байтах|AppName, Pod|
|аппмеморюсед|Используемая память приложения|Байты|Среднее|Используемая память приложения в байтах|AppName, Pod|
|аппмеморимакс|Максимальная память приложения|Байты|Максимальная|Максимальный объем памяти в байтах, который может использоваться для управления памятью|AppName, Pod|
|максолдженмеморипулбитес|Максимальный доступный размер данных старого поколения|Байты|Среднее|Максимальный размер пула памяти старого поколения|AppName, Pod|
|олдженмеморипулбитес|Размер данных старого поколения|Байты|Среднее|Размер пула памяти старого поколения после полного GC|AppName, Pod|
|олдженпромотедбитес|Повысить до старого размера данных поколения|Байты|Максимальная|Число положительных увеличений размера пула памяти старого поколения до сборки мусора в после сборки мусора|AppName, Pod|
|йоунгженпромотедбитес|Превращение в размер данных создания Титов.|Байты|Максимальная|Увеличено для увеличения размера пула памяти создания Титов после одного сборщика мусора до следующего|AppName, Pod|
|гкпаусетоталкаунт|Счетчик приостановки GC|Число|Всего|Счетчик приостановки GC|AppName, Pod|
|гкпаусетоталтиме|Общее время приостановки сборки мусора|Миллисекунд|Всего|Общее время приостановки сборки мусора|AppName, Pod|
|томкатсентбитес|Всего отправленных байтов Tomcat|Байты|Всего|Всего отправленных байтов Tomcat|AppName, Pod|
|томкатрецеиведбитес|Всего получено байт в Tomcat|Байты|Всего|Всего получено байт в Tomcat|AppName, Pod|
|томкатрекуесттоталтиме|Общее время запроса Tomcat|Миллисекунд|Всего|Общее время запроса Tomcat|AppName, Pod|
|томкатрекуесттоталкаунт|Общее число запросов Tomcat|Число|Всего|Общее число запросов Tomcat|AppName, Pod|
|томкатреспонсеавгтиме|Среднее время запроса Tomcat|Миллисекунд|Среднее|Среднее время запроса Tomcat|AppName, Pod|
|томкатрекуестмакстиме|Максимальное время запроса Tomcat|Миллисекунд|Максимальная|Максимальное время запроса Tomcat|AppName, Pod|
|томкатерроркаунт|Глобальная ошибка Tomcat|Число|Всего|Глобальная ошибка Tomcat|AppName, Pod|
|томкатсессионактивемакскаунт|Максимальное число активных сеансов Tomcat|Число|Всего|Максимальное число активных сеансов Tomcat|AppName, Pod|
|томкатсессионаливемакстиме|Максимальное время активности сеанса Tomcat|Миллисекунд|Максимальная|Максимальное время активности сеанса Tomcat|AppName, Pod|
|томкатсессионактивекурренткаунт|Счетчик активности сеансов Tomcat|Число|Всего|Счетчик активности сеансов Tomcat|AppName, Pod|
|томкатсессионкреатедкаунт|Число созданных сеансов Tomcat|Число|Всего|Число созданных сеансов Tomcat|AppName, Pod|
|томкатсессионекспиредкаунт|Счетчик с истекшим сроком действия сеанса Tomcat|Число|Всего|Счетчик с истекшим сроком действия сеанса Tomcat|AppName, Pod|
|томкатсессионрежектедкаунт|Число отклоненных сеансов Tomcat|Число|Всего|Число отклоненных сеансов Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Число|Всего|Общее количество заданий|Runbook, состояние|
|тоталупдатедеплойментрунс|Всего запусков развертывания обновлений|Число|Всего|Всего запусков развертывания обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние|
|тоталупдатедеплойментмачинерунс|Всего запусков компьютера для развертывания обновлений|Число|Всего|Всего компьютеров, на которых выполняется развертывание обновлений программного обеспечения, выполняются при развертывании обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Число|Всего|Общее число выделенных ядер в учетной записи пакетной службы.|None|
|TotalNodeCount|Dedicated Node Count|Число|Всего|Общее число выделенных узлов в учетной записи пакетной службы.|None|
|LowPriorityCoreCount|LowPriority Core Count|Число|Всего|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|None|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Число|Всего|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|None|
|CreatingNodeCount|Creating Node Count|Число|Всего|Количество создаваемых узлов.|None|
|StartingNodeCount|Starting Node Count|Число|Всего|Число узлов, которые запускаются.|None|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Число|Всего|Число узлов, ожидающих завершения задачи запуска.|None|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Число|Всего|Число узлов, на которых произошел сбой задачи запуска.|None|
|IdleNodeCount|Idle Node Count|Число|Всего|Количество узлов в неактивном состоянии.|None|
|OfflineNodeCount|Offline Node Count|Число|Всего|Количество узлов не в сети.|None|
|RebootingNodeCount|Rebooting Node Count|Число|Всего|Количество перезапускаемых узлов.|None|
|ReimagingNodeCount|Reimaging Node Count|Число|Всего|Число узлов, для которых пересоздается образ.|None|
|RunningNodeCount|Running Node Count|Число|Всего|Число работающих узлов.|None|
|LeavingPoolNodeCount|Leaving Pool Node Count|Число|Всего|Количество узлов, покидающих пул.|None|
|UnusableNodeCount|Unusable Node Count|Число|Всего|Число узлов, непригодных для использования.|None|
|PreemptedNodeCount|Preempted Node Count|Число|Всего|Количество замещенных узлов.|None|
|TaskStartEvent|Task Start Events|Число|Всего|Общее число запущенных задач.|None|
|TaskCompleteEvent|Task Complete Events|Число|Всего|Общее число завершенных задач.|None|
|TaskFailEvent|Task Fail Events|Число|Всего|Общее число задач, завершившихся сбоем.|None|
|PoolCreateEvent|Pool Create Events|Число|Всего|Общее число созданных пулов.|None|
|PoolResizeStartEvent|Pool Resize Start Events|Число|Всего|Общее число запущенных задач изменения размера пула.|None|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Число|Всего|Общее число завершенных задач изменения размера пула.|None|
|PoolDeleteStartEvent|Pool Delete Start Events|Число|Всего|Общее число запущенных задач удаления пула.|None|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Число|Всего|Общее число завершенных задач удаления пула.|None|
|JobDeleteCompleteEvent|Job Delete Complete Events (События окончания удаления задания)|Число|Всего|Общее количество заданий, которые были успешно удалены.|None|
|JobDeleteStartEvent|Job Delete Start Events (События начала удаления задания)|Число|Всего|Общее количество заданий, для которых запрошено удаление.|None|
|JobDisableCompleteEvent|Job Disable Complete Events (События окончания отключения задания)|Число|Всего|Общее количество заданий, которые были успешно отключены.|None|
|JobDisableStartEvent|Job Disable Start Events (События начала отключения задания)|Число|Всего|Общее количество заданий, для которых запрошено отключение.|None|
|JobStartEvent|Job Start Events (События запуска задания)|Число|Всего|Общее количество заданий, которые были успешно запущены.|None|
|JobTerminateCompleteEvent|Job Terminate Complete Events (События окончания завершения задания)|Число|Всего|Общее количество заданий, которые были успешно завершены.|None|
|JobTerminateStartEvent|Job Terminate Start Events (События начала завершения задания)|Число|Всего|Общее количество заданий, для которых запрошено завершение.|None|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/рабочие области

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Задание Отправлено|Задание Отправлено|Число|Всего|Число отправленных заданий|Сценарий, имя_кластера|
|Задание выполнено|Задание выполнено|Число|Всего|Число завершенных заданий|Сценарий, имя_кластера, ResultType|
|Всего узлов|Всего узлов|Число|Среднее|Общее число узлов|Сценарий, имя_кластера|
|Активные узлы|Активные узлы|Число|Среднее|Число работающих узлов.|Сценарий, имя_кластера|
|Бездействующие узлы|Бездействующие узлы|Число|Среднее|Количество узлов в неактивном состоянии.|Сценарий, имя_кластера|
|Неиспользуемые узлы|Неиспользуемые узлы|Число|Среднее|Число узлов, непригодных для использования.|Сценарий, имя_кластера|
|Прерванные узлы|Прерванные узлы|Число|Среднее|Количество замещенных узлов.|Сценарий, имя_кластера|
|Покинуть узлы|Покинуть узлы|Число|Среднее|Число покидает узлов|Сценарий, имя_кластера|
|Всего ядер|Всего ядер|Число|Среднее|Общее число ядер|Сценарий, имя_кластера|
|Активные ядра|Активные ядра|Число|Среднее|Число активных ядер|Сценарий, имя_кластера|
|Бездействующие ядра|Бездействующие ядра|Число|Среднее|Число бездействующих ядер|Сценарий, имя_кластера|
|Непригодные для использования ядра|Непригодные для использования ядра|Число|Среднее|Число неиспользуемых ядер|Сценарий, имя_кластера|
|Вытесненные ядра|Вытесненные ядра|Число|Среднее|Число ядер с вытеснением|Сценарий, имя_кластера|
|Освобождение ядер|Освобождение ядер|Число|Среднее|Число покидает ядер|Сценарий, имя_кластера|
|Процент использования квоты|Процент использования квоты|Число|Среднее|Процент использования квоты|Сценарий, имя_кластера, Вмфамилинаме, Вмприорити|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Блокчейн/Блоккчаинмемберс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|кпуусажеперцентажеиндаубле|Процент использования ЦП|Процент|Максимальная|Процент использования ЦП|Узел|
|MemoryUsage|Использование памяти|Байты|Среднее|Использование памяти|Узел|
|MemoryLimit|Ограничение памяти|Байты|Среднее|Ограничение памяти|Узел|
|меморюсажеперцентажеиндаубле|Процент использования памяти|Процент|Среднее|Процент использования памяти|Узел|
|сторажеусаже|Использование хранилища|Байты|Среднее|Использование хранилища|Узел|
|иореадбитес|Считанные байты ввода-вывода|Байты|Всего|Считанные байты ввода-вывода|Узел|
|иовритебитес|Число операций записи ввода-вывода|Байты|Всего|Число операций записи ввода-вывода|Узел|
|коннектионакцептед|Принятые подключения|Число|Всего|Принятые подключения|Узел|
|коннектионхандлед|Обработанные соединения|Число|Всего|Обработанные соединения|Узел|
|коннектионактиве|Активные подключения|Число|Среднее|Активные подключения|Узел|
|рекуессандлед|Обработанные запросы|Число|Всего|Обработанные запросы|Узел|
|процесседблоккс|Обработанные блоки|Число|Всего|Обработанные блоки|Узел|
|процесседтрансактионс|Обработанные транзакции|Число|Всего|Обработанные транзакции|Узел|
|пендингтрансактионс|Ожидающие транзакции|Число|Среднее|Ожидающие транзакции|Узел|
|куеуедтрансактионс|Транзакции в очереди|Число|Среднее|Транзакции в очереди|Узел|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Число|Максимальная||ShardId|
|totalcommandsprocessed|Всего операций|Число|Всего||ShardId|
|cachehits|Попаданий в кэш|Число|Всего||ShardId|
|cachemisses|Промахов в кэше|Число|Всего||ShardId|
|качемиссрате|Частота промахов кэша|Процент|качемиссрате||ShardId|
|getcommands|Операций считывания|Число|Всего||ShardId|
|setcommands|Наборы|Число|Всего||ShardId|
|operationsPerSecond|Количество операций в секунду|Число|Максимальная||ShardId|
|evictedkeys|Исключенные ключи|Число|Всего||ShardId|
|totalkeys|Всего ключей|Число|Максимальная||ShardId|
|expiredkeys|Ключи с истекшим сроком действия|Число|Всего||ShardId|
|usedmemory|Объем используемой памяти|Байты|Максимальная||ShardId|
|usedmemorypercentage|Процент используемой памяти|Процент|Максимальная||ShardId|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||ShardId|
|serverLoad|Загрузка сервера|Процент|Максимальная||ShardId|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||ShardId|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||ShardId|
|percentProcessorTime|ЦП|Процент|Максимальная||ShardId|
|cacheLatency|Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))|Число|Среднее||ShardId|
|ошибки|ошибки|Число|Максимальная||Шардид, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Число|Максимальная||None|
|totalcommandsprocessed0|Total Operations (Shard 0)|Число|Всего||None|
|cachehits0|Cache Hits (Shard 0)|Число|Всего||None|
|cachemisses0|Cache Misses (Shard 0)|Число|Всего||None|
|getcommands0|Gets (Shard 0)|Число|Всего||None|
|setcommands0|Sets (Shard 0)|Число|Всего||None|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Число|Максимальная||None|
|evictedkeys0|Evicted Keys (Shard 0)|Число|Всего||None|
|totalkeys0|Total Keys (Shard 0)|Число|Максимальная||None|
|expiredkeys0|Expired Keys (Shard 0)|Число|Всего||None|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||None|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||None|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||None|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||None|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||None|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||None|
|connectedclients1|Connected Clients (Shard 1)|Число|Максимальная||None|
|totalcommandsprocessed1|Total Operations (Shard 1)|Число|Всего||None|
|cachehits1|Cache Hits (Shard 1)|Число|Всего||None|
|cachemisses1|Cache Misses (Shard 1)|Число|Всего||None|
|getcommands1|Gets (Shard 1)|Число|Всего||None|
|getcommands1|Sets (Shard 1)|Число|Всего||None|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Число|Максимальная||None|
|evictedkeys1|Evicted Keys (Shard 1)|Число|Всего||None|
|totalkeys1|Total Keys (Shard 1)|Число|Максимальная||None|
|expiredkeys1|Expired Keys (Shard 1)|Число|Всего||None|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||None|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||None|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||None|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||None|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||None|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||None|
|connectedclients2|Connected Clients (Shard 2)|Число|Максимальная||None|
|totalcommandsprocessed2|Total Operations (Shard 2)|Число|Всего||None|
|cachehits2|Cache Hits (Shard 2)|Число|Всего||None|
|cachemisses2|Cache Misses (Shard 2)|Число|Всего||None|
|getcommands2|Gets (Shard 2)|Число|Всего||None|
|getcommands2|Sets (Shard 2)|Число|Всего||None|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Число|Максимальная||None|
|evictedkeys2|Evicted Keys (Shard 2)|Число|Всего||None|
|totalkeys2|Total Keys (Shard 2)|Число|Максимальная||None|
|expiredkeys2|Expired Keys (Shard 2)|Число|Всего||None|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||None|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||None|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||None|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||None|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||None|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||None|
|connectedclients3|Connected Clients (Shard 3)|Число|Максимальная||None|
|totalcommandsprocessed3|Total Operations (Shard 3)|Число|Всего||None|
|cachehits3|Cache Hits (Shard 3)|Число|Всего||None|
|cachemisses3|Cache Misses (Shard 3)|Число|Всего||None|
|getcommands3|Gets (Shard 3)|Число|Всего||None|
|setcommands3|Sets (Shard 3)|Число|Всего||None|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Число|Максимальная||None|
|evictedkeys3|Evicted Keys (Shard 3)|Число|Всего||None|
|totalkeys3|Total Keys (Shard 3)|Число|Максимальная||None|
|expiredkeys3|Expired Keys (Shard 3)|Число|Всего||None|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||None|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||None|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||None|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||None|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||None|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||None|
|connectedclients4|Connected Clients (Shard 4)|Число|Максимальная||None|
|totalcommandsprocessed4|Total Operations (Shard 4)|Число|Всего||None|
|cachehits4|Cache Hits (Shard 4)|Число|Всего||None|
|cachemisses4|Cache Misses (Shard 4)|Число|Всего||None|
|getcommands4|Gets (Shard 4)|Число|Всего||None|
|setcommands4|Sets (Shard 4)|Число|Всего||None|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Число|Максимальная||None|
|evictedkeys4|Evicted Keys (Shard 4)|Число|Всего||None|
|totalkeys4|Total Keys (Shard 4)|Число|Максимальная||None|
|expiredkeys4|Expired Keys (Shard 4)|Число|Всего||None|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||None|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||None|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||None|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||None|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||None|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||None|
|connectedclients5|Connected Clients (Shard 5)|Число|Максимальная||None|
|totalcommandsprocessed5|Total Operations (Shard 5)|Число|Всего||None|
|cachehits5|Cache Hits (Shard 5)|Число|Всего||None|
|cachemisses5|Cache Misses (Shard 5)|Число|Всего||None|
|getcommands5|Gets (Shard 5)|Число|Всего||None|
|getcommands5|Sets (Shard 5)|Число|Всего||None|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Число|Максимальная||None|
|evictedkeys5|Evicted Keys (Shard 5)|Число|Всего||None|
|totalkeys5|Total Keys (Shard 5)|Число|Максимальная||None|
|expiredkeys5|Expired Keys (Shard 5)|Число|Всего||None|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||None|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||None|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||None|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||None|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||None|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||None|
|connectedclients6|Connected Clients (Shard 6)|Число|Максимальная||None|
|totalcommandsprocessed6|Total Operations (Shard 6)|Число|Всего||None|
|cachehits6|Cache Hits (Shard 6)|Число|Всего||None|
|cachemisses6|Cache Misses (Shard 6)|Число|Всего||None|
|getcommands6|Gets (Shard 6)|Число|Всего||None|
|setcommands6|Sets (Shard 6)|Число|Всего||None|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Число|Максимальная||None|
|evictedkeys6|Evicted Keys (Shard 6)|Число|Всего||None|
|totalkeys6|Total Keys (Shard 6)|Число|Максимальная||None|
|expiredkeys6|Expired Keys (Shard 6)|Число|Всего||None|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||None|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||None|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||None|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||None|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||None|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||None|
|connectedclients7|Connected Clients (Shard 7)|Число|Максимальная||None|
|totalcommandsprocessed7|Total Operations (Shard 7)|Число|Всего||None|
|cachehits7|Cache Hits (Shard 7)|Число|Всего||None|
|cachemisses7|Cache Misses (Shard 7)|Число|Всего||None|
|getcommands7|Gets (Shard 7)|Число|Всего||None|
|setcommands7|Sets (Shard 7)|Число|Всего||None|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Число|Максимальная||None|
|evictedkeys7|Evicted Keys (Shard 7)|Число|Всего||None|
|totalkeys7|Total Keys (Shard 7)|Число|Максимальная||None|
|expiredkeys7|Expired Keys (Shard 7)|Число|Всего||None|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||None|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||None|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||None|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||None|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||None|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||None|
|connectedclients8|Connected Clients (Shard 8)|Число|Максимальная||None|
|totalcommandsprocessed8|Total Operations (Shard 8)|Число|Всего||None|
|cachehits8|Cache Hits (Shard 8)|Число|Всего||None|
|cachemisses8|Cache Misses (Shard 8)|Число|Всего||None|
|getcommands8|Gets (Shard 8)|Число|Всего||None|
|setcommands8|Sets (Shard 8)|Число|Всего||None|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Число|Максимальная||None|
|evictedkeys8|Evicted Keys (Shard 8)|Число|Всего||None|
|totalkeys8|Total Keys (Shard 8)|Число|Максимальная||None|
|expiredkeys8|Expired Keys (Shard 8)|Число|Всего||None|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||None|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||None|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||None|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||None|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||None|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||None|
|connectedclients9|Connected Clients (Shard 9)|Число|Максимальная||None|
|totalcommandsprocessed9|Total Operations (Shard 9)|Число|Всего||None|
|cachehits9|Cache Hits (Shard 9)|Число|Всего||None|
|cachemisses9|Cache Misses (Shard 9)|Число|Всего||None|
|getcommands9|Gets (Shard 9)|Число|Всего||None|
|setcommands9|Sets (Shard 9)|Число|Всего||None|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Число|Максимальная||None|
|evictedkeys9|Evicted Keys (Shard 9)|Число|Всего||None|
|totalkeys9|Total Keys (Shard 9)|Число|Максимальная||None|
|expiredkeys9|Expired Keys (Shard 9)|Число|Всего||None|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||None|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||None|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||None|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||None|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||None|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/кднвебаппликатионфиреваллполиЦиес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Число|Всего|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, действие|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|None|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. Классикстораже/storageAccounts/Блобсервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Число|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Число|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|None|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемый ADLS 2-го поколения (иерархический) индекс в байтах.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. Классикстораже/storageAccounts/Таблесервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|None|
|TableCount|Количество таблиц|Число|Среднее|Количество таблиц в службе таблиц учетной записи хранения.|None|
|TableEntityCount|Количество сущностей таблиц|Число|Среднее|Количество сущностей таблиц в службе таблиц учетной записи хранения.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. Классикстораже/storageAccounts/Филесервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|FileShare|
|FileCount|Количество файлов|Число|Среднее|Количество файлов в службе файлов учетной записи хранения.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Число|Среднее|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|None|
|филешареснапшоткаунт|Число моментальных снимков общей папки|Число|Среднее|Количество моментальных снимков, имеющихся в службе файлов учетной записи хранения в общей папке.|FileShare|
|филешареснапшотсизе|Размер моментального снимка файлового ресурса|Байты|Среднее|Объем хранилища, используемый моментальными снимками в файловой службе учетной записи хранения в байтах.|FileShare|
|филешарекуота|Размер квоты общей папки|Байты|Среднее|Верхний предел объема хранилища, который может использоваться службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности, Общая папка|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности, Общая папка|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. Классикстораже/storageAccounts/Куеуесервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|None|
|QueueCount|Количество очередей|Число|Среднее|Количество очередей в службе очередей учетной записи хранения.|None|
|QueueMessageCount|Количество сообщений очереди|Число|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Число|Всего|Общее число вызовов.|ApiName, Имя_операции, регион|
|SuccessfulCalls|Successful Calls|Число|Всего|Число успешных вызовов.|ApiName, Имя_операции, регион|
|TotalErrors|Total Errors|Число|Всего|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName, Имя_операции, регион|
|BlockedCalls|Blocked Calls|Число|Всего|Число вызовов, превысивших ограничение скорости или квоты.|ApiName, Имя_операции, регион|
|ServerErrors|Server Errors|Число|Всего|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName, Имя_операции, регион|
|ClientErrors|Client Errors|Число|Всего|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName, Имя_операции, регион|
|DataIn|Входящие данные:|Байты|Всего|Размер входящих данных в байтах.|ApiName, Имя_операции, регион|
|DataOut|Выходные данные|Байты|Всего|Размер исходящих данных в байтах.|ApiName, Имя_операции, регион|
|Задержка|Задержка|MilliSeconds|Среднее|Время задержки в миллисекундах.|ApiName, Имя_операции, регион|
|CharactersTranslated|Преобразованные символы|Число|Всего|Общее количество символов во входящем текстовом запросе.|ApiName, Имя_операции, регион|
|чарактерстраинед|Обученные символы|Число|Всего|Общее количество обученных символов.|ApiName, Имя_операции, регион|
|SpeechSessionDuration|Длительность речи|Секунды|Всего|Общая длительность речи в секундах.|ApiName, Имя_операции, регион|
|TotalTransactions|Всего транзакций|Число|Всего|Общее число транзакций.|None|
|TotalTokenCalls|Всего вызовов токенов|Число|Всего|Общее число вызовов токенов.|ApiName, Имя_операции, регион|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть в оплатах (не рекомендуется)|Байты|Всего|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|None|
|Сеть (исходящий трафик)|Оплата за сеть (не рекомендуется)|Байты|Всего|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Байт считано с диска за период мониторинга|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Всего|Число байтов, записанных на диск в течение периода мониторинга|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Число|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Число|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с) (не рекомендуется)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с (устарело)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (устарело)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду (не рекомендуется)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Длина очереди на диск ОС|Диск ОС длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков ОС|None|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN|
|Операций чтения с диска данных/с|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Операций записи на диск данных в секунду|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Глубина очереди диска данных|Глубина очереди диска данных (Предварительная версия)|Число|Среднее|Длина очереди дисков данных|LUN|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|None|
|Операций чтения с диска ОС в секунду|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Операций записи на диск ОС в секунду|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Глубина очереди диска ОС|Глубина очереди диска ОС (Предварительная версия)|Число|Среднее|Длина очереди дисков ОС|None|
|Входящие потоки|Входящие потоки|Число|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|None|
|Исходящие потоки|Исходящие потоки|Число|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|None|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|None|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|None|
|Попадание в кэш диска данных Premium|Попадание на чтение кэша диска данных Premium (Предварительная версия)|Процент|Среднее|Попадание в кэш диска данных Premium|LUN|
|Промах чтения кэша на диске данных Premium|Промах чтения кэша на диске данных Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске данных Premium|LUN|
|Попадание чтения кэша на диске ОС Premium|Попадание чтения кэша на диске ОС Premium (Предварительная версия)|Процент|Среднее|Попадание чтения кэша на диске ОС Premium|None|
|Промах чтения кэша на диске ОС Premium|Промах чтения кэша диска ОС Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске ОС Premium|None|
|Всего сети|Всего сети|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Общее количество сетевых исходящих|Общее количество сетевых исходящих|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Сеть (входящий трафик)|Сеть в оплатах (не рекомендуется)|Байты|Всего|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|VMName|
|Сеть (исходящий трафик)|Оплата за сеть (не рекомендуется)|Байты|Всего|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|VMName|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Байт считано с диска за период мониторинга|VMName|
|Disk Write Bytes|Disk Write Bytes|Байты|Всего|Число байтов, записанных на диск в течение периода мониторинга|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Число|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Число|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с) (не рекомендуется)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с (устарело)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (устарело)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду (не рекомендуется)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Длина очереди на диск ОС|Диск ОС длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков ОС|None|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN, VMName|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN, VMName|
|Операций чтения с диска данных/с|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Операций записи на диск данных в секунду|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Глубина очереди диска данных|Глубина очереди диска данных (Предварительная версия)|Число|Среднее|Длина очереди дисков данных|LUN, VMName|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|VMName|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|VMName|
|Операций чтения с диска ОС в секунду|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Операций записи на диск ОС в секунду|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Глубина очереди диска ОС|Глубина очереди диска ОС (Предварительная версия)|Число|Среднее|Длина очереди дисков ОС|VMName|
|Входящие потоки|Входящие потоки|Число|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|VMName|
|Исходящие потоки|Исходящие потоки|Число|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|VMName|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|VMName|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|VMName|
|Попадание в кэш диска данных Premium|Попадание на чтение кэша диска данных Premium (Предварительная версия)|Процент|Среднее|Попадание в кэш диска данных Premium|LUN, VMName|
|Промах чтения кэша на диске данных Premium|Промах чтения кэша на диске данных Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске данных Premium|LUN, VMName|
|Попадание чтения кэша на диске ОС Premium|Попадание чтения кэша на диске ОС Premium (Предварительная версия)|Процент|Среднее|Попадание чтения кэша на диске ОС Premium|VMName|
|Промах чтения кэша на диске ОС Premium|Промах чтения кэша диска ОС Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске ОС Premium|VMName|
|Всего сети|Всего сети|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Общее количество сетевых исходящих|Общее количество сетевых исходящих|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|None|
|Сеть (входящий трафик)|Сеть в оплатах (не рекомендуется)|Байты|Всего|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|None|
|Сеть (исходящий трафик)|Оплата за сеть (не рекомендуется)|Байты|Всего|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Байт считано с диска за период мониторинга|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Всего|Число байтов, записанных на диск в течение периода мониторинга|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|None|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Число|Среднее|Общее число доступных для увеличения кредитов|None|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Число|Среднее|Общее количество кредитов, использованных виртуальной машиной|None|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с) (не рекомендуется)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с (устарело)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (устарело)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (устарело)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|None|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду (не рекомендуется)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду (не рекомендуется)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Длина очереди на диск ОС|Диск ОС длина очереди (не рекомендуется)|Число|Среднее|Длина очереди дисков ОС|None|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN|
|Операций чтения с диска данных/с|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Операций записи на диск данных в секунду|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Глубина очереди диска данных|Глубина очереди диска данных (Предварительная версия)|Число|Среднее|Длина очереди дисков данных|LUN|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|None|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|None|
|Операций чтения с диска ОС в секунду|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Операций записи на диск ОС в секунду|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|None|
|Глубина очереди диска ОС|Глубина очереди диска ОС (Предварительная версия)|Число|Среднее|Длина очереди дисков ОС|None|
|Входящие потоки|Входящие потоки|Число|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|None|
|Исходящие потоки|Исходящие потоки|Число|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|None|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|None|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков (Предварительная версия)|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|None|
|Попадание в кэш диска данных Premium|Попадание на чтение кэша диска данных Premium (Предварительная версия)|Процент|Среднее|Попадание в кэш диска данных Premium|LUN|
|Промах чтения кэша на диске данных Premium|Промах чтения кэша на диске данных Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске данных Premium|LUN|
|Попадание чтения кэша на диске ОС Premium|Попадание чтения кэша на диске ОС Premium (Предварительная версия)|Процент|Среднее|Попадание чтения кэша на диске ОС Premium|None|
|Промах чтения кэша на диске ОС Premium|Промах чтения кэша диска ОС Premium (Предварительная версия)|Процент|Среднее|Промах чтения кэша на диске ОС Premium|None|
|Всего сети|Всего сети|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|None|
|Общее количество сетевых исходящих|Общее количество сетевых исходящих|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Число|Среднее|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Среднее|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байты|Среднее|Количество байт, полученных по сети в секунду.|None|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байты|Среднее|Количество байт, переданных по сети в секунду.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|тоталпуллкаунт|Общее число броских|Число|Среднее|Общее число опросов изображений|None|
|сукцессфулпуллкаунт|Число успешных операций извлечения|Число|Среднее|Число успешных операций извлечения изображений|None|
|тоталпушкаунт|Общее число push-уведомлений|Число|Среднее|Общее число push-уведомлений для образа|None|
|сукцессфулпушкаунт|Число успешных push-уведомлений|Число|Среднее|Число успешных push-уведомлений образа|None|
|рундуратион|Длительность выполнения|Миллисекунд|Всего|Длительность выполнения в миллисекундах|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Число|Среднее|Общее количество доступных ядер ЦП в управляемом кластере|None|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байты|Среднее|Общий объем доступной памяти в управляемом кластере|None|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Число|Среднее|Число модулей pod в состоянии готовности|пространство имен, Pod|
|kube_node_status_condition|Состояния для различных условий узла|Число|Среднее|Состояния для различных условий узла|условие, состояние, status2, узел|
|kube_pod_status_phase|Число модулей pod по фазам|Число|Среднее|Число модулей pod по фазам|этап, пространство имен, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. Кустомпровидерс/ресаурцепровидерс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|сукцессфуллрекуестс|Выполненные запросы|Число|Всего|Успешные запросы, выполненные настраиваемым поставщиком|HttpMethod, Каллпас, StatusCode|
|FailedRequests|Невыполненные запросы|Число|Всего|Получение доступных журналов для пользовательских поставщиков ресурсов.|HttpMethod, Каллпас, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. Датабокседже/Датабокседжедевицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|никреадсраугхпут|Пропускная способность чтения (сеть)|Байт/с|Среднее|Пропускная способность чтения сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|никвритесраугхпут|Пропускная способность записи (сеть)|Байт/с|Среднее|Пропускная способность записи сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|клаудреадсраугхпутпершаре|Пропускная способность скачивания в облаке (общая папка)|Байт/с|Среднее|Пропускная способность загрузки в Azure из общей папки в течение отчетного периода.|Share (Предоставить общий доступ)|
|клаудуплоадсраугхпутпершаре|Пропускная способность передачи в облаке (общая папка)|Байт/с|Среднее|Пропускная способность передачи в Azure из общей папки в течение отчетного периода.|Share (Предоставить общий доступ)|
|битесуплоадедтоклаудпершаре|Отправлено облачных байт (общий ресурс)|Байты|Среднее|Общее число байтов, отправленных в Azure из общей папки в течение отчетного периода.|Share (Предоставить общий доступ)|
|Общая емкость составляет|Общая емкость|Байты|Среднее|Общая емкость|None|
|AvailableCapacity|Доступная емкость|Байты|Среднее|Доступная емкость в байтах за отчетный период.|None|
|клаудуплоадсраугхпут|Пропускная способность передачи в облаке|Байт/с|Среднее|Пропускная способность облачной передачи в Azure за период отчетного периода.|None|
|клаудреадсраугхпут|Пропускная способность скачивания в облаке|Байт/с|Среднее|Пропускная способность скачивания облака в Azure в течение отчетного периода.|None|
|битесуплоадедтоклауд|Отправлено облачных байт (устройство)|Байты|Среднее|Общее число байтов, отправленных в Azure с устройства за отчетный период.|None|
|хиперввиртуалпроцессорутилизатион|Вычисление ребра — процент ЦП|Процент|Среднее|Процент использования ЦП|InstanceName|
|хипервмеморютилизатион|Использование памяти при вычислении граничных ресурсов|Процент|Среднее|Объем используемой оперативной памяти|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Число|Всего||Пипелиненаме, activityName|
|SuccessfulRuns.|успешные циклы выполнения.|Число|Всего||Пипелиненаме, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Число|Всего||FailureType, имя|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Число|Всего||FailureType, имя|
|пипелинеканцелледрунс|Отмененные метрики запуска конвейера|Число|Всего||FailureType, имя|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Число|Всего||ActivityType, Пипелиненаме, FailureType, имя|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Число|Всего||ActivityType, Пипелиненаме, FailureType, имя|
|активитиканцелледрунс|Отмененные метрики выполнения действий|Число|Всего||ActivityType, Пипелиненаме, FailureType, имя|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Число|Всего||Имя, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Число|Всего||Имя, FailureType|
|тригжерканцелледрунс|Отмененные метрики запуска триггера|Число|Всего||Имя, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Процент|Среднее||Интегратионрунтименаме, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Среднее||Интегратионрунтименаме, NodeName|
|интегратионрунтимеаверажетаскпиккупделай|Длительность очереди среды выполнения интеграции|Секунды|Среднее||интегратионрунтименаме|
|интегратионрунтимекуеуеленгс|Длина очереди среды выполнения интеграции|Число|Среднее||интегратионрунтименаме|
|интегратионрунтимеаваилабленоденумбер|Число доступных узлов в среде выполнения интеграции|Число|Среднее||интегратионрунтименаме|
|максалловедресаурцекаунт|Максимальное число допустимых сущностей|Число|Максимальная||None|
|максалловедфакторисизеингбунитс|Максимально допустимый размер фабрики (единица GB)|Число|Максимальная||None|
|ресаурцекаунт|Общее число сущностей|Число|Максимальная||None|
|факторисизеингбунитс|Общий размер фабрики (ГБ единиц)|Число|Максимальная||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Число|Всего|Количество успешно выполненных заданий.|None|
|JobEndedFailure|Failed Jobs|Число|Всего|Количество невыполненных заданий.|None|
|JobEndedCancelled|Cancelled Jobs|Число|Всего|Количество отмененных заданий.|None|
|JobAUEndedSuccess|Successful AU Time|Секунды|Всего|Общее время AU успешно выполненных заданий.|None|
|JobAUEndedFailure|Failed AU Time|Секунды|Всего|Общее время AU невыполненных заданий.|None|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Всего|Общее время AU отмененных заданий.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|None|
|DataWritten|Записанные данные|Байты|Всего|Общий объем данных, записанных в учетной записи.|None|
|DataRead|Данных прочитано|Байты|Всего|Общий объем данных, прочитанных в учетной записи.|None|
|WriteRequests|Запросы на запись|Число|Всего|Количество запросов на запись данных в учетную запись.|None|
|ReadRequests|Запросы на чтение|Число|Всего|Количество запросов на чтение данных для учетной записи.|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
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
|active_connections|Активные подключения|Число|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Число|Всего|Неудачные подключения|None|
|seconds_behind_master|Задержка репликации в секундах|Число|Максимальная|Задержка репликации в секундах|None|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Всего|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Всего|Входящий сетевой трафик по активным подключениям|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
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
|active_connections|Активные подключения|Число|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Число|Всего|Неудачные подключения|None|
|seconds_behind_master|Задержка репликации в секундах|Число|Максимальная|Задержка репликации в секундах|None|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Всего|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Всего|Входящий сетевой трафик по активным подключениям|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
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
|active_connections|Активные подключения|Число|Среднее|Активные подключения|None|
|connections_failed|Неудачные подключения|Число|Всего|Неудачные подключения|None|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Всего|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Всего|Входящий сетевой трафик по активным подключениям|None|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимальная|Запаздывание реплики в секундах|None|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|Максимальная|Запаздывание в байтах самой задержкой реплики|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. Дбфорпостгрескл/serversv2

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|None|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|None|
|/|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Число|Среднее|Операций ввода-вывода в секунду|None|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|None|
|storage_used|Storage used|Байты|Среднее|Storage used|None|
|active_connections|Активные подключения|Число|Среднее|Активные подключения|None|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Всего|Исходящий сетевой трафик по активным подключениям|None|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Всего|Входящий сетевой трафик по активным подключениям|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Число|Всего|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|None|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Число|Всего|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|None|
|c2d.commands.egress.complete.success|Доставка сообщений C2D завершена|Число|Всего|Число попыток доставки сообщений, отправленных из облака на устройство, успешно завершено устройством|None|
|c2d.commands.egress.abandon.success|Отброшенных сообщений C2D|Число|Всего|Число сообщений, отправленных из облака на устройство, которые отброшены устройством|None|
|c2d.commands.egress.reject.success|Отклоненные сообщения C2D|Число|Всего|Число сообщений, отправленных из облака на устройство, отклоненных устройством|None|
|C2DMessagesExpired|Срок действия сообщений C2D истек (Предварительная версия)|Число|Всего|Число сообщений, отправленных из облака на устройство с истекшим сроком действия|None|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Число|Всего|Число устройств, зарегистрированных в Центре Интернета вещей.|None|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Число|Всего|Число устройств, подключенных к Центру Интернета вещей.|None|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Число|Всего|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|None|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Число|Всего|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|None|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Число|Всего|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |None|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Число|Всего|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|None|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|None|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|None|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|None|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|None|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|None|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|None|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|None|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events).|None|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events).|None|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|None|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|None|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байты|Всего|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|None|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Число|Всего|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|None|
|евентгридделивериес|Сетка событий: доставок (Предварительная версия)|Число|Всего|Число событий центра Интернета вещей, опубликованных в сетке событий. Используйте измерение результат для количества успешных и неудачных запросов. Измерение EventType показывает тип события (https://aka.ms/ioteventgrid).|ResourceId, result, EventType|
|евентгридлатенци|Задержка сетки событий (Предварительная версия)|Миллисекунд|Среднее|Средняя задержка (в миллисекундах) от момента создания события центра Интернета вещей до публикации события в сетке событий. Это число является средним значением между всеми типами событий. Используйте измерение EventType для просмотра задержки определенного типа события.|ResourceId, EventType|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Число|Всего|Число всех успешных операций чтения с двойников, инициированных устройством.|None|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Число|Всего|Число всех неудачных операций чтения с двойников, инициированных устройством.|None|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|None|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Число|Всего|Число всех успешных обновлений двойников, инициированных устройством.|None|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Число|Всего|Число всех неудачных обновлений двойников, инициированных устройством.|None|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|None|
|c2d.methods.success|Успешные вызовы прямых методов.|Число|Всего|Число всех успешных вызовов прямых методов.|None|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Число|Всего|Число всех неудачных вызовов прямых методов.|None|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|None|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|None|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Число|Всего|Число всех успешных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Число|Всего|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|None|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Число|Всего|Число всех успешных обновлений двойников, инициированных из серверной части.|None|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Число|Всего|Число всех неудачных обновлений двойников, инициированных из серверной части.|None|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|None|
|twinQueries.success|Успешные запросы двойников.|Число|Всего|Число всех успешных запросов двойников.|None|
|twinQueries.failure|Неудачные запросы двойников.|Число|Всего|Количество всех неудачных запросов двойников.|None|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Среднее|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|None|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Число|Всего|Количество всех успешных созданий заданий обновления двойников.|None|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Число|Всего|Количество всех неудачных операций создания заданий обновления двойников.|None|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Число|Всего|Количество всех успешных операций создания заданий вызова прямых методов.|None|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Число|Всего|Количество всех неудачных операций создания заданий вызова прямых методов.|None|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Число|Всего|Количество всех успешных вызовов для получения списка заданий.|None|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Число|Всего|Количество всех неудачных вызовов для получения списка заданий.|None|
|jobs.cancelJob.success|Успешные отмены заданий.|Число|Всего|Количество всех успешных вызовов для отмены заданий.|None|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Число|Всего|Количество всех неудачных вызовов для отмены заданий.|None|
|jobs.queryJobs.success|Успешные запросы заданий.|Число|Всего|Количество всех успешных вызовов для запроса заданий.|None|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Число|Всего|Количество всех неудачных вызовов для запроса заданий.|None|
|jobs.completed|Завершенные задания|Число|Всего|Количество всех выполненных заданий.|None|
|jobs.failed|Неудачные задания.|Число|Всего|Количество всех неудачных заданий.|None|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Число|Всего|Количество ошибок регулирования из-за регулирования пропускной способности устройства|None|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Число|Среднее|Количество сообщений, использованных сегодня|None|
|deviceDataUsage|Общее использование данных устройства|Байты|Всего|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|None|
|deviceDataUsageV2|Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))|Байты|Всего|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|None|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Число|Среднее|Число устройств, зарегистрированных в Центре Интернета вещей.|None|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Число|Среднее|Число устройств, подключенных к Центру Интернета вещей.|None|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Число|Всего|Метрики для операций конфигурации|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Число|Всего|Количество попыток регистрации устройств|Провисионингсервиценаме, IotHubName, состояние|
|DeviceAssignments|Назначенные устройства|Число|Всего|Количество устройств, назначенных Центру Интернета вещей|Провисионингсервиценаме, IotHubName|
|AttestationAttempts|Попытки аттестации|Число|Всего|Количество попыток аттестации устройств|Провисионингсервиценаме, состояние, протокол|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|аддрегион|Регион добавлен|Число|Число|Регион добавлен|Регион|
|AvailableStorage|Доступная служба хранилища|Байты|Всего|Общее Доступное хранилище получено по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|CassandraConnectionClosures|Отключение связи Cassandra|Число|Всего|Число закрытых подключений Cassandra, отправленных в отчет с детализацией по 1 минуте|Апитипе, регион, Клосуререасон|
|CassandraRequestCharges|Расходы запросов по Cassandra|Число|Всего|Для запросов Cassandra используется параметр "RUs"|Апитипе, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Запросы по Cassandra|Число|Число|Число выполненных запросов Cassandra|Апитипе, DatabaseName, CollectionName, регион, OperationType, ResourceType, ErrorCode|
|креатеаккаунт|Учетная запись создана|Число|Число|Учетная запись создана|None|
|DataUsage|Использование данных|Байты|Всего|Общая степень использования данных в 5 минут|CollectionName, DatabaseName, регион|
|делетеаккаунт|Учетная запись удалена|Число|Число|Учетная запись удалена|None|
|DocumentCount|Число документов|Число|Всего|Общее число документов, о которых сообщило 5 минут|CollectionName, DatabaseName, регион|
|DocumentQuota|Квота на документы|Байты|Всего|Общая квота хранилища, полученная на уровне 5 минут|CollectionName, DatabaseName, регион|
|IndexUsage|Использование индексов|Байты|Всего|Общее использование индекса, полученное по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|MetadataRequests|Запросы метаданных|Число|Число|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|DatabaseName, CollectionName, регион, StatusCode, роль|
|MongoRequestCharge|Запросить оплату Mongo|Число|Всего|Использованные единицы запросов Mongo|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|MongoRequests|Запросы Mongo|Число|Число|Количество выполненных запросов Mongo|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|монгорекуестскаунт|Частота запросов Mongo|Число/с|Среднее|Число запросов Mongo в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|монгорекуестсделете|Частота запросов на удаление Mongo|Число/с|Среднее|Mongoных запросов на удаление в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|монгорекуестсинсерт|Частота запросов на вставку Mongo|Число/с|Среднее|Число вставок Mongo в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|монгорекуестскуери|Частота запросов Mongo запросов|Число/с|Среднее|Mongo запросов в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|монгорекуестсупдате|Частота запросов на обновление Mongo|Число/с|Среднее|Mongo запросов на обновление в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|ProvisionedThroughput|Подготовленная пропускная способность|Число|Максимальная|Подготовленная пропускная способность|DatabaseName, CollectionName|
|регионфаиловер|Отработка отказа региона|Число|Число|Отработка отказа региона|None|
|ремоверегион|Регион удален|Число|Число|Регион удален|Регион|
|ReplicationLatency|Задержка репликации P99|MilliSeconds|Среднее|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|Саурцерегион, Таржетрегион|
|ServiceAvailability|Доступность службы|Процент|Среднее|Доступность запросов учетной записи за один час, с детализацией дня или месяца|None|
|TotalRequestUnits|Общее количество единиц запросов|Число|Всего|Использованные единицы запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Общее количество запросов|Число|Число|Количество выполненных запросов|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|упдатеаккаунткэйс|Ключи учетной записи обновлены|Число|Число|Ключи учетной записи обновлены|KeyType|
|упдатеаккаунтнетворксеттингс|Параметры сети учетной записи обновлены|Число|Число|Параметры сети учетной записи обновлены|None|
|упдатеаккаунтрепликатионсеттингс|Параметры репликации учетной записи обновлены|Число|Число|Параметры репликации учетной записи обновлены|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. Ентерприсекновледжеграф/Services

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|трансактионкаунт|Число транзакций|Число|Число|Общее число транзакций|трансактионкаунт|
|SuccessCount|Подсчет успешных событий|Число|Число|Число выполненных транзакций|SuccessCount|
|FailureCount|Число сбоев|Число|Число|Число невыполненных транзакций|FailureCount|
|сукцесслатенци|Задержка успешного выполнения|MilliSeconds|Среднее|Задержка успешных транзакций|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Число|Всего|Всего событий, опубликованных в этом разделе|Раздел|
|PublishFailCount|Опубликовать события с ошибками|Число|Всего|Всего событий, которые не удалось опубликовать в этом разделе|Раздел, ErrorType, ошибка|
|публишсукцесслатенциинмс|Задержка успешной публикации|Миллисекунд|Всего|Задержка успешной публикации в миллисекундах|None|
|MatchedEventCount|Соответствующие события|Число|Всего|Всего событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Число|Всего|Всего событий, которые не удалось доставить в эту подписку на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Error, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Число|Всего|Всего событий, доставленных в эту подписку на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунд|Среднее|Длительность обработки назначения в миллисекундах|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DroppedEventCount|Удаленные события|Число|Всего|Всего удаленных событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Дропреасон|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Число|Всего|Всего невостребованных событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Деадлеттерреасон|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Число|Всего|Всего событий, опубликованных в этом разделе|None|
|PublishFailCount|Опубликовать события с ошибками|Число|Всего|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Число|Всего|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|None|
|публишсукцесслатенциинмс|Задержка успешной публикации|Миллисекунд|Всего|Задержка успешной публикации в миллисекундах|None|
|MatchedEventCount|Соответствующие события|Число|Всего|Всего событий, которые соответствуют этой подписке на события|евентсубскриптионнаме|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Число|Всего|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка, ErrorType, Евентсубскриптионнаме|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Число|Всего|Всего событий, доставленных в эту подписку на события|евентсубскриптионнаме|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунд|Среднее|Длительность обработки назначения в миллисекундах|евентсубскриптионнаме|
|DroppedEventCount|Удаленные события|Число|Всего|Всего удаленных событий, которые соответствуют этой подписке на события|Дропреасон, Евентсубскриптионнаме|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Число|Всего|Всего невостребованных событий, которые соответствуют этой подписке на события|Деадлеттерреасон, Евентсубскриптионнаме|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Число|Всего|Всего событий, которые соответствуют этой подписке на события|None|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Число|Всего|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Число|Всего|Всего событий, доставленных в эту подписку на события|None|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунд|Среднее|Длительность обработки назначения в миллисекундах|None|
|DroppedEventCount|Удаленные события|Число|Всего|Всего удаленных событий, которые соответствуют этой подписке на события|дропреасон|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Число|Всего|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Число|Всего|Всего событий, опубликованных в этом разделе|None|
|PublishFailCount|Опубликовать события с ошибками|Число|Всего|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Число|Всего|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|None|
|публишсукцесслатенциинмс|Задержка успешной публикации|Миллисекунд|Всего|Задержка успешной публикации в миллисекундах|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Число|Всего|Выполненные запросы для Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Ошибки сервера.|Число|Всего|Ошибки на сервере для Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Ошибки пользователей.|Число|Всего|Ошибки пользователей для Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Число|Всего|Ошибки превышения квоты для Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Регулируемые запросы.|Число|Всего|Регулируемые запросы для Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Входящих запросов|Число|Всего|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Число|Всего|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Число|Всего|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байты|Всего|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байты|Всего|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Число|Среднее|Число активных подключений для Microsoft.EventHub.|None|
|ConnectionsOpened|Открытые подключения.|Число|Среднее|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Число|Среднее|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Число|Всего|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Число|Всего|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байты|Всего|Записанные байты для Microsoft.EventHub.|EntityName|
|Размер|Размер|Байты|Среднее|Размер EventHub в байтах.|EntityName|
|INREQS|Входящие запросы (не рекомендуется)|Число|Всего|Всего входящих запросов на отправку для пространства имен (не рекомендуется)|None|
|SUCCREQ|Успешные запросы (устарело)|Число|Всего|Общее количество успешных запросов для пространства имен (не рекомендуется)|None|
|FAILREQ|Неудачные запросы (устарело)|Число|Всего|Всего неудачных запросов для пространства имен (не рекомендуется)|None|
|SVRBSY|Ошибки занятости сервера (устарели)|Число|Всего|Общее число ошибок "сервер занят" для пространства имен (не рекомендуется)|None|
|INTERR|Внутренние ошибки сервера (не рекомендуется)|Число|Всего|Общее количество внутренних ошибок сервера для пространства имен (не рекомендуется)|None|
|MISCERR|Другие ошибки (не рекомендуется)|Число|Всего|Всего неудачных запросов для пространства имен (не рекомендуется)|None|
|INMSGS|Входящие сообщения (устаревшие) (не рекомендуется)|Число|Всего|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих сообщений (не рекомендуется).|None|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Число|Всего|Всего входящих сообщений для пространства имен (не рекомендуется)|None|
|OUTMSGS|Исходящие сообщения (устаревшие) (не рекомендуется)|Число|Всего|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих сообщений (не рекомендуется).|None|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Число|Всего|Всего исходящих сообщений для пространства имен (не рекомендуется)|None|
|EHINMBS|Входящие байты (устарели) (не рекомендуется)|Байты|Всего|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих байт (не рекомендуется).|None|
|EHINBYTES|Входящие байты (не рекомендуется)|Байты|Всего|Пропускная способность входящего сообщения концентратора событий для пространства имен (не рекомендуется)|None|
|EHOUTMBS|Исходящие байты (устарели) (не рекомендуется)|Байты|Всего|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих байт (не рекомендуется).|None|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байты|Всего|Пропускная способность исходящего сообщения концентратора событий для пространства имен (не рекомендуется)|None|
|EHABL|Архивировать сообщения невыполненной работы (не рекомендуется)|Число|Всего|Архивные сообщения концентратора событий в невыполненной работе для пространства имен (не рекомендуется)|None|
|EHAMSGS|Архивирование сообщений (не рекомендуется)|Число|Всего|Архивные сообщения концентратора событий в пространстве имен (не рекомендуется)|None|
|EHAMBS|Пропускная способность сообщений архивации (не рекомендуется)|Байты|Всего|Пропускная способность архивного сообщения концентратора событий в пространстве имен (не рекомендуется)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Число|Всего|Выполненные запросы для Microsoft.EventHub.|OperationResult|
|ServerErrors|Ошибки сервера.|Число|Всего|Ошибки на сервере для Microsoft.EventHub.|OperationResult|
|UserErrors|Ошибки пользователей.|Число|Всего|Ошибки пользователей для Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Число|Всего|Ошибки превышения квоты для Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Регулируемые запросы.|Число|Всего|Регулируемые запросы для Microsoft.EventHub.|OperationResult|
|IncomingRequests|Входящих запросов|Число|Всего|Входящие запросы для Microsoft.EventHub.|None|
|IncomingMessages|Входящие сообщения|Число|Всего|Входящие сообщения для Microsoft.EventHub.|None|
|OutgoingMessages|Исходящие сообщения|Число|Всего|Исходящие сообщения для Microsoft.EventHub.|None|
|IncomingBytes|Входящие байты.|Байты|Всего|Входящие байты для Microsoft.EventHub.|None|
|OutgoingBytes|Исходящие байты.|Байты|Всего|Исходящие байты для Microsoft.EventHub.|None|
|ActiveConnections|ActiveConnections|Число|Среднее|Число активных подключений для Microsoft.EventHub.|None|
|ConnectionsOpened|Открытые подключения.|Число|Среднее|Открытые подключения для Microsoft.EventHub.|None|
|ConnectionsOpened|Закрытые подключения.|Число|Среднее|Закрытые подключения для Microsoft.EventHub.|None|
|CaptureBacklog|Невыполненная работа записи.|Число|Всего|Невыполненная работа записи для Microsoft.EventHub.|None|
|CapturedMessages|Записанные сообщения.|Число|Всего|Записанные сообщения для Microsoft.EventHub.|None|
|CapturedBytes|Записанные байты.|Байты|Всего|Записанные байты для Microsoft.EventHub.|None|
|ЦП|ЦП|Процент|Максимальная|Использование ЦП для кластера концентратора событий в процентах|Роль|
|AvailableMemory|Доступная память|Процент|Максимальная|Объем доступной памяти для кластера концентраторов событий в процентах от общего объема памяти.|Роль|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Число|Всего|Число запросов к шлюзу|HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Число|Всего|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|HttpStatus|
|нумактивеворкерс|Число активных рабочих ролей|Число|Максимальная|Число активных рабочих ролей|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Число|Среднее|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Число|Среднее|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Число|Среднее|Емкость, передаваемая для автомасштабирования при выполнении.|None|
|ScaleActionsInitiated|Инициированные действия масштабирования|Число|Всего|Направление операции масштабирования.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|availabilityResults/Аваилабилитиперцентаже|Доступность|Процент|Среднее|Процент успешно завершенных тестов доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location|
|availabilityResults/Count|Тесты доступности|Число|Число|Число тестов доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location, Аваилабилитиресулт/Success|
|availabilityResults/duration|Длительность теста доступности|MilliSeconds|Среднее|Длительность теста доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location, Аваилабилитиресулт/Success|
|browserTimings/networkDuration|Время подключения к сети при загрузке страницы|MilliSeconds|Среднее|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|None|
|browserTimings/processingDuration|Время обработки клиента|MilliSeconds|Среднее|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|None|
|browserTimings/receiveDuration|Время получения ответа|MilliSeconds|Среднее|Время от первого до последнего байта или до отключения.|None|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Среднее|Время от установки сетевого подключения до получения первого байта.|None|
|browserTimings/totalDuration|Время загрузки страницы в браузере|MilliSeconds|Среднее|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|None|
|dependencies/count|Вызовы зависимостей|Число|Число|Число вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|dependencies/duration|Длительность зависимости|MilliSeconds|Среднее|Длительность вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|dependencies/failed|Сбои при вызове зависимостей|Число|Число|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|pageViews/count|Просмотры страниц|Число|Число|Число просмотров страниц.|операция, искусственная, облачная/roleName|
|pageViews/duration|Время загрузки страницы для просмотра|MilliSeconds|Среднее|Время загрузки страницы для просмотра|операция, искусственная, облачная/roleName|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Среднее|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue (HTTP-запросы в очереди приложений)|Число|Среднее|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|Число/с|Среднее|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|Число/с|Среднее|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|Байт/с|Среднее|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Обработка ЦП|Процент|Среднее|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Процессорное время|Процент|Среднее|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Объем доступной памяти|Байты|Среднее|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Количество байтов исключительного использования процессов|Байты|Среднее|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|времени ответа сервера;|MilliSeconds|Среднее|Время с момента получения HTTP-запроса до завершения отправки ответа.|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|requests/count|Запросы сервера|Число|Число|Число выполненных запросов HTTP.|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|requests/failed|Failed requests (Неудачные запросы)|Число|Число|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|запрос/Перформанцебуккет, запрос/resultCode, запрос/успешно, операция, искусственный, облако или roleInstance, Облако/roleName|
|запросов и ставок|Частота запросов сервера|Число/с|Среднее|Частота запросов сервера в секунду|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|exceptions/count|Исключения|Число|Число|Общее число всех неперехваченных исключений.|Облако/roleName, Cloud/roleInstance, клиент/тип|
|exceptions/browser|Исключения браузера|Число|Число|Число необработанных исключений в браузере.|клиент/сервер, Облако/roleName|
|exceptions/server|Исключения сервера|Число|Число|Число неперехваченных исключений, созданных в серверном приложении.|клиент/сервер, Облако/roleName, Cloud/roleInstance|
|traces/count|Трассировки|Число|Число|Число документов трассировки|Trace/Северитилевел, Operation/синтетическая, облачная/roleName, Cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Число|Число|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунд|Среднее|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode, Статускодекласс|
|ServiceApiResult|Всего результатов для API службы|Число|Число|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode, Статускодекласс|
|сатуратионшоебокс|Общая насыщенность хранилища|Процент|Среднее|Используемая емкость хранилища|ActivityType, ActivityName, Трансактионтипе|
|Доступность|Общая доступность хранилища|Процент|Среднее|Доступность запросов хранилища|ActivityType, ActivityName, StatusCode, Статускодекласс|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|качеутилизатион|Использование кэша|Процент|Среднее|Уровень загрузки в области кластера|None|
|QueryDuration|Query duration (Длительность запросов)|Миллисекунд|Среднее|Длительность запросов в секундах|QueryStatus|
|инжестионутилизатион|Использование приема|Процент|Среднее|Доля использованных слотов приема данных в кластере|None|
|Проверки|Срок поддержания активности|Число|Среднее|Проверка работоспособности показывает, что кластер отвечает на запросы|None|
|инжестионволумеинмб|Объем приема (в МБ)|Число|Всего|Общий объем данных, принятых в кластере (в МБ)|База данных|
|инжестионлатенциинсекондс|Задержка приема (в секундах)|Секунды|Среднее|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|None|
|евентспроцесседфоревенсубс|Обработано событий (для концентраторов событий и IoT)|Число|Всего|Число событий, обработанных кластером при приеме из события или центра Интернета вещей|EventStatus|
|IngestionResult|Результат приема|Число|Число|Число операций приема данных|IngestionResultDetails|
|ЦП|ЦП|Процент|Среднее|Уровень загрузки ЦП|None|
|континуаусекспортнумофрекордсекспортед|Непрерывный экспорт — число экспортированных записей|Число|Всего|Число экспортированных записей, которое срабатывает для каждого артефакта хранилища, записанного во время операции экспорта|None|
|експортутилизатион|Использование экспорта|Процент|Максимальная|Использование экспорта|None|
|континуаусекспортпендингкаунт|Число ожидающих непрерывного экспорта|Число|Максимальная|Число ожидающих заданий непрерывного экспорта, готовых к выполнению|None|
|континуаусекспортмакслатенессминутес|Максимальное время непрерывного экспорта (мин.)|Число|Максимальная|Максимальная длина в минутах для всех непрерывных экспортов, ожидающих выполнения и готовых к выполнению|None|
|континуаусекспортресулт|Результат непрерывного экспорта|Число|Число|Указывает, завершился ли непрерывный экспорт успешно или нет|Континуаусекспортнаме, результат, база данных|
|стреамингинжестдуратион|Длительность приема потоковой передачи|Миллисекунд|Среднее|Длительность приема потоковой передачи в миллисекундах|None|
|стреамингинжестдатарате|Скорость приема данных потоковой передачи|Число|Среднее|Скорость приема данных потоковой передачи (МБ в секунду)|None|
|стеамингинжестрекуестрате|Частота запросов приема потоковой передачи|Число|ратерекуестсперсеконд|Частота запросов приема потоковой передачи (запросов в секунду)|None|
|стреамингинжестресултс|Поток приема результатов|Число|Среднее|Поток приема результатов|Результат|
|тоталнумберофконкурренткуериес|Общее число одновременных запросов|Число|Всего|Общее число одновременных запросов|None|
|тоталнумберофсроттледкуериес|Общее число регулируемых запросов|Число|Всего|Общее число регулируемых запросов|None|
|тоталнумберофсроттледкоммандс|Общее число регулируемых команд|Число|Всего|Общее число регулируемых команд|CommandType|
|тоталнумберофекстентс|Общее число экстентов|Число|Всего|Общее число экстентов данных|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Число|Всего|Число запущенных выполнений рабочего процесса.|None|
|RunsCompleted|Runs Completed|Число|Всего|Число завершенных выполнений рабочего процесса.|None|
|RunsSucceeded|Runs Succeeded|Число|Всего|Число успешно завершенных выполнений рабочего процесса.|None|
|RunsFailed|Runs Failed|Число|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|None|
|RunsCancelled|Runs Cancelled|Число|Всего|Число отмененных выполнений рабочего процесса.|None|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|None|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|None|
|RunThrottledEvents|Run Throttled Events|Число|Всего|Число событий регулирования действия или триггера рабочего процесса.|None|
|рунстартсроттледевентс|Запустить регулируемые события запуска|Число|Всего|Число перерегулируемых событий запуска рабочего процесса.|None|
|RunFailurePercentage|Run Failure Percentage|Процент|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|None|
|ActionsStarted|Actions Started |Число|Всего|Число запущенных действий рабочего процесса.|None|
|ActionsCompleted|Actions Completed |Число|Всего|Число завершенных действий рабочего процесса.|None|
|ActionsSucceeded|Actions Succeeded |Число|Всего|Число успешно выполненных действий рабочего процесса.|None|
|ActionsFailed|Actions Failed |Число|Всего|Число действий рабочего процесса, завершившихся сбоем.|None|
|ActionsSkipped|Actions Skipped |Число|Всего|Число пропущенных действий рабочего процесса.|None|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|None|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|None|
|ActionThrottledEvents|Action Throttled Events|Число|Всего|Число событий регулирования действия рабочего процесса.|None|
|TriggersStarted|Triggers Started |Число|Всего|Число запущенных триггеров рабочего процесса.|None|
|TriggersCompleted|Triggers Completed |Число|Всего|Число завершенных триггеров рабочего процесса.|None|
|TriggersSucceeded|Triggers Succeeded |Число|Всего|Число успешно выполненных триггеров рабочего процесса.|None|
|TriggersFailed|Triggers Failed |Число|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|None|
|TriggersSkipped|Triggers Skipped|Число|Всего|Число пропущенных триггеров рабочего процесса.|None|
|TriggersFired|Triggers Fired |Число|Всего|Число активированных триггеров рабочего процесса.|None|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|None|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|None|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|None|
|TriggerThrottledEvents|Trigger Throttled Events|Число|Всего|Число событий регулирования триггера рабочего процесса.|None|
|BillableActionExecutions|Billable Action Executions|Число|Всего|Число выполненных действий рабочего процесса, за которые выставляется счет.|None|
|BillableTriggerExecutions|Billable Trigger Executions|Число|Всего|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|None|
|TotalBillableExecutions|Total Billable Executions|Число|Всего|Число выполнений рабочего процесса, за которые выставляется счет.|None|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Число|Всего|Число выполнений внутренних операций, за которые выставляется счет.|None|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Число|Всего|Число операций стандартного соединителя, за которые выставляется счет.|None|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Число|Всего|Число операций с использованием хранилища, за которые выставляется счет.|None|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Число|Всего|Число выполнений внутренних операций, за которые выставляется счет.|None|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Число|Всего|Число операций стандартного соединителя, за которые выставляется счет.|None|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Число|Всего|Число операций с использованием хранилища, за которые выставляется счет.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/Интегратионсервицеенвиронментс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Число|Всего|Число запущенных выполнений рабочего процесса.|None|
|RunsCompleted|Runs Completed|Число|Всего|Число завершенных выполнений рабочего процесса.|None|
|RunsSucceeded|Runs Succeeded|Число|Всего|Число успешно завершенных выполнений рабочего процесса.|None|
|RunsFailed|Runs Failed|Число|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|None|
|RunsCancelled|Runs Cancelled|Число|Всего|Число отмененных выполнений рабочего процесса.|None|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|None|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|None|
|RunThrottledEvents|Run Throttled Events|Число|Всего|Число событий регулирования действия или триггера рабочего процесса.|None|
|рунстартсроттледевентс|Запустить регулируемые события запуска|Число|Всего|Число перерегулируемых событий запуска рабочего процесса.|None|
|RunFailurePercentage|Run Failure Percentage|Процент|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|None|
|ActionsStarted|Actions Started |Число|Всего|Число запущенных действий рабочего процесса.|None|
|ActionsCompleted|Actions Completed |Число|Всего|Число завершенных действий рабочего процесса.|None|
|ActionsSucceeded|Actions Succeeded |Число|Всего|Число успешно выполненных действий рабочего процесса.|None|
|ActionsFailed|Actions Failed |Число|Всего|Число действий рабочего процесса, завершившихся сбоем.|None|
|ActionsSkipped|Actions Skipped |Число|Всего|Число пропущенных действий рабочего процесса.|None|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|None|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|None|
|ActionThrottledEvents|Action Throttled Events|Число|Всего|Число событий регулирования действия рабочего процесса.|None|
|TriggersStarted|Triggers Started |Число|Всего|Число запущенных триггеров рабочего процесса.|None|
|TriggersCompleted|Triggers Completed |Число|Всего|Число завершенных триггеров рабочего процесса.|None|
|TriggersSucceeded|Triggers Succeeded |Число|Всего|Число успешно выполненных триггеров рабочего процесса.|None|
|TriggersFailed|Triggers Failed |Число|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|None|
|TriggersSkipped|Triggers Skipped|Число|Всего|Число пропущенных триггеров рабочего процесса.|None|
|TriggersFired|Triggers Fired |Число|Всего|Число активированных триггеров рабочего процесса.|None|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|None|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|None|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|None|
|TriggerThrottledEvents|Trigger Throttled Events|Число|Всего|Число событий регулирования триггера рабочего процесса.|None|
|интегратионсервицеенвиронментворкфловпроцессорусаже|Использование процессора рабочих процессов для среда службы интеграции|Процент|Среднее|Использование процессора рабочих процессов для среды службы интеграции.|None|
|интегратионсервицеенвиронментворкфловмеморюсаже|Использование памяти рабочего процесса для среда службы интеграции|Процент|Среднее|Использование памяти рабочего процесса для среды службы интеграции.|None|
|интегратионсервицеенвиронментконнекторпроцессорусаже|Использование процессора соединителя для среда службы интеграции|Процент|Среднее|Использование процессора соединителя для среды службы интеграции.|None|
|интегратионсервицеенвиронментконнектормеморюсаже|Использование памяти соединителя для среда службы интеграции|Процент|Среднее|Использование памяти соединителя для среды службы интеграции.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Завершенные запуски|Завершенные запуски|Число|Всего|Число выполнений, успешно завершенных для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запущенные запуски|Запущенные запуски|Число|Всего|Число запусков, запущенных для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|циклы выполнения со сбоем;|циклы выполнения со сбоем;|Число|Всего|Число запусков, завершившихся с этой рабочей областью|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Регистрация модели прошла|Регистрация модели прошла|Число|Всего|Число регистраций моделей, которые были успешной в этой рабочей области|Сценарий|
|Не удалось зарегистрировать модель|Не удалось зарегистрировать модель|Число|Всего|Число неудачных регистраций моделей в этой рабочей области|Сценарий, StatusCode|
|развертывание модели запущено|развертывание модели запущено|Число|Всего|Число развертываний моделей, запущенных в этой рабочей области|Сценарий|
|развертывание модели успех|развертывание модели успех|Число|Всего|Число развертываний моделей, завершившихся в этой рабочей области|Сценарий|
|Сбой развертывание модели|Сбой развертывание модели|Число|Всего|Число развертываний моделей, завершившихся сбоем в этой рабочей области|Сценарий, StatusCode|
|Всего узлов|Всего узлов|Число|Среднее|Общее число узлов. Сюда входят некоторые активные узлы, бездействующие узлы, неиспользуемые узлы, прерываются узлы, покидает узлы.|Сценарий, имя_кластера|
|Активные узлы|Активные узлы|Число|Среднее|Число узлов Acitve. Это узлы, активно выполняющие задание.|Сценарий, имя_кластера|
|Бездействующие узлы|Бездействующие узлы|Число|Среднее|Количество бездействующих узлов. Бездействующие узлы — это узлы, на которых не выполняются никакие задания, но могут принимать новые задания, если они доступны.|Сценарий, имя_кластера|
|Неиспользуемые узлы|Неиспользуемые узлы|Число|Среднее|Количество неиспользуемых узлов. Неиспользуемые узлы не работают из-за неразрешимой проблемы. Azure выполнит повторный запуск этих узлов.|Сценарий, имя_кластера|
|Прерванные узлы|Прерванные узлы|Число|Среднее|Число узлов с вытеснением. Эти узлы являются узлами с низким приоритетом, взятыми из пула доступных узлов.|Сценарий, имя_кластера|
|Покинуть узлы|Покинуть узлы|Число|Среднее|Количество покидает узлов. Узлы — это узлы, которые только что завершили обработку задания и будут переходить в состояние простоя.|Сценарий, имя_кластера|
|Всего ядер|Всего ядер|Число|Среднее|Общее число ядер|Сценарий, имя_кластера|
|Активные ядра|Активные ядра|Число|Среднее|Число активных ядер|Сценарий, имя_кластера|
|Бездействующие ядра|Бездействующие ядра|Число|Среднее|Число бездействующих ядер|Сценарий, имя_кластера|
|Непригодные для использования ядра|Непригодные для использования ядра|Число|Среднее|Число неиспользуемых ядер|Сценарий, имя_кластера|
|Вытесненные ядра|Вытесненные ядра|Число|Среднее|Число ядер с вытеснением|Сценарий, имя_кластера|
|Освобождение ядер|Освобождение ядер|Число|Среднее|Число покидает ядер|Сценарий, имя_кластера|
|Процент использования квоты|Процент использования квоты|Число|Среднее|Процент использования квоты|Сценарий, имя_кластера, Вмфамилинаме, Вмприорити|
|График|График|Число|Среднее|CPU (Preview) (ЦП (предварительная версия))|Сценарий, runId, NodeId, время создания|
|гпуутилизатион|гпуутилизатион|Число|Среднее|GPU (Предварительная версия)|Сценарий, runId, NodeId, время создания, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Число|Число|Число вызовов API|Апикатегори, ApiName, ResultType, ResponseCode|
|Доступность|Доступность|Процент|Среднее|Доступность API-интерфейсов|Апикатегори, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/mediaservices/Streamingendpoint

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Исходящие|Исходящие|Байты|Всего|Объем выходных данных в байтах.|OutputFormat|
|SuccessE2ELatency|Успешное завершение до окончания задержки|Миллисекунд|Среднее|Средняя задержка для успешных запросов в миллисекундах.|OutputFormat|
|Requests|Requests|Число|Всего|Запросы к конечной точке потоковой передачи.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ассеткуота|Квота ресурса|Число|Среднее|Сколько ресурсов разрешено для текущей учетной записи службы мультимедиа|None|
|ассеткаунт|Число ресурсов|Число|Среднее|Сколько ресурсов уже создано в текущей учетной записи службы мультимедиа|None|
|ассеткуотауседперцентаже|Процент используемой квоты актива|Процент|Среднее|Процент использования ресурса в текущей учетной записи службы мультимедиа|None|
|контенткэйполицикуота|Квота политики для ключа содержимого|Число|Среднее|Сколько политик ключей содержимого разрешено для текущей учетной записи службы мультимедиа|None|
|контенткэйполицикаунт|Число политик ключей содержимого|Число|Среднее|Сколько политик ключей содержимого уже создано в текущей учетной записи службы мультимедиа|None|
|контенткэйполицикуотауседперцентаже|Процент использования квоты для ключа содержимого|Процент|Среднее|Процент использования политики ключа содержимого в текущей учетной записи службы мультимедиа|None|
|стреамингполицикуота|Квота политики потоковой передачи|Число|Среднее|Сколько политик потоковой передачи разрешено для текущей учетной записи службы мультимедиа|None|
|стреамингполицикаунт|Число политик потоковой передачи|Число|Среднее|Сколько политик потоковой передачи уже созданы в текущей учетной записи службы мультимедиа|None|
|стреамингполицикуотауседперцентаже|Процент используемой квоты политики потоковой передачи|Процент|Среднее|Процент использования политики потоковой передачи в текущей учетной записи службы мультимедиа|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|AverageReadLatency|Средняя задержка чтения|MilliSeconds|Среднее|Средняя задержка чтения в миллисекундах на операцию|None|
|AverageWriteLatency|Средняя задержка записи|MilliSeconds|Среднее|Средняя задержка записи в миллисекундах на операцию|None|
|VolumeLogicalSize|Volume logical size (Логический размер тома)|Байты|Среднее|Логический размер тома (используемые байты)|None|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|Байты|Среднее|Размер всех моментальных снимков в томе|None|
|ReadIops|Read iops (Число операций ввода-вывода в секунду при чтении)|Число/с|Среднее|Число операций ввода-вывода в секунду при чтении|None|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|Число/с|Среднее|Число операций ввода-вывода в секунду при записи|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Volume pool allocated used (Используемый размер выделенного пула для тома)|Байты|Среднее|Используемый выделенный размер пула|None|
|VolumePoolTotalLogicalSize|Volume pool total logical size (Общий логический размер пула для тома)|Байты|Среднее|Сумма логического размера всех томов, входящих в пул|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Байты|Всего|Число байт, отправленных сетевым интерфейсом|None|
|BytesReceivedRate|Получено байт|Байты|Всего|Число байт, полученных сетевым интерфейсом|None|
|PacketsSentRate|Отправлено пакетов|Число|Всего|Число пакетов, отправленных сетевым интерфейсом|None|
|PacketsReceivedRate|Получено пакетов|Число|Всего|Число пакетов, полученных сетевым интерфейсом|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Число|Среднее|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|Фронтендипаддресс, FrontendPort|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Число|Среднее|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType, Баккендпорт, Фронтендипаддресс, FrontendPort, Баккендипаддресс|
|ByteCount|Количество байтов|Число|Всего|Общее количество байтов, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|PacketCount|Количество пакетов|Число|Всего|Общее количество пакетов, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|SYNCount|Количество пакетов SYN|Число|Всего|Общее количество пакетов SYN, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|SnatConnectionCount|Количество подключений SNAT|Число|Всего|Общее количество подключений SNAT, созданных за период времени|Фронтендипаддресс, Баккендипаддресс, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Число|Всего|Общее число портов SNAT, выделенных за период времени|Фронтендипаддресс, Баккендипаддресс, ProtocolType, Исаваитингремовал|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Число|Всего|Общее количество портов SNAT, использованных за период времени|Фронтендипаддресс, Баккендипаддресс, ProtocolType, Исаваитингремовал|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Число|Всего|Количество запросов, выполненных для зоны DNS|None|
|RecordSetCount|Количество наборов записей|Число|Максимальная|Количество наборов записей в зоне DNS|None|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
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
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Число|Максимальная|Выполняется ли атака DDoS|None|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|None|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|None|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|None|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Число|Среднее|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Число|Всего|Общее количество байтов, переданных за период времени|Порт, направление|
|PacketCount|Количество пакетов|Число|Всего|Общее количество пакетов, переданных за период времени|Порт, направление|
|SynCount|Количество пакетов SYN|Число|Всего|Общее количество пакетов SYN, переданных за период времени|Порт, направление|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|пингмешаверажераундтрипмс|Время кругового пути для проверки связи с виртуальной машиной|MilliSeconds|Среднее|Время кругового пути для проверки связи, отправленной на целевую виртуальную машину|Саурцекустомераддресс, Дестинатионкустомераддресс|
|пингмешпробесфаиледперцент|Неудачные проверки связи с виртуальной машиной|Процент|Среднее|Процент числа неудачных проверок связи для общего числа отправленных сообщений ping для целевой виртуальной машины|Саурцекустомераддресс, Дестинатионкустомераддресс|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|аппликатионрулехит|Число попаданий правил приложения|Число|Всего|Число попаданий в правилах приложения|Состояние, причина, протокол|
|нетворкрулехит|Число попаданий сетевых правил|Число|Всего|Число попаданий сетевых правил|Состояние, причина, протокол|
|фиреваллхеалс|Состояние работоспособности брандмауэра|Процент|Среднее|Состояние работоспособности брандмауэра|Состояние, причина|
|Обработка обработанных|Обработанные данные|Байты|Всего|Общий объем данных, обработанных брандмауэром|None|
|снатпортутилизатион|Использование портов SNAT|Процент|Среднее|Использование портов SNAT|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Среднее|Количество байтов в секунду, обрабатываемых шлюзом приложений|None|
|UnhealthyHostCount|Количество неработоспособных узлов|Число|Среднее|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Число|Среднее|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Число|Всего|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|авгрекуесткаунтперхеалсихост|Количество запросов в минуту на исправный узел|Число|Среднее|Среднее число запросов в минуту за каждый работоспособный хост-сервер в пуле|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Число|Всего|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Число|Всего|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Число|Всего|Число текущих установленных подключений к шлюзу приложений|None|
|График|Использование ЦП|Процент|Среднее|Текущая загрузка ЦП шлюзом приложений|None|
|капаЦитюнитс|Текущие единицы мощности|Число|Среднее|Потребленные единицы мощности|None|
|компутеунитс|Текущие единицы вычислений|Число|Среднее|Потребленные единицы вычислений|None|
|баккендреспонсестатус|Состояние ответа серверной части|Число|Всего|Количество кодов HTTP-ответов, создаваемых внутренними элементами. Сюда не входят коды ответов, созданные шлюзом приложений.|Баккендсервер, неработоспособных, BackendHttpSetting, Хттпстатусграуп|
|тлспротокол|Клиентский протокол TLS|Число|Всего|Количество запросов TLS и не-TLS, инициированных клиентом, который установил подключение к шлюзу приложений. Чтобы просмотреть распределение протокола TLS, отфильтруйте его по протоколу измерения TLS.|Прослушиватель, Тлспротокол|
|BytesSent|Отправлено байт|Байты|Всего|Общее число байтов, отправленных шлюзом приложений клиентам|Прослушиватель|
|BytesReceived|Получено байт|Байты|Всего|Общее число байтов, полученных шлюзом приложений от клиентов|Прослушиватель|
|клиентртт|RTT клиента|MilliSeconds|Среднее|Среднее время кругового пути между клиентами и шлюзом приложений. Эта метрика показывает, сколько времени требуется для установления подключений и возврата подтверждений.|Прослушиватель|
|аппликатионгатевайтоталтиме|Общее время шлюза приложений|MilliSeconds|Среднее|Среднее время, затрачиваемое на обработку запроса и отправку ответа. Это значение вычисляется как среднее по интервалу времени, когда шлюз приложений получает первый байт HTTP-запроса до момента завершения операции отправки ответа. Важно отметить, что это обычно включает время обработки шлюза приложений, время, в течение которого пакеты запросов и ответов перемещаются по сети, а также время ответа внутреннего сервера.|Прослушиватель|
|баккендконнекттиме|Время соединения серверной части|MilliSeconds|Среднее|Время, затраченное на установление соединения с внутренним сервером|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|баккендфирстбитереспонсетиме|Время ответа первого байта внутреннего сервера|MilliSeconds|Среднее|Интервал времени между началом установления соединения с внутренним сервером и получением первого байта заголовка ответа, приблизительного времени обработки внутреннего сервера|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|баккендластбитереспонсетиме|Время последнего ответа серверной части|MilliSeconds|Среднее|Интервал времени между началом установления соединения с внутренним сервером и получения последнего байта текста ответа|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|матчедкаунт|Общее распространение правила брандмауэра веб-приложения|Число|Всего|Общее распределение правил брандмауэра веб-приложения для входящего трафика|Группу правил, RuleId|
|блоккедкаунт|Распределение правил блокированных запросов брандмауэра веб-приложения|Число|Всего|Распределение правил блокированных запросов брандмауэра веб-приложения|Группу правил, RuleId|
|блоккедреккаунт|Число заблокированных запросов брандмауэра веб-приложения|Число|Всего|Число заблокированных запросов брандмауэра веб-приложения|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|Байт/с|Среднее|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|None|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|Байт/с|Среднее|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|None|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Число|Максимальная|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Среднее|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Всего|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Всего|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Число|Всего|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Число|Всего|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Число|Всего|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Число|Всего|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/Експрессраутепортс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ркслигхтлевел|ркслигхтлевел|Число|Среднее|Уровень источника RX в dBm|Связь, дорожка|
|ткслигхтлевел|ткслигхтлевел|Число|Среднее|Уровень источника TX в dBm|Связь, дорожка|
|админстате|админстате|Число|Среднее|Состояние администратора порта|Ссылка|
|линепротокол|линепротокол|Число|Среднее|Состояние протокола строки для порта|Ссылка|
|портбитсинперсеконд|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Ссылка|
|портбитсаутперсеконд|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Ссылка|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|пирингтипе|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|пирингтипе|
|глобалреачбитсинперсеконд|глобалреачбитсинперсеконд|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|пиредЦиркуитскэй|
|глобалреачбитсаутперсеконд|глобалреачбитсаутперсеконд|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|пиредЦиркуитскэй|
|бгпаваилабилити|Доступность BGP|Процент|Среднее|Доступность BGP от MSEE к всем одноранговым узлам.|Пирингтипе, одноранговая|
|арпаваилабилити|Доступность ARP|Процент|Среднее|Доступность ARP из MSEE для всех узлов.|Пирингтипе, одноранговая|
|косдропбитсинперсеконд|дроппединбитсперсеконд|Число/с|Среднее|Количество входящих бит данных, отбрасываемых в секунду|None|
|косдропбитсаутперсеконд|дроппедаутбитсперсеконд|Число/с|Среднее|Число отброшенных битов данных, отбрасываемых в секунду|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|None|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|None|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/Експрессраутегатевайс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ергатевайконнектионбитсинперсеконд|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|ConnectionName|
|ергатевайконнектионбитсаутперсеконд|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Число|Всего|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Число|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Процент|Среднее|Доля проб мониторинга подключения с ошибками, в процентах|None|
|AverageRoundtripMs|Среднее время приема-передачи (МС)|MilliSeconds|Среднее|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|None|
|чекксфаиледперцент|Процент неудачных проверок (Предварительная версия)|Процент|Среднее|% сбоев проверки подключения|Саурцеаддресс, SourceName, значение sourceresourceid, SourceType, Protocol, Дестинатионаддресс, имя назначения, Дестинатионресаурцеид, DestinationType, Дестинатионпорт, Тестграупнаме, TestConfigurationName|
|раундтриптимемс|Время приема-передачи (МС) (Предварительная версия)|MilliSeconds|Среднее|Время приема-передачи в миллисекундах для проверок мониторинга подключения|Саурцеаддресс, SourceName, значение sourceresourceid, SourceType, Protocol, Дестинатионаддресс, имя назначения, Дестинатионресаурцеид, DestinationType, Дестинатионпорт, Тестграупнаме, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Число|Всего|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|RequestSize|Размер запроса|Байты|Всего|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|ResponseSize|Размер ответа|Байты|Всего|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|биллаблереспонсесизе|Оплачиваемый размер ответа|Байты|Всего|Число оплачиваемых байтов (минимальное 2 КБ на запрос), отправленных в качестве ответов от прокси-сервера HTTP/S клиентам.|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|BackendRequestCount|Число запросов к серверному компоненту|Число|Всего|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|Состоянию HTTP, Хттпстатусграуп, серверная часть|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Среднее|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Серверная часть|
|TotalLatency|Total Latency (Общая задержка)|MilliSeconds|Среднее|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Процент|Среднее|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Серверная часть, неработоспособных|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Число|Всего|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, действие|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Число|Всего|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |None|
|registration.create|Операции создания регистрации|Число|Всего|Число всех успешных операций создания регистраций.|None|
|registration.update|Операции обновления регистрации|Число|Всего|Число всех успешных операций обновления регистраций.|None|
|registration.get|Операции чтения регистрации|Число|Всего|Число всех успешных запросов регистрации.|None|
|registration.delete|Операции удаления регистрации|Число|Всего|Число всех успешных операций удаления регистраций.|None|
|incoming|Входящие сообщения|Число|Всего|Число всех успешно отправленных вызовов API. |None|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Число|Всего|Отменено запланированных push-уведомлений|None|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Число|Всего|Отменено запланированных push-уведомлений|None|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Число|Всего|Запланированных уведомлений в состоянии ожидания|None|
|installation.all|Операции управления установкой|Число|Всего|Операции управления установкой|None|
|installation.get|Операции получения установки|Число|Всего|Операции получения установки|None|
|installation.upsert|Операции создания или обновления установки|Число|Всего|Операции создания или обновления установки|None|
|installation.patch|Операции исправления установки|Число|Всего|Операции исправления установки|None|
|installation.delete|Операции удаления установки|Число|Всего|Операции удаления установки|None|
|outgoing.allpns.success|Успешные уведомления|Число|Всего|Общее число успешных уведомлений.|None|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Число|Всего|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|None|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Число|Всего|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|None|
|outgoing.allpns.channelerror|Ошибки канала|Число|Всего|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|None|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Число|Всего|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|None|
|outgoing.wns.success|Успешные уведомления WNS|Число|Всего|Общее число успешных уведомлений.|None|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|None|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Число|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|None|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Число|Всего|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|None|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Число|Всего|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|None|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Число|Всего|Windows Live недоступна.|None|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Число|Всего|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|None|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Число|Всего|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|None|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Число|Всего|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|None|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Число|Всего|Полезные данные уведомления слишком большие (состояние WNS: 413).|None|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Число|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|None|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Число|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|None|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Число|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|None|
|outgoing.wns.pnserror|Ошибки WNS|Число|Всего|Уведомление не доставлено из-за ошибок связи с WNS.|None|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Число|Всего|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|None|
|outgoing.apns.success|Успешные уведомления APNS|Число|Всего|Общее число успешных уведомлений.|None|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Число|Всего|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|None|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Число|Всего|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|None|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Число|Всего|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|None|
|outgoing.apns.pnserror|Ошибки APNS|Число|Всего|Количество неудачных отправлений из-за ошибок связи с APNS.|None|
|outgoing.gcm.success|Успешные уведомления GCM|Число|Всего|Общее число успешных уведомлений.|None|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Число|Всего|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|None|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Число|Всего|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|None|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Число|Всего|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|None|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Число|Всего|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|None|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Число|Всего|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|None|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Число|Всего|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|None|
|outgoing.gcm.pnserror|Ошибки GCM|Число|Всего|Количество неудачных отправлений из-за ошибок связи с GCM.|None|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|None|
|outgoing.mpns.success|Успешные уведомления MPNS|Число|Всего|Общее число успешных уведомлений.|None|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Число|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|None|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Число|Всего|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|None|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Число|Всего|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|None|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Число|Всего|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|None|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Число|Всего|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|None|
|outgoing.mpns.pnserror|Ошибки MPNS|Число|Всего|Количество неудачных отправлений из-за ошибок связи с MPNS.|None|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Число|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|None|
|notificationhub.pushes|Все исходящие уведомления|Число|Всего|Все исходящие уведомления из центра уведомлений.|None|
|incoming.all.requests|Все входящие запросы|Число|Всего|Общее количество входящих запросов для концентратора уведомлений|None|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Число|Всего|Общее количество неудачных входящих запросов для концентратора уведомлений|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Процент свободных индексных дескрипторов|Число|Среднее|Average_% Free Inodes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Free Space|Процент свободного места|Число|Среднее|Average_% Free Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Inodes|Процент используемых индексных дескрипторов|Число|Среднее|Average_% Used Inodes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Space|Процент используемого места|Число|Среднее|Average_% Used Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Read Bytes/sec|Скорость чтения с диска (байт/с)|Число|Среднее|Average_Disk Read Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Reads/sec|Обращений чтения с диска/с|Число|Среднее|Average_Disk Reads/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Число|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Write Bytes/sec|Скорость записи на диск (байт/с)|Число|Среднее|Average_Disk Write Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Writes/sec|Обращений записи на диск/с|Число|Среднее|Average_Disk Writes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Megabytes|Свободно мегабайт|Число|Среднее|Average_Free Megabytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Logical Disk Bytes/sec|Скорость обмена с логическим диском, байт/с|Число|Среднее|Average_Logical Disk Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Available Memory|Процент доступной памяти|Число|Среднее|Average_% Available Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Available Swap Space|Процент доступной области подкачки|Число|Среднее|Average_% Available Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Memory|Процент используемой памяти|Число|Среднее|Average_% Used Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Swap Space|Процент используемой области подкачки|Число|Среднее|Average_% Used Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes Memory|Доступный объем памяти в МБ|Число|Среднее|Average_Available MBytes Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes Swap|Доступный объем подкачки в МБ|Число|Среднее|Average_Available MBytes Swap|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Page Reads/sec|Операций чтения со страницы в секунду|Число|Среднее|Average_Page Reads/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Page Writes/sec|Операций записи на страницу в секунду|Число|Среднее|Average_Page Writes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pages/sec|Страниц в секунду|Число|Среднее|Average_Pages/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used MBytes Swap Space|Используемая области подкачки в МБ|Число|Среднее|Average_Used MBytes Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used Memory MBytes|Используемый объем памяти в МБ|Число|Среднее|Average_Used Memory MBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes Transmitted|Всего передано байт|Число|Среднее|Average_Total Bytes Transmitted|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes Received|Всего получено байт|Число|Среднее|Average_Total Bytes Received|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes|Всего байт|Число|Среднее|Average_Total Bytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Packets Transmitted|Всего передано пакетов|Число|Среднее|Average_Total Packets Transmitted|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Packets Received|Всего получено пакетов|Число|Среднее|Average_Total Packets Received|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Rx Errors|Всего ошибок Rx|Число|Среднее|Average_Total Rx Errors|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Tx Errors|Всего ошибок Tx|Число|Среднее|Average_Total Tx Errors|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Collisions|Всего конфликтов|Число|Среднее|Average_Total Collisions|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Число|Среднее|Average_Avg. время чтения с диска (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время обращения к диску (с)|Среднее время обращения к диску (сек.)|Число|Среднее|Average_Avg. время обращения к диску (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (сек.)|Число|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Physical Disk Bytes/sec|Скорость обмена с физическим диском (байт/с)|Число|Среднее|Average_Physical Disk Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pct Privileged Time|Процент времени работы в привилегированном режиме|Число|Среднее|Average_Pct Privileged Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pct User Time|Процент времени пользователя|Число|Среднее|Average_Pct User Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used Memory kBytes|Используемая память в КБ|Число|Среднее|Average_Used Memory kBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Virtual Shared Memory|Виртуальная общая память|Число|Среднее|Average_Virtual Shared Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% DPC Time|Процент времени DPC|Число|Среднее|Average_% DPC Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Idle Time|Процент времени простоя|Число|Среднее|Average_% Idle Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Interrupt Time|Процент времени прерывания|Число|Среднее|Average_% Interrupt Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% IO Wait Time|Процент времени ожидания ввода-вывода|Число|Среднее|Average_% IO Wait Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Nice Time|Процент времени с низким приоритетом|Число|Среднее|Average_% Nice Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Privileged Time|Процент времени работы в привилегированном режиме|Число|Среднее|Average_% Privileged Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Processor Time|% загруженности процессора|Число|Среднее|Average_% Processor Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% User Time|Процент времени пользователя|Число|Среднее|Average_% User Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Physical Memory|Объем свободной физической памяти|Число|Среднее|Average_Free Physical Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Space in Paging Files|Объем свободного места в файлах подкачки|Число|Среднее|Average_Free Space in Paging Files|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Virtual Memory|Объем свободной виртуальной памяти|Число|Среднее|Average_Free Virtual Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Processes|Процессы|Число|Среднее|Average_Processes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Size Stored In Paging Files|Объем, сохраненный в файлах подкачки|Число|Среднее|Average_Size Stored In Paging Files|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Uptime|Время работы|Число|Среднее|Average_Uptime|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Users|Users|Число|Среднее|Average_Users|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Число|Среднее|Average_Avg. время чтения с диска (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (сек.)|Число|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Число|Среднее|Average_Current Disk Queue Length|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Reads/sec|Обращений чтения с диска/с|Число|Среднее|Average_Disk Reads/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Число|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Writes/sec|Обращений записи на диск/с|Число|Среднее|Average_Disk Writes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Megabytes|Свободно мегабайт|Число|Среднее|Average_Free Megabytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Free Space|Процент свободного места|Число|Среднее|Average_% Free Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes|Доступный объем в МБ|Число|Среднее|Average_Available MBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Число|Среднее|Average_% Committed Bytes In Use|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Received/sec|Полученных байтов/с|Число|Среднее|Average_Bytes Received/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Sent/sec|Отправленных байтов/с|Число|Среднее|Average_Bytes Sent/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Total/sec|Всего байтов/с|Число|Среднее|Average_Bytes Total/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Processor Time|% загруженности процессора|Число|Среднее|Average_% Processor Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Processor Queue Length|Длина очереди процессора.|Число|Среднее|Average_Processor Queue Length|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Пульс|Пульс|Число|Всего|Пульс|Computer, OSType, Version, Саурцекомпутерид|
|Update|Update|Число|Среднее|Update|Компьютер, продукт, классификация, Упдатестате, необязательный, утвержденный|
|Событие|Событие|Число|Среднее|Событие|Source, EventLog, Computer, Евенткатегори, Евентлевел, него eventlevelname, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. пиринг/Пирингсервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|префикслатенци|Задержка префикса|Миллисекунд|Среднее|Средняя задержка префикса|префикснаме|

## <a name="microsoftpeeringpeerings"></a>Microsoft. пиринг/пиринг

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Доступность сеанса v4|Процент|Среднее|Доступность сеанса v4|ConnectionId|
|SessionAvailabilityV6|Доступность сеанса V6|Процент|Среднее|Доступность сеанса V6|ConnectionId|
|ингресстраффикрате|Скорость входящего трафика|битсперсеконд|Среднее|Скорость входящего трафика в битах в секунду|ConnectionId|
|егресстраффикрате|Скорость исходящего трафика|битсперсеконд|Среднее|Скорость исходящего трафика в битах в секунду|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунд|Среднее|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Число|Среднее|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Число|Всего|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байты|Среднее|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|Нет измерений|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Число|Всего|Выполненные ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Число|Всего|ClientError в ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Число|Всего|ServerError в ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|Число|Всего|Выполненные SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Число|Всего|ClientError в SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Число|Всего|ServerError в SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Число|Всего|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Число|Всего|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Число|Всего|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Число|Всего|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Число|Всего|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Число|Всего|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Число|Всего|Всего SenderDisconnects для Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Среднее|Среднее время задержки поиска для службы поиска.|None|
|SearchQueriesPerSecond|Search queries per second|Число/с|Среднее|Число поисковых запросов в секунду для службы поиска.|None|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Среднее|Процент отрегулированных поисковых запросов для службы поиска.|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Число|Всего|Общее количество успешных запросов для пространства имен|EntityName, OperationResult|
|ServerErrors|Ошибки сервера.|Число|Всего|Ошибки на сервере для Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Ошибки пользователей.|Число|Всего|Ошибки пользователей для Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Регулируемые запросы.|Число|Всего|Регулируемые запросы для Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Входящих запросов|Число|Всего|Входящие запросы для Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Входящие сообщения|Число|Всего|Входящие сообщения для Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Исходящие сообщения|Число|Всего|Исходящие сообщения для Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Число|Всего|Число активных подключений для Microsoft.ServiceBus.|None|
|ConnectionsOpened|Открытые подключения.|Число|Среднее|Открытые подключения для Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Число|Среднее|Закрытые подключения для Microsoft.ServiceBus.|EntityName|
|Размер|Размер|Байты|Среднее|Размер очереди или раздела в байтах.|EntityName|
|Сообщения|Число сообщений в очереди или разделе.|Число|Среднее|Число сообщений в очереди или разделе.|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе.|Число|Среднее|Число активных сообщений в очереди или разделе.|EntityName|
|деадлеттередмессажес|Количество недоставленных сообщений в очереди или разделе.|Число|Среднее|Количество недоставленных сообщений в очереди или разделе.|EntityName|
|счедуледмессажес|Число запланированных сообщений в очереди или разделе.|Число|Среднее|Число запланированных сообщений в очереди или разделе.|EntityName|
|намеспацекпуусаже|ЦП|Процент|Максимальная|Метрика использования ЦП для пространства имен Premium служебной шины.|Реплика|
|намеспацемеморюсаже|Использование памяти|Процент|Максимальная|Метрика использования памяти пространства имен Premium служебной шины.|Реплика|
|CPUXNS|ЦП (не рекомендуется)|Процент|Максимальная|Метрика использования ЦП для пространства имен Premium служебной шины. Эта метрика — поддерживается. Используйте вместо этого метрику ЦП (Намеспацекпуусаже).|Реплика|
|WSXNS|Использование памяти (не рекомендуется)|Процент|Максимальная|Метрика использования памяти пространства имен Premium служебной шины. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику "использование памяти" (Намеспацемеморюсаже).|Реплика|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. Сервицефабрикмеш/приложения

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|аллокатедкпу|аллокатедкпу|Число|Среднее|ЦП, выделенный этому контейнеру в миллисекундах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|аллокатедмемори|аллокатедмемори|Байты|Среднее|Память, выделенная для этого контейнера, в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|актуалкпу|актуалкпу|Число|Среднее|Фактическое использование ЦП в миллисекундах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|актуалмемори|актуалмемори|Байты|Среднее|Фактическое использование памяти в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|График|График|Процент|Среднее|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|меморютилизатион|меморютилизатион|Процент|Среднее|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|аппликатионстатус|аппликатионстатус|Число|Среднее|Состояние Service Fabric приложения сетки|ApplicationName, состояние|
|сервицестатус|сервицестатус|Число|Среднее|Состояние работоспособности службы в Service Fabric приложении для сетки|ApplicationName, состояние, ServiceName|
|сервицерепликастатус|сервицерепликастатус|Число|Среднее|Состояние работоспособности реплики службы в Service Fabric приложении сети|ApplicationName, состояние, ServiceName, Сервицерепликанаме|
|контаинерстатус|контаинерстатус|Число|Среднее|Состояние контейнера в Service Fabric приложении сетки|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме, состояние|
|рестарткаунт|рестарткаунт|Число|Среднее|Число перезапусков контейнера в Service Fabric приложении сетки|ApplicationName, Status, ServiceName, Сервицерепликанаме, Кодепаккаженаме|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Число|Максимальная|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Количество сообщений|Число|Всего|Общее количество сообщений.|None|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байты|Всего|Входящий трафик службы|None|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байты|Всего|Исходящий трафик службы|None|
|UserErrors|Ошибки пользователей|Процент|Максимальная|Процент ошибок пользователей|None|
|SystemErrors|Системные ошибки|Процент|Максимальная|Процент системных ошибок|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|None|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|None|
|log_write_percent|Log IO percentage|Процент|Среднее|Процент операций ввода-вывода журнала. Неприменимо к хранилищам данных.|None|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент DTU. Применяется к базам данных на основе DTU.|None|
|носителей.|Используемое пространство данных|Байты|Максимальная|Используемое пространство данных. Неприменимо к хранилищам данных.|None|
|connection_successful|Успешные подключения|Число|Всего|Успешные подключения|None|
|connection_failed|Неудачные подключения|Число|Всего|Неудачные подключения|None|
|blocked_by_firewall|Заблокировано брандмауэром|Число|Всего|Заблокировано брандмауэром|None|
|взаимоблокировка|Взаимоблокировки|Число|Всего|Взаимоблокировок. Неприменимо к хранилищам данных.|None|
|storage_percent|Процент использования пространства данных|Процент|Максимальная|Процент использования пространства данных. Неприменимо к хранилищам данных или базам данных с масштабированием.|None|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранения выполняющейся в памяти OLTP. Неприменимо к хранилищам данных.|None|
|workers_percent|Workers percentage|Процент|Среднее|Процент рабочих ролей. Неприменимо к хранилищам данных.|None|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент сеансов. Неприменимо к хранилищам данных.|None|
|dtu_limit|Лимит DTU|Число|Среднее|Предел DTU. Применяется к базам данных на основе DTU.|None|
|dtu_used|DTU used|Число|Среднее|Использованные DTU. Применяется к базам данных на основе DTU.|None|
|cpu_limit|Ограничение ЦП|Число|Среднее|Ограничение ЦП. Применяется к базам данных на основе виртуальное ядро.|None|
|cpu_used|Используемый ЦП|Число|Среднее|ЦП используется. Применяется к базам данных на основе виртуальное ядро.|None|
|dwu_limit|Лимит DWU|Число|Максимальная|Ограничение DWU. Применяется только к хранилищам данных.|None|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU в процентах. Применяется только к хранилищам данных.|None|
|dwu_used|DWU used|Число|Максимальная|Используется DWU. Применяется только к хранилищам данных.|None|
|cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Процент|Максимальная|Процент попаданий в кэш. Применяется только к хранилищам данных.|None|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Процент|Максимальная|Процент использованного кэша. Применяется только к хранилищам данных.|None|
|sqlserver_process_core_percent|Процент ядра процесса SQL Server|Процент|Максимальная|Процент использования ЦП для SQL Server процесса, измеряемый операционной системой. В настоящее время доступно только для серверных баз данных.|None|
|sqlserver_process_memory_percent|Процент памяти процесса SQL Server|Процент|Максимальная|Процент использования памяти для процесса SQL Server, измеряемый операционной системой. В настоящее время доступно только для серверных баз данных.|None|
|tempdb_data_size|Размер файла данных tempdb, КБ|Число|Максимальная|Размер файла данных tempdb в килобайтах. Неприменимо к хранилищам данных.|None|
|tempdb_log_size|Размер файла журнала tempdb, КБ|Число|Максимальная|Размер файла журнала tempdb в килобайтах. Неприменимо к хранилищам данных.|None|
|tempdb_log_used_percent|Используемый журнал в процентах tempdb|Процент|Максимальная|Используемый журнал в процентах tempdb. Неприменимо к хранилищам данных.|None|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Процент|Среднее|Процент локальной базы данных tempdb. Применяется только к хранилищам данных.|None|
|app_cpu_billed|Выплата за ЦП приложения|Число|Всего|Выплата за ЦП приложения. Применяется к бессерверным базам данных.|None|
|app_cpu_percent|Процент загрузки ЦП приложения|Процент|Среднее|Процент загрузки ЦП приложения. Применяется к бессерверным базам данных.|None|
|app_memory_percent|Процент памяти приложения|Процент|Среднее|Процент памяти приложения. Применяется к бессерверным базам данных.|None|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное хранилище данных. Неприменимо к хранилищам данных.|None|
|memory_usage_percent|Процент памяти|Процент|Максимальная|Процент памяти. Применяется только к хранилищам данных.|None|
|full_backup_size_bytes|Полный размер хранилища резервных копий|Байты|Максимальная|Совокупный размер хранилища полного резервного копирования. Применяется к базам данных на основе виртуальное ядро. Неприменимо к базам данных с масштабированием.|None|
|diff_backup_size_bytes|Разностный размер хранилища резервных копий|Байты|Максимальная|Совокупный разностный размер хранилища резервных копий. Применяется к базам данных на основе виртуальное ядро. Неприменимо к базам данных с масштабированием.|None|
|log_backup_size_bytes|Размер хранилища резервных копий журналов|Байты|Максимальная|Совокупный размер хранилища резервных копий журналов. Применяется к базам данных на основе виртуальное ядро. Неприменимо к базам данных с масштабированием.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|None|
|database_cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|DatabaseResourceId|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|None|
|database_physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|DatabaseResourceId|
|log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|None|
|database_log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|DatabaseResourceId|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент DTU. Применяется к эластичным пулам на основе DTU.|None|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|DatabaseResourceId|
|storage_percent|Процент использования пространства данных|Процент|Среднее|Процент использования пространства данных|None|
|workers_percent|Workers percentage|Процент|Среднее|Workers percentage|None|
|database_workers_percent|Workers percentage|Процент|Среднее|Workers percentage|DatabaseResourceId|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|None|
|database_sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|DatabaseResourceId|
|eDTU_limit|eDTU limit|Число|Среднее|предел eDTU. Применяется к эластичным пулам на основе DTU.|None|
|storage_limit|Максимальный размер данных|Байты|Среднее|Максимальный размер данных|None|
|eDTU_used|eDTU used|Число|Среднее|используемые eDTU. Применяется к эластичным пулам на основе DTU.|None|
|database_eDTU_used|eDTU used|Число|Среднее|eDTU used|DatabaseResourceId|
|storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|None|
|database_storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|DatabaseResourceId|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранилища выполняющейся в памяти OLTP|None|
|cpu_limit|Ограничение ЦП|Число|Среднее|Ограничение ЦП. Применяется к эластичным пулам на основе виртуальное ядро.|None|
|database_cpu_limit|Ограничение ЦП|Число|Среднее|Ограничение ЦП|DatabaseResourceId|
|cpu_used|Используемый ЦП|Число|Среднее|ЦП используется. Применяется к эластичным пулам на основе виртуальное ядро.|None|
|database_cpu_used|Используемый ЦП|Число|Среднее|Используемый ЦП|DatabaseResourceId|
|sqlserver_process_core_percent|Процент ядра процесса SQL Server|Процент|Максимальная|Загрузка ЦП в процентах от процесса базы данных SQL. Применяется к эластичным пулам.|None|
|sqlserver_process_memory_percent|Процент памяти процесса SQL Server|Процент|Максимальная|Использование памяти в процентах от процесса базы данных SQL. Применяется к эластичным пулам.|None|
|tempdb_data_size|Размер файла данных tempdb, КБ|Число|Максимальная|Размер файла данных tempdb, КБ|None|
|tempdb_log_size|Размер файла журнала tempdb, КБ|Число|Максимальная|Размер файла журнала tempdb, КБ|None|
|tempdb_log_used_percent|Используемый журнал в процентах tempdb|Процент|Максимальная|Используемый журнал в процентах tempdb|None|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|None|
|database_allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|DatabaseResourceId|
|allocated_data_storage_percent|Процент выделенного пространства данных|Процент|Максимальная|Процент выделенного пространства данных|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|Датабасересаурцеид, Еластикпулресаурцеид|
|storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|ElasticPoolResourceId|
|database_storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|Датабасересаурцеид, Еластикпулресаурцеид|
|dtu_used|DTU used|Число|Среднее|DTU used|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Число|Среднее|Virtual core count (Число виртуальных ядер)|None|
|avg_cpu_percent|Average CPU percentage (Средний процент использования ЦП)|Процент|Среднее|Average CPU percentage (Средний процент использования ЦП)|None|
|reserved_storage_mb|Зарезервированное дисковое пространство|Число|Среднее|Зарезервированное дисковое пространство|None|
|storage_space_used_mb|Использованное дисковое пространство|Число|Среднее|Использованное дисковое пространство|None|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Число|Среднее|IO requests count (Количество запросов ввода-вывода)|None|
|io_bytes_read|Количество считанных байт ввода-вывода|Байты|Среднее|Количество считанных байт ввода-вывода|None|
|io_bytes_written|Количество записанных байт ввода-вывода|Байты|Среднее|Количество записанных байт ввода-вывода|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Число|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Число|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|None|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемый ADLS 2-го поколения (иерархический) индекс в байтах.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|None|
|TableCount|Количество таблиц|Число|Среднее|Количество таблиц в службе таблиц учетной записи хранения.|None|
|TableEntityCount|Количество сущностей таблиц|Число|Среднее|Количество сущностей таблиц в службе таблиц учетной записи хранения.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|FileShare|
|FileCount|Количество файлов|Число|Среднее|Количество файлов в службе файлов учетной записи хранения.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Число|Среднее|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|None|
|филешареснапшоткаунт|Число моментальных снимков общей папки|Число|Среднее|Количество моментальных снимков, имеющихся в службе файлов учетной записи хранения в общей папке.|FileShare|
|филешареснапшотсизе|Размер моментального снимка файлового ресурса|Байты|Среднее|Объем хранилища, используемый моментальными снимками в файловой службе учетной записи хранения в байтах.|FileShare|
|филешарекуота|Размер квоты общей папки|Байты|Среднее|Верхний предел объема хранилища, который может использоваться службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности, Общая папка|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности, Общая папка|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|None|
|QueueCount|Количество очередей|Число|Среднее|Количество очередей в службе очередей учетной записи хранения.|None|
|QueueMessageCount|Количество сообщений очереди|Число|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|None|
|Transactions|Transactions|Число|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунд|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунд|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|





## <a name="microsoftstoragecachecaches"></a>Microsoft. Сторажекаче/кэши

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|клиентиопс|Всего операций ввода-вывода клиента|Число|Среднее|Частота операций с клиентскими файлами, обрабатываемых кэшем.|None|
|клиентлатенци|Средняя задержка клиента|Миллисекунд|Среднее|Средняя задержка операций файлового файла клиента в кэш хранилища.|None|
|клиентреадиопс|Операции чтения клиента в секунду|Число/с|Среднее|Количество операций чтения клиента в секунду.|None|
|клиентреадсраугхпут|Средняя пропускная способность чтения кэша|Байт/с|Среднее|Скорость передачи данных при чтении клиента.|None|
|клиентвритеиопс|Число операций записи клиента в секунду|Число/с|Среднее|Количество операций записи клиента в секунду.|None|
|клиентвритесраугхпут|Средняя пропускная способность записи в кэш|Байт/с|Среднее|Скорость передачи данных для клиента.|None|
|клиентметадатареадиопс|ОПЕРАЦИЙ чтения метаданных клиента|Число/с|Среднее|Частота операций с клиентскими файлами, отправленных в кэш, за исключением операций чтения данных, которые не изменяют постоянное состояние.|None|
|клиентметадатавритеиопс|Запись метаданных клиента в операции ввода-вывода|Число/с|Среднее|Частота операций с клиентскими файлами, отправляемых в кэш, за исключением операций записи данных, которые изменяют постоянное состояние.|None|
|клиентлоккиопс|Операции ввода-вывода блокировки клиента|Число/с|Среднее|Количество операций блокировки файлов клиента в секунду.|None|
|сторажетаржесеалс|Работоспособность целевого объекта хранилища|Число|Среднее|Логические результаты проверки подключения между целевыми объектами хранилища и кэша.|None|
|Время работы|Время работы|Число|Среднее|Логические результаты проверки подключения между системой кэша и мониторинга.|None|
|сторажетаржетиопс|Всего Сторажетаржет операций ввода-вывода|Число|Среднее|Частота операций с файлами, которые кэш отправляет определенному Сторажетаржету.|сторажетаржет|
|сторажетаржетвритеиопс|Сторажетаржет запись в секунду|Число|Среднее|Частота операций записи в файл, отправляемых кэшем в определенный Сторажетаржет.|сторажетаржет|
|сторажетаржетасинквритесраугхпут|Пропускная способность асинхронной записи Сторажетаржет|Байт/с|Среднее|Скорость, с которой кэш асинхронно записывает данные в определенный Сторажетаржет. Это уступающей операции записи, которые не приводят к блокировке клиентов.|сторажетаржет|
|сторажетаржетсинквритесраугхпут|Пропускная способность синхронной записи Сторажетаржет|Байт/с|Среднее|Скорость, с которой кэш синхронно записывает данные в определенный Сторажетаржет. Это операции записи, которые приводят к блокировке клиентов.|сторажетаржет|
|сторажетаржеттоталвритесраугхпут|Общая пропускная способность записи Сторажетаржет|Байт/с|Среднее|Общая частота, с которой кэш записывает данные в определенный Сторажетаржет.|сторажетаржет|
|сторажетаржетлатенци|Задержка Сторажетаржет|Миллисекунд|Среднее|Средняя задержка кругового пути для всех операций с файлами, которые кэш отправляет в партрикулар Сторажетаржет.|сторажетаржет|
|сторажетаржетметадатареадиопс|Сторажетаржет чтение метаданных операций ввода-вывода|Число/с|Среднее|Частота операций с файлами, которые не изменяют постоянное состояние и не выполняют операцию чтения, которые кэш отправляет определенному Сторажетаржет.|сторажетаржет|
|сторажетаржетметадатавритеиопс|Сторажетаржет запись метаданных в секунду|Число/с|Среднее|Частота операций с файлами, которые выполняют изменение постоянного состояния и исключают операцию записи, которые кэшируются в определенный Сторажетаржет.|сторажетаржет|
|сторажетаржетреадиопс|Сторажетаржет чтение операций ввода-вывода|Число/с|Среднее|Частота операций чтения файлов, которые кэш отправляет определенному Сторажетаржету.|сторажетаржет|
|сторажетаржетреадахеадсраугхпут|Пропускная способность чтения Сторажетаржет|Байт/с|Среднее|Скорость, с которой оппортунистикли кэш считывает данные из Сторажетаржет.|сторажетаржет|
|сторажетаржетфиллсраугхпут|Пропускная способность заполнения Сторажетаржет|Байт/с|Среднее|Скорость, с которой кэш считывает данные из Сторажетаржет для управления промахом кэша.|сторажетаржет|
|сторажетаржеттоталреадсраугхпут|Общая пропускная способность чтения Сторажетаржет|Байт/с|Среднее|Общая скорость, с которой кэш считывает данные из определенного Сторажетаржет.|сторажетаржет|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/Сторажесинксервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|серверсинксессионресулт|Результат сеанса синхронизации|Число|Среднее|Метрика, которая регистрирует значение 1 каждый раз, когда конечная точка сервера успешно завершает сеанс синхронизации с конечной точкой облака.|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксинксессионапплиедфилескаунт|Синхронизировано файлов|Число|Всего|Число синхронизированных файлов|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксинксессионперитемеррорскаунт|Несинхронизирующиеся файлы|Число|Всего|Число файлов, которые не удалось синхронизировать|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинкбатчтрансферредфилебитес|Синхронизировано байт|Байты|Всего|Общий размер файлов, переданных для сеансов синхронизации|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|сторажесинксерверхеартбеат|Состояние сервера в сети|Число|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер ресигтеред успешно записывает пульс в облачную конечную точку.|ServerName|
|сторажесинкрекаллиототалсизебитес|Отзыв уровней в облаке|Байты|Всего|Общий размер данных, отозванных сервером|ServerName|
|сторажесинкрекалледтоталнетворкбитес|Размер отзыва при уровне облака|Байты|Всего|размер отозванных данных;|Синкграупнаме, ServerName|
|сторажесинкрекаллсраугхпутбитесперсеконд|Пропускная способность отзыва облачного уровня|Байт/с|Среднее|Размер пропускной способности при отзыве данных|Синкграупнаме, ServerName|
|сторажесинкрекалледнетворкбитесбяппликатион|Размер отзыва по уровням облака по приложениям|Байты|Всего|Размер данных, отозванных приложением|Синкграупнаме, ServerName, имяПриложения|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/Сторажесинксервицес/Синкграупс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|синкграупсинксессионапплиедфилескаунт|Синхронизировано файлов|Число|Всего|Число синхронизированных файлов|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|синкграупсинксессионперитемеррорскаунт|Несинхронизирующиеся файлы|Число|Всего|Число файлов, которые не удалось синхронизировать|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|синкграупбатчтрансферредфилебитес|Синхронизировано байт|Байты|Всего|Общий размер файлов, переданных для сеансов синхронизации|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|серверендпоинтсинксессионапплиедфилескаунт|Синхронизировано файлов|Число|Всего|Число синхронизированных файлов|Серверендпоинтнаме, Синкдиректион|
|серверендпоинтсинксессионперитемеррорскаунт|Несинхронизирующиеся файлы|Число|Всего|Число файлов, которые не удалось синхронизировать|Серверендпоинтнаме, Синкдиректион|
|серверендпоинтбатчтрансферредфилебитес|Синхронизировано байт|Байты|Всего|Общий размер файлов, переданных для сеансов синхронизации|Серверендпоинтнаме, Синкдиректион|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|серверхеартбеат|Состояние сервера в сети|Число|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер ресигтеред успешно записывает пульс в облачную конечную точку.|Серверресаурцеид, ServerName|
|серверрекаллиототалсизебитес|Отзыв уровней в облаке|Байты|Всего|Общий размер данных, отозванных сервером|Серверресаурцеид, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|LogicalName, PartitionId|
|InputEvents|Входные события|Число|Всего|Входные события|LogicalName, PartitionId|
|InputEventBytes|Байты входного события|Байты|Всего|Байты входного события|LogicalName, PartitionId|
|LateInputEvents|События позднего ввода|Число|Всего|События позднего ввода|LogicalName, PartitionId|
|OutputEvents|Выходные события|Число|Всего|Выходные события|LogicalName, PartitionId|
|ConversionErrors|Ошибки преобразования данных|Число|Всего|Ошибки преобразования данных|LogicalName, PartitionId|
|ошибки|Ошибки среды выполнения|Число|Всего|Ошибки среды выполнения|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Неупорядоченные события|Число|Всего|Неупорядоченные события|LogicalName, PartitionId|
|AMLCalloutRequests|Запросы функций|Число|Всего|Запросы функций|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Число|Всего|Неудачные запросы функций|LogicalName, PartitionId|
|AMLCalloutInputEvents|События функций|Число|Всего|События функций|LogicalName, PartitionId|
|DeserializationError|Ошибки десериализации входа|Число|Всего|Ошибки десериализации входа|LogicalName, PartitionId|
|EarlyInputEvents|Ранние входные события|Число|Всего|Ранние входные события|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимальная|Предельная задержка|LogicalName, PartitionId|
|инпутевентссаурцесбакклогжед|Необработанные входные события|Число|Максимальная|Необработанные входные события|LogicalName, PartitionId|
|инпутевентссаурцесперсеконд|Полученные входные источники|Число|Всего|Полученные входные источники|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Число|Всего|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|None|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Число|Всего|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|None|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Всего|Количество байтов, полученных от всех источников событий|None|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Всего|Общий размер событий, успешно обработанных и доступных для запросов|None|
|IngressStoredEvents|Хранится событий входящих данных|Число|Всего|Количество сведенных событий, успешно обработанных и доступных для запросов|None|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|None|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Число|Среднее|Разница между порядковым номером последней поставленной в очередь сообщения в секции источника событий и порядковым номером сообщений, обрабатываемых в входящих данных|None|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Число|Максимальная|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|None|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Число|Максимальная|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Число|Всего|Количество сообщений, считанных из источника событий|None|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Число|Всего|Количество недопустимых сообщений, считанных из источника событий|None|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Всего|Количество байтов, считанных из источника событий|None|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Всего|Общий размер событий, успешно обработанных и доступных для запросов|None|
|IngressStoredEvents|Хранится событий входящих данных|Число|Всего|Количество сведенных событий, успешно обработанных и доступных для запросов|None|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|None|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Число|Среднее|Разница между порядковым номером последней поставленной в очередь сообщения в секции источника событий и порядковым номером сообщений, обрабатываемых в входящих данных|None|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Число|Максимальная|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|None|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Число|Максимальная|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. Вмвареклаудсимпле/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|дискреадбитесперсеконд|Disk Read Bytes/Sec|Байт/с|Среднее|Средняя пропускная способность диска из-за операций чтения за период выборки.|None|
|дисквритебитесперсеконд|Disk Write Bytes/Sec|Байт/с|Среднее|Средняя пропускная способность диска из-за операций записи за период выборки.|None|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общая пропускная способность диска из-за операций чтения за период выборки.|None|
|Disk Write Bytes|Disk Write Bytes|Байты|Всего|Общая пропускная способность диска из-за операций записи за период выборки.|None|
|дискреадоператионс|Операции чтения с диска|Число|Всего|Число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|None|
|дисквритеоператионс|Операции записи на диск|Число|Всего|Число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Среднее число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Среднее число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|None|
|дискреадлатенци|Задержка чтения с диска|Миллисекунд|Среднее|Общая задержка чтения. Сумма задержек чтения устройства и ядра.|None|
|дискврителатенци|Задержка записи на диск|Миллисекунд|Среднее|Общая задержка записи. Сумма задержек при записи в устройство и ядро.|None|
|нетворкинбитесперсеконд|Сеть в байтах/с|Байт/с|Среднее|Средняя пропускная способность сети для полученного трафика.|None|
|нетворкаутбитесперсеконд|Сетевых исходящих байт/с|Байт/с|Среднее|Средняя пропускная способность сети для передаваемого трафика.|None|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Общая пропускная способность сети для полученного трафика.|None|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Общая пропускная способность сети для передаваемого трафика.|None|
|MemoryUsed|Используемая память|Байты|Среднее|Объем памяти компьютера, используемой виртуальной машиной.|None|
|меморигрантед|Предоставленная память|Байты|Среднее|Объем памяти, предоставленной виртуальной машине узлом. Память не предоставляется узлу до тех пор, пока она не будет затронута один раз и выделенная память может быть переключена или выведена из появления, если Вмкернел нуждается в памяти.|None|
|меморяктиве|Память активна|Байты|Среднее|Объем памяти, используемой виртуальной машиной в прошлом маленьком окне времени. Это "истинное" количество памяти, которое в настоящее время требуется виртуальной машине. Дополнительная неиспользуемая память может быть переключена или повышена, что не влияет на производительность гостевой системы.|None|
|Percentage CPU|Percentage CPU|Процент|Среднее|Загрузка ЦП. Это значение указывается в 100%, представляющем все ядра процессора в системе. Например, 2-сторонняя виртуальная машина, использующая 50% системы из четырех ядер, полностью использует два ядра.|None|
|перцентажекпуреади|Процент готовности ЦП|Миллисекунд|Всего|Время готовности — это время, затрачиваемое на ожидание доступности ЦП (ов) за последний интервал обновления.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Число|Среднее|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Число|Среднее|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|ткпсинсент|TCP SYN отправлен|Число|Среднее|TCP SYN отправлен|Экземпляр|
|ткпсинрецеивед|Получен TCP SYN|Число|Среднее|Получен TCP SYN|Экземпляр|
|ткпестаблишед|Установлен TCP|Число|Среднее|Установлен TCP|Экземпляр|
|TcpFinWait1|Ожидание TCP FIN 1|Число|Среднее|Ожидание TCP FIN 1|Экземпляр|
|TcpFinWait2|Ожидание TCP FIN 2|Число|Среднее|Ожидание TCP FIN 2|Экземпляр|
|ткпклосинг|TCP-закрытие|Число|Среднее|TCP-закрытие|Экземпляр|
|ткпклосеваит|Ожидание TCP-закрытия|Число|Среднее|Ожидание TCP-закрытия|Экземпляр|
|ткпластакк|Последнее подтверждение TCP|Число|Среднее|Последнее подтверждение TCP|Экземпляр|
|ткптимеваит|Время ожидания TCP|Число|Среднее|Время ожидания TCP|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|Экземпляр|
|Requests|Requests|Число|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Число|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Число|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Число|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Число|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Число|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Число|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Число|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Число|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Число|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|AppConnections|Соединения|Число|Среднее|Соединения|Экземпляр|
|Маркеры|Число дескрипторов|Число|Среднее|Число дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Число|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Число|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Число|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Число|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Число|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Число|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Число|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Число|Всего|Сборок мусора поколения 2|Экземпляр|
|хеалсчеккстатус|Состояние проверки работоспособности|Число|Среднее|Состояние проверки работоспособности|Экземпляр|
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|None|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Число|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|МБ/МС|Всего|[Единицы выполнения функции](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Число|Всего|Function Execution Count|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Число|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Число|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Число|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Число|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Число|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Число|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Число|Всего|Сборок мусора поколения 2|Экземпляр|
|хеалсчеккстатус|Состояние проверки работоспособности|Число|Среднее|Состояние проверки работоспособности|Экземпляр|
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|None|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|Экземпляр|
|Requests|Requests|Число|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Число|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Число|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Число|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Число|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Число|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Число|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Число|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Число|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Число|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|хттпреспонсетиме|Время отклика|Секунды|Среднее|Время отклика|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Число|Всего|Function Execution Units|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Число|Всего|Function Execution Count|Экземпляр|
|AppConnections|Соединения|Число|Среднее|Соединения|Экземпляр|
|Маркеры|Число дескрипторов|Число|Среднее|Число дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Число|Среднее|Счетчик потоков|Экземпляр|
|PrivateBytes|Байты исключительного пользования|Байты|Среднее|Байты исключительного пользования|Экземпляр|
|IoReadBytesPerSecond|Операции ввода-вывода: чтение, байт в секунду|Байт/с|Всего|Операции ввода-вывода: чтение, байт в секунду|Экземпляр|
|IoWriteBytesPerSecond|Операции ввода-вывода: запись, байт в секунду|Байт/с|Всего|Операции ввода-вывода: запись, байт в секунду|Экземпляр|
|IoOtherBytesPerSecond|Операции ввода-вывода: прочие, байт в секунду|Байт/с|Всего|Операции ввода-вывода: прочие, байт в секунду|Экземпляр|
|IoReadOperationsPerSecond|Операции ввода-вывода: операций чтения в секунду|Байт/с|Всего|Операции ввода-вывода: операций чтения в секунду|Экземпляр|
|IoWriteOperationsPerSecond|Операции ввода-вывода: операций записи в секунду|Байт/с|Всего|Операции ввода-вывода: операций записи в секунду|Экземпляр|
|IoOtherOperationsPerSecond|Операции ввода-вывода: прочих операций в секунду|Байт/с|Всего|Операции ввода-вывода: прочих операций в секунду|Экземпляр|
|RequestsInApplicationQueue|Запросов в очереди приложений|Число|Среднее|Запросов в очереди приложений|Экземпляр|
|CurrentAssemblies|Текущее число сборок|Число|Среднее|Текущее число сборок|Экземпляр|
|TotalAppDomains|Всего доменов приложений|Число|Среднее|Всего доменов приложений|Экземпляр|
|TotalAppDomainsUnloaded|Всего выгруженных доменов приложений|Число|Среднее|Всего выгруженных доменов приложений|Экземпляр|
|Gen0Collections|Сборок мусора поколения 0|Число|Всего|Сборок мусора поколения 0|Экземпляр|
|Gen1Collections|Сборок мусора поколения 1|Число|Всего|Сборок мусора поколения 1|Экземпляр|
|Gen2Collections|Сборок мусора поколения 2|Число|Всего|Сборок мусора поколения 2|Экземпляр|
|хеалсчеккстатус|Состояние проверки работоспособности|Число|Среднее|Состояние проверки работоспособности|Экземпляр|
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|Requests|Requests|Число|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Число|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Число|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Число|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Число|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Число|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Число|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Число|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Число|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Число|Всего|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Число|Среднее|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Число|Среднее|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Число|Всего|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Число|Среднее|Общее число внешних интерфейсов|None|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Число|Среднее|Рабочие роли плана службы приложений уровня "Малый"|None|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Число|Среднее|Рабочие процессы плана службы приложений уровня "Средний"|None|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Число|Среднее|Рабочие процессы плана службы приложений уровня "Крупный"|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Description|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Число|Среднее|Общее количество рабочих процессов|None|
|WorkersAvailable|Доступные рабочие процессы|Число|Среднее|Доступные рабочие процессы|None|
|WorkersUsed|Использованные рабочие процессы|Число|Среднее|Использованные рабочие процессы|None|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
## <a name="next-steps"></a>Дальнейшие действия
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создайте оповещения на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](platform-logs-overview.md)
