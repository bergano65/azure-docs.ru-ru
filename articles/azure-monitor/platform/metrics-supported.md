---
title: Поддерживаемые метрики Azure Monitor, доступные для каждого типа ресурса
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 7ed55200db72879f7ec7323b5c165998944688a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906791"
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor

> [!NOTE]
> Этот список в основном создается автоматически на основе метрик Azure Monitor REST API. Любые изменения, внесенные в этот список через GitHub, могут быть записаны без предупреждения. Обратитесь к автору этой статьи за сведениями о том, как выполнять постоянные обновления.

Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. 

В этой статье приведен полный список метрик для всех платформ (то есть автоматически собираемых), доступных в настоящее время в консолидированном конвейере метрик Azure Monitor. Список был последним обновлен в марте 27, 2020. Метрики, измененные или добавленные после этой даты, могут не отображаться ниже. Чтобы запросить список метрик и получить к ним доступ программным способом, используйте [версию api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Другие метрики, не включенные в этот список, могут быть доступны на портале или с помощью API прежних версий.

Метрики упорядочены по поставщикам ресурсов и типам ресурсов. Список служб и поставщиков ресурсов, принадлежащих к ним, см. в статье [поставщики ресурсов для служб Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Метрики гостевой ОС

Метрики для гостевой операционной системы (гостевой ОС), которая выполняется на виртуальных машинах Azure, Service Fabric и облачных служб, **не** перечислены здесь. Вместо этого необходимо собрать метрики производительности гостевой ОС с помощью одного или нескольких агентов, которые работают в или в составе гостевой операционной системы.  Метрики гостевой ОС включают счетчики производительности, которые следят за процентом использования ЦП или памятью в виртуальной машине, которые часто используются для автоматического масштабирования или оповещения.  С помощью [расширения система диагностики Azure](diagnostics-extension-overview.md)можно отправить метрики производительности гостевой ОС в ту же базу данных, где хранятся метрики платформы. Он направляет метрики гостевой ОС через API [настраиваемых метрик](metrics-custom-overview.md) . Затем вы можете построить диаграмму, предупредить и иным образом использовать метрики гостевой ОС, такие как метрики платформы. Дополнительные сведения см. в разделе [Обзор агентов мониторинга](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Маршрутизация метрик платформы в другие расположения

[Параметры диагностики](diagnostic-settings.md) можно использовать для направления метрик платформы в службу хранилища Azure, Azure Monitor журналы (и, таким log Analytics) и концентраторы событий.  

Существуют некоторые ограничения на то, что можно маршрутизировать, и форму, в которой они хранятся. 
- Не все метрики можно экспортировать в другие расположения. Список метрик платформы, экспортируемых с помощью параметров диагностики, см. в [этой статье](metrics-supported-export-diagnostic-settings.md).

- Отправка многомерных метрик в другие расположения через параметры диагностики в настоящее время не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
*Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Среднее|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Среднее|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|private_bytes_metric|Байты исключительного пользования|Байты|Среднее|Байтов исключительного назначения.|ServerResourceType|
|virtual_bytes_metric|Байт виртуальной памяти|Байты|Среднее|Число виртуальных байтов.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Среднее|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Среднее|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Среднее|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Среднее|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Среднее|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Среднее|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Среднее|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: Текущие подключения|Count|Среднее|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Среднее|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: Использование памяти|Байты|Среднее|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Среднее|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Среднее|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Среднее|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Среднее|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: Quota|Байты|Среднее|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
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
|mashup_engine_private_bytes_metric|Байты исключительного пользования подсистемы M|Байты|Среднее|Использование байтов исключительного пользования процессом гибридного механизма.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Байты виртуальной памяти подсистемы M|Байты|Среднее|Использование виртуальной памяти процессами гибридного механизма.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Всего запросов к шлюзу (не рекомендуется)|Count|Итог|Число запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с помощью измерения Гатевайреспонсекодекатегори.|Расположение, имя узла|
|SuccessfulRequests|Успешные запросы к шлюзу (не рекомендуется)|Count|Итог|Число успешных запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу (не рекомендуется)|Count|Итог|Число неавторизованных запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|FailedRequests|Неудачные запросы к шлюзу (не рекомендуется)|Count|Итог|Число сбоев в запросах к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|OtherRequests|Прочие запросы к шлюзу (не рекомендуется)|Count|Итог|Число других запросов к шлюзу. Используйте метрику запроса с несколькими измерениями с Гатевайреспонсекодекатегори измерением.|Расположение, имя узла|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Среднее|Общая длительность запросов шлюза в миллисекундах|Расположение, имя узла|
|BackendDuration|Длительность внутренних запросов|Миллисекунды|Среднее|Длительность внутренних запросов в миллисекундах|Расположение, имя узла|
|Capacity|Capacity|Процент|Среднее|Метрика использования для службы ApiManagement|Расположение|
|EventHubTotalEvents|Всего событий EventHub|Count|Итог|Число событий, отправленных в EventHub|Расположение|
|EventHubSuccessfulEvents|Успешные события EventHub|Count|Итог|Число успешных событий EventHub|Расположение|
|EventHubTotalFailedEvents|События EventHub с ошибками|Count|Итог|Число неудачных событий EventHub|Расположение|
|EventHubRejectedEvents|Отклоненные события EventHub|Count|Итог|Число отклоненных событий EventHub (неправильная конфигурация или несанкционированный доступ)|Расположение|
|EventHubThrottledEvents|События регулировки EventHub|Count|Итог|Число регулируемых событий EventHub|Расположение|
|EventHubTimedoutEvents|События истекшего времени ожидания EventHub|Count|Итог|Число событий с превышением времени ожидания EventHub|Расположение|
|EventHubDroppedEvents|Удаленные события EventHub|Count|Итог|Число пропущенных событий из-за ограничения размера очереди|Расположение|
|EventHubTotalBytesSent|Размер событий EventHub|Байты|Итог|Общий размер событий EventHub в байтах|Расположение|
|Requests|Requests|Count|Итог|Метрики запросов шлюза с несколькими измерениями|Расположение, имя узла, Ластеррорреасон, Баккендреспонсекоде, Гатевайреспонсекоде, Баккендреспонсекодекатегори, GatewayResponseCodeCategory|
|нетворкконнективити|Состояние сетевых подключений для ресурсов (Предварительная версия)|Count|Итог|Состояние сетевых подключений для зависимых типов ресурсов из службы управления API|Расположение, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|хттпинкомингрекуесткаунт|хттпинкомингрекуесткаунт|Count|Count|Общее число входящих HTTP-запросов.|StatusCode|
|хттпинкомингрекуестдуратион|хттпинкомингрекуестдуратион|Count|Среднее|Задержка HTTP-запроса.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Процент использования ЦП системой|Процент|Среднее|Последняя загрузка ЦП для всей системы|AppName, Pod|
|AppCpuUsagePercentage|Процент использования ЦП приложением|Процент|Среднее|Процент использования ЦП приложением ВИРТУАЛЬНОЙ машины Java|AppName, Pod|
|AppMemoryCommitted|Назначенная память для приложения|Байты|Среднее|Память, назначенная ВИРТУАЛЬНОЙ машины Java в байтах|AppName, Pod|
|AppMemoryUsed|Используемая память для приложения|Байты|Среднее|Используемая память приложения в байтах|AppName, Pod|
|AppMemoryMax|Максимальная память для приложения|Байты|Максимальная|Максимальный объем памяти в байтах, который может использоваться для управления памятью|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Максимальный доступный размер данных старого поколения|Байты|Среднее|Максимальный размер пула памяти старого поколения|AppName, Pod|
|OldGenMemoryPoolBytes|Размер данных старого поколения|Байты|Среднее|Размер пула памяти старого поколения после полного GC|AppName, Pod|
|OldGenPromotedBytes|Размер данных для продвижения в старое поколение|Байты|Максимальная|Число положительных увеличений размера пула памяти старого поколения до сборки мусора в после сборки мусора|AppName, Pod|
|YoungGenPromotedBytes|Размер данных для продвижения в молодое поколение|Байты|Максимальная|Увеличено для увеличения размера пула памяти создания Титов после одного сборщика мусора до следующего|AppName, Pod|
|GCPauseTotalCount|Число приостановок сборки мусора|Count|Итог|Число приостановок сборки мусора|AppName, Pod|
|GCPauseTotalTime|Общее время приостановок сборки мусора|Миллисекунды|Итог|Общее время приостановок сборки мусора|AppName, Pod|
|TomcatSentBytes|Всего отправленных байтов Tomcat|Байты|Итог|Всего отправленных байтов Tomcat|AppName, Pod|
|TomcatReceivedBytes|Всего полученных байтов Tomcat|Байты|Итог|Всего полученных байтов Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Общее время запросов Tomcat|Миллисекунды|Итог|Общее время запросов Tomcat|AppName, Pod|
|TomcatRequestTotalCount|Всего запросов Tomcat|Count|Итог|Всего запросов Tomcat|AppName, Pod|
|TomcatResponseAvgTime|Среднее время запроса Tomcat|Миллисекунды|Среднее|Среднее время запроса Tomcat|AppName, Pod|
|TomcatRequestMaxTime|Максимальное время запроса Tomcat|Миллисекунды|Максимальная|Максимальное время запроса Tomcat|AppName, Pod|
|TomcatErrorCount|Глобальная ошибка Tomcat|Count|Итог|Глобальная ошибка Tomcat|AppName, Pod|
|TomcatSessionActiveMaxCount|Максимальное число активных сеансов Tomcat|Count|Итог|Максимальное число активных сеансов Tomcat|AppName, Pod|
|TomcatSessionAliveMaxTime|Максимальное время активных сеансов Tomcat|Миллисекунды|Максимальная|Максимальное время активных сеансов Tomcat|AppName, Pod|
|TomcatSessionActiveCurrentCount|Число активных сеансов Tomcat|Count|Итог|Число активных сеансов Tomcat|AppName, Pod|
|TomcatSessionCreatedCount|Число созданных сеансов Tomcat|Count|Итог|Число созданных сеансов Tomcat|AppName, Pod|
|TomcatSessionExpiredCount|Число истекших сеансов Tomcat|Count|Итог|Число истекших сеансов Tomcat|AppName, Pod|
|TomcatSessionRejectedCount|Число отклоненных сеансов Tomcat|Count|Итог|Число отклоненных сеансов Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Итог|Общее количество заданий|Runbook, состояние|
|TotalUpdateDeploymentRuns|Общее количество запусков развертывания обновлений|Count|Итог|Всего запусков развертывания обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние|
|TotalUpdateDeploymentMachineRuns|Общее количество запусков развертывания обновлений для компьютера|Count|Итог|Всего компьютеров, на которых выполняется развертывание обновлений программного обеспечения, выполняются при развертывании обновлений программного обеспечения|SoftwareUpdateConfigurationName, состояние, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Итог|Общее число выделенных ядер в учетной записи пакетной службы.|Отсутствуют|
|TotalNodeCount|Dedicated Node Count|Count|Итог|Общее число выделенных узлов в учетной записи пакетной службы.|Отсутствуют|
|LowPriorityCoreCount|LowPriority Core Count|Count|Итог|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Отсутствуют|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Итог|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Отсутствуют|
|CreatingNodeCount|Creating Node Count|Count|Итог|Количество создаваемых узлов.|Отсутствуют|
|StartingNodeCount|Starting Node Count|Count|Итог|Число узлов, которые запускаются.|Отсутствуют|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Итог|Число узлов, ожидающих завершения задачи запуска.|Отсутствуют|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Итог|Число узлов, на которых произошел сбой задачи запуска.|Отсутствуют|
|IdleNodeCount|Idle Node Count|Count|Итог|Количество узлов в неактивном состоянии.|Отсутствуют|
|OfflineNodeCount|Offline Node Count|Count|Итог|Количество узлов не в сети.|Отсутствуют|
|RebootingNodeCount|Rebooting Node Count|Count|Итог|Количество перезапускаемых узлов.|Отсутствуют|
|ReimagingNodeCount|Reimaging Node Count|Count|Итог|Число узлов, для которых пересоздается образ.|Отсутствуют|
|RunningNodeCount|Running Node Count|Count|Итог|Число работающих узлов.|Отсутствуют|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Итог|Количество узлов, покидающих пул.|Отсутствуют|
|UnusableNodeCount|Unusable Node Count|Count|Итог|Число узлов, непригодных для использования.|Отсутствуют|
|PreemptedNodeCount|Preempted Node Count|Count|Итог|Количество замещенных узлов.|Отсутствуют|
|TaskStartEvent|Task Start Events|Count|Итог|Общее число запущенных задач.|Пулид, jobId|
|TaskCompleteEvent|Task Complete Events|Count|Итог|Общее число завершенных задач.|Пулид, jobId|
|TaskFailEvent|Task Fail Events|Count|Итог|Общее число задач, завершившихся сбоем.|Пулид, jobId|
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


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Отправленные задания|Отправленные задания|Count|Итог|Число отправленных заданий|Сценарий, имя_кластера|
|Выполненные задания|Выполненные задания|Count|Итог|Число завершенных заданий|Сценарий, имя_кластера, ResultType|
|Всего узлов|Всего узлов|Count|Среднее|Общее число узлов|Сценарий, имя_кластера|
|Активные узлы|Активные узлы|Count|Среднее|Число работающих узлов.|Сценарий, имя_кластера|
|Бездействующие узлы|Бездействующие узлы|Count|Среднее|Количество узлов в неактивном состоянии.|Сценарий, имя_кластера|
|Недоступные для использования узлы|Недоступные для использования узлы|Count|Среднее|Число узлов, непригодных для использования.|Сценарий, имя_кластера|
|Замещенные узлы|Замещенные узлы|Count|Среднее|Количество замещенных узлов.|Сценарий, имя_кластера|
|Освобождаемые узлы|Освобождаемые узлы|Count|Среднее|Число покидает узлов|Сценарий, имя_кластера|
|Всего ядер|Всего ядер|Count|Среднее|Общее число ядер|Сценарий, имя_кластера|
|Активные ядра|Активные ядра|Count|Среднее|Число активных ядер|Сценарий, имя_кластера|
|Бездействующие ядра|Бездействующие ядра|Count|Среднее|Число бездействующих ядер|Сценарий, имя_кластера|
|Недоступные для использования ядра|Недоступные для использования ядра|Count|Среднее|Число неиспользуемых ядер|Сценарий, имя_кластера|
|Замещенные ядра|Замещенные ядра|Count|Среднее|Число ядер с вытеснением|Сценарий, имя_кластера|
|Освобождаемые ядра|Освобождаемые ядра|Count|Среднее|Число покидает ядер|Сценарий, имя_кластера|
|Процент использования квоты|Процент использования квоты|Count|Среднее|Процент использования квоты|Сценарий, имя_кластера, Вмфамилинаме, Вмприорити|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Процент использования ЦП|Процент|Максимальная|Процент использования ЦП|Узел|
|MemoryUsage|Использование памяти|Байты|Среднее|Использование памяти|Узел|
|MemoryLimit|Предельный объем памяти|Байты|Среднее|Предельный объем памяти|Узел|
|MemoryUsagePercentageInDouble|Процент использования памяти|Процент|Среднее|Процент использования памяти|Узел|
|StorageUsage|Использование хранилища|Байты|Среднее|Использование хранилища|Узел|
|IOReadBytes|Считанные байты ввода-вывода|Байты|Итог|Считанные байты ввода-вывода|Узел|
|IOWriteBytes|Записанные байты ввода-вывода|Байты|Итог|Записанные байты ввода-вывода|Узел|
|ConnectionAccepted|Принятые подключения|Count|Итог|Принятые подключения|Узел|
|ConnectionHandled|Обработанные подключения|Count|Итог|Обработанные подключения|Узел|
|ConnectionActive|Активные подключения|Count|Среднее|Активные подключения|Узел|
|RequestHandled|Обработанные запросы|Count|Итог|Обработанные запросы|Узел|
|ProcessedBlocks|Обработанные блоки|Count|Итог|Обработанные блоки|Узел|
|ProcessedTransactions|Обработанные транзакции|Count|Итог|Обработанные транзакции|Узел|
|PendingTransactions|Отложенные транзакции|Count|Среднее|Отложенные транзакции|Узел|
|QueuedTransactions|Транзакции в очереди|Count|Среднее|Транзакции в очереди|Узел|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Count|Максимальная||ShardId|
|totalcommandsprocessed|Всего операций|Count|Итог||ShardId|
|cachehits|Попаданий в кэш|Count|Итог||ShardId|
|cachemisses|Промахов в кэше|Count|Итог||ShardId|
|качемиссрате|Частота промахов кэша|Процент|качемиссрате||ShardId|
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
|ошибки|ошибки|Count|Максимальная||Шардид, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Итог||Отсутствуют|
|cachehits0|Cache Hits (Shard 0)|Count|Итог||Отсутствуют|
|cachemisses0|Cache Misses (Shard 0)|Count|Итог||Отсутствуют|
|getcommands0|Gets (Shard 0)|Count|Итог||Отсутствуют|
|setcommands0|Sets (Shard 0)|Count|Итог||Отсутствуют|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Count|Максимальная||Отсутствуют|
|evictedkeys0|Evicted Keys (Shard 0)|Count|Итог||Отсутствуют|
|totalkeys0|Total Keys (Shard 0)|Count|Максимальная||Отсутствуют|
|expiredkeys0|Expired Keys (Shard 0)|Count|Итог||Отсутствуют|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||Отсутствуют|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||Отсутствуют|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||Отсутствуют|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||Отсутствуют|
|connectedclients1|Connected Clients (Shard 1)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Итог||Отсутствуют|
|cachehits1|Cache Hits (Shard 1)|Count|Итог||Отсутствуют|
|cachemisses1|Cache Misses (Shard 1)|Count|Итог||Отсутствуют|
|getcommands1|Gets (Shard 1)|Count|Итог||Отсутствуют|
|getcommands1|Sets (Shard 1)|Count|Итог||Отсутствуют|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Count|Максимальная||Отсутствуют|
|evictedkeys1|Evicted Keys (Shard 1)|Count|Итог||Отсутствуют|
|totalkeys1|Total Keys (Shard 1)|Count|Максимальная||Отсутствуют|
|expiredkeys1|Expired Keys (Shard 1)|Count|Итог||Отсутствуют|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||Отсутствуют|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||Отсутствуют|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||Отсутствуют|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||Отсутствуют|
|connectedclients2|Connected Clients (Shard 2)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Итог||Отсутствуют|
|cachehits2|Cache Hits (Shard 2)|Count|Итог||Отсутствуют|
|cachemisses2|Cache Misses (Shard 2)|Count|Итог||Отсутствуют|
|getcommands2|Gets (Shard 2)|Count|Итог||Отсутствуют|
|getcommands2|Sets (Shard 2)|Count|Итог||Отсутствуют|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Count|Максимальная||Отсутствуют|
|evictedkeys2|Evicted Keys (Shard 2)|Count|Итог||Отсутствуют|
|totalkeys2|Total Keys (Shard 2)|Count|Максимальная||Отсутствуют|
|expiredkeys2|Expired Keys (Shard 2)|Count|Итог||Отсутствуют|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||Отсутствуют|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||Отсутствуют|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||Отсутствуют|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||Отсутствуют|
|connectedclients3|Connected Clients (Shard 3)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Итог||Отсутствуют|
|cachehits3|Cache Hits (Shard 3)|Count|Итог||Отсутствуют|
|cachemisses3|Cache Misses (Shard 3)|Count|Итог||Отсутствуют|
|getcommands3|Gets (Shard 3)|Count|Итог||Отсутствуют|
|setcommands3|Sets (Shard 3)|Count|Итог||Отсутствуют|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Count|Максимальная||Отсутствуют|
|evictedkeys3|Evicted Keys (Shard 3)|Count|Итог||Отсутствуют|
|totalkeys3|Total Keys (Shard 3)|Count|Максимальная||Отсутствуют|
|expiredkeys3|Expired Keys (Shard 3)|Count|Итог||Отсутствуют|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||Отсутствуют|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||Отсутствуют|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||Отсутствуют|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||Отсутствуют|
|connectedclients4|Connected Clients (Shard 4)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Итог||Отсутствуют|
|cachehits4|Cache Hits (Shard 4)|Count|Итог||Отсутствуют|
|cachemisses4|Cache Misses (Shard 4)|Count|Итог||Отсутствуют|
|getcommands4|Gets (Shard 4)|Count|Итог||Отсутствуют|
|setcommands4|Sets (Shard 4)|Count|Итог||Отсутствуют|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Count|Максимальная||Отсутствуют|
|evictedkeys4|Evicted Keys (Shard 4)|Count|Итог||Отсутствуют|
|totalkeys4|Total Keys (Shard 4)|Count|Максимальная||Отсутствуют|
|expiredkeys4|Expired Keys (Shard 4)|Count|Итог||Отсутствуют|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||Отсутствуют|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||Отсутствуют|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||Отсутствуют|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||Отсутствуют|
|connectedclients5|Connected Clients (Shard 5)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Итог||Отсутствуют|
|cachehits5|Cache Hits (Shard 5)|Count|Итог||Отсутствуют|
|cachemisses5|Cache Misses (Shard 5)|Count|Итог||Отсутствуют|
|getcommands5|Gets (Shard 5)|Count|Итог||Отсутствуют|
|getcommands5|Sets (Shard 5)|Count|Итог||Отсутствуют|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Count|Максимальная||Отсутствуют|
|evictedkeys5|Evicted Keys (Shard 5)|Count|Итог||Отсутствуют|
|totalkeys5|Total Keys (Shard 5)|Count|Максимальная||Отсутствуют|
|expiredkeys5|Expired Keys (Shard 5)|Count|Итог||Отсутствуют|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||Отсутствуют|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||Отсутствуют|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||Отсутствуют|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||Отсутствуют|
|connectedclients6|Connected Clients (Shard 6)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Итог||Отсутствуют|
|cachehits6|Cache Hits (Shard 6)|Count|Итог||Отсутствуют|
|cachemisses6|Cache Misses (Shard 6)|Count|Итог||Отсутствуют|
|getcommands6|Gets (Shard 6)|Count|Итог||Отсутствуют|
|setcommands6|Sets (Shard 6)|Count|Итог||Отсутствуют|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Count|Максимальная||Отсутствуют|
|evictedkeys6|Evicted Keys (Shard 6)|Count|Итог||Отсутствуют|
|totalkeys6|Total Keys (Shard 6)|Count|Максимальная||Отсутствуют|
|expiredkeys6|Expired Keys (Shard 6)|Count|Итог||Отсутствуют|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||Отсутствуют|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||Отсутствуют|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||Отсутствуют|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||Отсутствуют|
|connectedclients7|Connected Clients (Shard 7)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Итог||Отсутствуют|
|cachehits7|Cache Hits (Shard 7)|Count|Итог||Отсутствуют|
|cachemisses7|Cache Misses (Shard 7)|Count|Итог||Отсутствуют|
|getcommands7|Gets (Shard 7)|Count|Итог||Отсутствуют|
|setcommands7|Sets (Shard 7)|Count|Итог||Отсутствуют|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Count|Максимальная||Отсутствуют|
|evictedkeys7|Evicted Keys (Shard 7)|Count|Итог||Отсутствуют|
|totalkeys7|Total Keys (Shard 7)|Count|Максимальная||Отсутствуют|
|expiredkeys7|Expired Keys (Shard 7)|Count|Итог||Отсутствуют|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||Отсутствуют|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||Отсутствуют|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||Отсутствуют|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||Отсутствуют|
|connectedclients8|Connected Clients (Shard 8)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Итог||Отсутствуют|
|cachehits8|Cache Hits (Shard 8)|Count|Итог||Отсутствуют|
|cachemisses8|Cache Misses (Shard 8)|Count|Итог||Отсутствуют|
|getcommands8|Gets (Shard 8)|Count|Итог||Отсутствуют|
|setcommands8|Sets (Shard 8)|Count|Итог||Отсутствуют|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Count|Максимальная||Отсутствуют|
|evictedkeys8|Evicted Keys (Shard 8)|Count|Итог||Отсутствуют|
|totalkeys8|Total Keys (Shard 8)|Count|Максимальная||Отсутствуют|
|expiredkeys8|Expired Keys (Shard 8)|Count|Итог||Отсутствуют|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||Отсутствуют|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||Отсутствуют|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||Отсутствуют|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||Отсутствуют|
|connectedclients9|Connected Clients (Shard 9)|Count|Максимальная||Отсутствуют|
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Итог||Отсутствуют|
|cachehits9|Cache Hits (Shard 9)|Count|Итог||Отсутствуют|
|cachemisses9|Cache Misses (Shard 9)|Count|Итог||Отсутствуют|
|getcommands9|Gets (Shard 9)|Count|Итог||Отсутствуют|
|setcommands9|Sets (Shard 9)|Count|Итог||Отсутствуют|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Count|Максимальная||Отсутствуют|
|evictedkeys9|Evicted Keys (Shard 9)|Count|Итог||Отсутствуют|
|totalkeys9|Total Keys (Shard 9)|Count|Максимальная||Отсутствуют|
|expiredkeys9|Expired Keys (Shard 9)|Count|Итог||Отсутствуют|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||Отсутствуют|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||Отсутствуют|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||Отсутствуют|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||Отсутствуют|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||Отсутствуют|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||Отсутствуют|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/кднвебаппликатионфиреваллполиЦиес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Итог|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, действие|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Отсутствуют|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Отсутствуют|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Отсутствуют|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|Отсутствуют|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|Отсутствуют|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Отсутствуют|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Отсутствуют|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|RoleInstanceId|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|RoleInstanceId|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|RoleInstanceId|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Среднее|Среднее количество байтов, считанных с диска за период мониторинга.|RoleInstanceId|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Среднее|Среднее количество байтов, записанных на диск за период мониторинга.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Count|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Отсутствуют|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемый ADLS 2-го поколения (иерархический) индекс в байтах.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый Хранилищем таблиц учетной записи хранения, в байтах.|Отсутствуют|
|TableCount|Количество таблиц|Count|Среднее|Количество таблиц в Хранилище таблиц учетной записи хранения.|Отсутствуют|
|TableEntityCount|Количество сущностей таблиц|Count|Среднее|Количество сущностей таблиц в Хранилище таблиц учетной записи хранения.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения в байтах.|FileShare|
|FileCount|Количество файлов|Count|Среднее|Число файлов в файловой службе учетной записи хранения.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Count|Среднее|Число общих файловых ресурсов в файловой службе учетной записи хранения.|Отсутствуют|
|FileShareSnapshotCount|Число моментальных снимков файлового ресурса|Count|Среднее|Количество моментальных снимков, имеющихся в службе файлов учетной записи хранения в общей папке.|FileShare|
|FileShareSnapshotSize|Размер моментального снимка файлового ресурса|Байты|Среднее|Объем хранилища, используемый моментальными снимками в файловой службе учетной записи хранения в байтах.|FileShare|
|FileShareQuota|Размер квоты файлового ресурса|Байты|Среднее|Верхний предел объема хранилища, который может использоваться службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности, Общая папка|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности, Общая папка|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Отсутствуют|
|QueueCount|Количество очередей|Count|Среднее|Количество очередей в службе очередей учетной записи хранения.|Отсутствуют|
|QueueMessageCount|Количество сообщений очереди|Count|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Задержка, используемая службой хранилища Azure для обработки успешного запроса в миллисекундах. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Сквозная задержка успешных запросов к службе хранилища или заданной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Итог|Общее число вызовов.|ApiName, Имя_операции, регион|
|SuccessfulCalls|Successful Calls|Count|Итог|Число успешных вызовов.|ApiName, Имя_операции, регион|
|TotalErrors|Total Errors|Count|Итог|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|ApiName, Имя_операции, регион|
|BlockedCalls|Blocked Calls|Count|Итог|Число вызовов, превысивших ограничение скорости или квоты.|ApiName, Имя_операции, регион|
|ServerErrors|Server Errors|Count|Итог|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|ApiName, Имя_операции, регион|
|ClientErrors|Client Errors|Count|Итог|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|ApiName, Имя_операции, регион|
|DataIn|Входящие данные:|Байты|Итог|Размер входящих данных в байтах.|ApiName, Имя_операции, регион|
|DataOut|Выходные данные|Байты|Итог|Размер исходящих данных в байтах.|ApiName, Имя_операции, регион|
|Задержка|Задержка|MilliSeconds|Среднее|Время задержки в миллисекундах.|ApiName, Имя_операции, регион|
|TotalTokenCalls|Всего вызовов токенов|Count|Итог|Общее число вызовов токенов.|ApiName, Имя_операции, регион|
|CharactersTranslated|Преобразованные символы|Count|Итог|Общее количество символов во входящем текстовом запросе.|ApiName, Имя_операции, регион|
|CharactersTrained|Обученные символы|Count|Итог|Общее количество обученных символов.|ApiName, Имя_операции, регион|
|SpeechSessionDuration|Длительность речи|Секунды|Итог|Общая длительность речи в секундах.|ApiName, Имя_операции, регион|
|TotalTransactions|Всего транзакций|Count|Итог|Общее число транзакций.|Отсутствуют|
|процесседимажес|Обработано изображений|Count|Итог| Число транзакций для обработки изображений.|ApiName, FeatureName, канал, регион|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Отсутствуют|
|Сеть (входящий трафик)|Оплачиваемый входящий трафик (не рекомендуется)|Байты|Итог|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|Отсутствуют|
|Сеть (исходящий трафик)|Оплачиваемый исходящий трафик (не рекомендуется)|Байты|Итог|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|Отсутствуют|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байт считано с диска за период мониторинга|Отсутствуют|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Число байтов, записанных на диск в течение периода мониторинга|Отсутствуют|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Отсутствуют|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Отсутствуют|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|Отсутствуют|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|Отсутствуют|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с [) (не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с [(устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)] (Portal-Disk-Metrics-Deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Длина очереди на диск ОС|Диск ОС длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|Отсутствуют|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN|
|Скорость чтения с диска данных (операций/с)|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (операций/с)|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Длина очереди диска данных|Длина очереди диска данных (предварительная версия)|Count|Среднее|Длина очереди дисков данных|LUN|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость чтения с диска ОС (операций/с)|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (операций/с)|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Длина очереди диска ОС|Длина очереди диска ОС (предварительная версия)|Count|Среднее|Длина очереди дисков ОС|Отсутствуют|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|Отсутствуют|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|Отсутствуют|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|Отсутствуют|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|Отсутствуют|
|Попадание при чтении в кэше для диска данных категории "Премиум"|Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска данных категории "Премиум"|LUN|
|Промах чтения в кэше для диска данных категории "Премиум"|Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска данных категории "Премиум"|LUN|
|Попадание при чтении в кэше для диска ОС категории "Премиум"|Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска ОС категории "Премиум"|Отсутствуют|
|Промах чтения в кэше для диска ОС категории "Премиум"|Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска ОС категории "Премиум"|Отсутствуют|
|Суммарный входящий трафик|Суммарный входящий трафик|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Отсутствуют|
|Суммарный исходящий трафик|Суммарный исходящий трафик|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Отсутствуют|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|VMName|
|Сеть (входящий трафик)|Оплачиваемый входящий трафик (не рекомендуется)|Байты|Итог|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|VMName|
|Сеть (исходящий трафик)|Оплачиваемый исходящий трафик (не рекомендуется)|Байты|Итог|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|VMName|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байт считано с диска за период мониторинга|VMName|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Число байтов, записанных на диск в течение периода мониторинга|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|VMName|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|Отсутствуют|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|Отсутствуют|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с [) (не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с [(устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Длина очереди на диск ОС|Диск ОС длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|Отсутствуют|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN, VMName|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN, VMName|
|Скорость чтения с диска данных (операций/с)|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Скорость записи на диск данных (операций/с)|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN, VMName|
|Длина очереди диска данных|Длина очереди диска данных (предварительная версия)|Count|Среднее|Длина очереди дисков данных|LUN, VMName|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|VMName|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|VMName|
|Скорость чтения с диска ОС (операций/с)|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Скорость записи на диск ОС (операций/с)|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|VMName|
|Длина очереди диска ОС|Длина очереди диска ОС (предварительная версия)|Count|Среднее|Длина очереди дисков ОС|VMName|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|VMName|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|VMName|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|VMName|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|VMName|
|Попадание при чтении в кэше для диска данных категории "Премиум"|Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска данных категории "Премиум"|LUN, VMName|
|Промах чтения в кэше для диска данных категории "Премиум"|Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска данных категории "Премиум"|LUN, VMName|
|Попадание при чтении в кэше для диска ОС категории "Премиум"|Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска ОС категории "Премиум"|VMName|
|Промах чтения в кэше для диска ОС категории "Премиум"|Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска ОС категории "Премиум"|VMName|
|Суммарный входящий трафик|Суммарный входящий трафик|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|VMName|
|Суммарный исходящий трафик|Суммарный исходящий трафик|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Отсутствуют|
|Сеть (входящий трафик)|Оплачиваемый входящий трафик (не рекомендуется)|Байты|Итог|Число оплачиваемых байт, полученных по всем сетевым интерфейсам виртуальных машин (входящий трафик) (не рекомендуется)|Отсутствуют|
|Сеть (исходящий трафик)|Оплачиваемый исходящий трафик (не рекомендуется)|Байты|Итог|Количество исходящих байт за все сетевые интерфейсы виртуальных машин (исходящий трафик) (не рекомендуется)|Отсутствуют|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Байт считано с диска за период мониторинга|Отсутствуют|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Число байтов, записанных на диск в течение периода мониторинга|Отсутствуют|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Отсутствуют|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Отсутствуют|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|Отсутствуют|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|Отсутствуют|
|Скорость чтения в расчете на диск (байт/с)|Скорость чтения с диска данных (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (байт/с)|Скорость записи на диск данных (байт/с [) (не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|ИД слота|
|Скорость чтения в расчете на диск (операций/с)|Операций чтения с диска данных/с [(устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Скорость записи в расчете на диск (операций/с)|Операций записи на диск данных в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|ИД слота|
|Длина очереди в расчете на диск|Диск данных длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков данных|ИД слота|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с [) (устарело)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость чтения с диска ОС (операций/с)|Операций чтения с диска ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (операций/с)|Операций записи на диск ОС в секунду [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Длина очереди на диск ОС|Диск ОС длина очереди [(не рекомендуется)](portal-disk-metrics-deprecation.md)|Count|Среднее|Длина очереди дисков ОС|Отсутствуют|
|Скорость чтения с диска данных (байт/с)|Скорость чтения с диска данных (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (байт/с)|Скорость записи на диск данных (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск за период мониторинга|LUN|
|Скорость чтения с диска данных (операций/с)|Скорость чтения с диска данных (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Скорость записи на диск данных (операций/с)|Скорость записи на диск данных (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга|LUN|
|Длина очереди диска данных|Длина очереди диска данных (предварительная версия)|Count|Среднее|Длина очереди дисков данных|LUN|
|Скорость чтения с диска ОС (байт/с)|Скорость чтения с диска ОС (байт/с) (предварительная версия)|Число/с|Среднее|Скорость чтения (байт/с) с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (байт/с)|Скорость записи на диск ОС (байт/с) (предварительная версия)|Число/с|Среднее|Байтов в секунду, записанных на один диск в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость чтения с диска ОС (операций/с)|Скорость чтения с диска ОС (операций/с) (предварительная версия)|Число/с|Среднее|Чтение операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Скорость записи на диск ОС (операций/с)|Скорость записи на диск ОС (операций/с) (предварительная версия)|Число/с|Среднее|Запись операций ввода-вывода с одного диска в течение периода мониторинга для диска ОС|Отсутствуют|
|Длина очереди диска ОС|Длина очереди диска ОС (предварительная версия)|Count|Среднее|Длина очереди дисков ОС|Отсутствуют|
|Входящие потоки|Входящие потоки|Count|Среднее|Входящие потоки — это число текущих потоков во входящем направлении (трафик, поступающий на виртуальную машину).|Отсутствуют|
|Исходящие потоки|Исходящие потоки|Count|Среднее|Исходящие потоки — это число текущих потоков в исходящем направлении (трафик, выходящий за пределы виртуальной машины)|Отсутствуют|
|Максимальная скорость создания входящих потоков|Максимальная скорость создания входящих потоков|Число/с|Среднее|Максимальная скорость создания входящих потоков (трафик, поступающий в виртуальную машину)|Отсутствуют|
|Максимальная скорость создания исходящих потоков|Максимальная скорость создания исходящих потоков|Число/с|Среднее|Максимальная скорость создания исходящих потоков (трафик, выходящий за пределы виртуальной машины)|Отсутствуют|
|Попадание при чтении в кэше для диска данных категории "Премиум"|Попадание при чтении в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска данных категории "Премиум"|LUN|
|Промах чтения в кэше для диска данных категории "Премиум"|Промах чтения в кэше для диска данных категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска данных категории "Премиум"|LUN|
|Попадание при чтении в кэше для диска ОС категории "Премиум"|Попадание при чтении в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Попадание при чтении в кэше для диска ОС категории "Премиум"|Отсутствуют|
|Промах чтения в кэше для диска ОС категории "Премиум"|Промах чтения в кэше для диска ОС категории "Премиум" (предварительная версия)|Процент|Среднее|Промах чтения в кэше для диска ОС категории "Премиум"|Отсутствуют|
|Суммарный входящий трафик|Суммарный входящий трафик|Байты|Итог|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Отсутствуют|
|Суммарный исходящий трафик|Суммарный исходящий трафик|Байты|Итог|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Отсутствуют|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Count|Среднее|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Среднее|Общее использование памяти в байтах.|containerName|
|NetworkBytesReceivedPerSecond|Получено байтов по сети в секунду|Байты|Среднее|Количество байт, полученных по сети в секунду.|Отсутствуют|
|NetworkBytesTransmittedPerSecond|Передано байт по сети в секунду|Байты|Среднее|Количество байт, переданных по сети в секунду.|Отсутствуют|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TotalPullCount|Всего операций извлечения|Count|Среднее|Общее число опросов изображений|Отсутствуют|
|SuccessfulPullCount|Число успешных операций извлечения|Count|Среднее|Число успешных операций извлечения изображений|Отсутствуют|
|TotalPushCount|Всего операций отправки|Count|Среднее|Общее число push-уведомлений для образа|Отсутствуют|
|SuccessfulPushCount|Число успешных операций отправки|Count|Среднее|Число успешных push-уведомлений образа|Отсутствуют|
|RunDuration|Длительность выполнения|Миллисекунды|Итог|Длительность выполнения в миллисекундах|Отсутствуют|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Общее количество доступных ядер ЦП в управляемом кластере|Count|Среднее|Общее количество доступных ядер ЦП в управляемом кластере|Отсутствуют|
|kube_node_status_allocatable_memory_bytes|Общий объем доступной памяти в управляемом кластере|Байты|Среднее|Общий объем доступной памяти в управляемом кластере|Отсутствуют|
|kube_pod_status_ready|Число модулей pod в состоянии готовности|Count|Среднее|Число модулей pod в состоянии готовности|пространство имен, Pod|
|kube_node_status_condition|Состояния для различных условий узла|Count|Среднее|Состояния для различных условий узла|условие, состояние, status2, узел|
|kube_pod_status_phase|Число модулей pod по фазам|Count|Среднее|Число модулей pod по фазам|этап, пространство имен, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. Кустомпровидерс/ресаурцепровидерс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|сукцессфуллрекуестс|Выполненные запросы|Count|Итог|Успешные запросы, выполненные настраиваемым поставщиком|HttpMethod, Каллпас, StatusCode|
|FailedRequests|Невыполненные запросы|Count|Итог|Получение доступных журналов для пользовательских поставщиков ресурсов.|HttpMethod, Каллпас, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|NICReadThroughput|Пропускная способность чтения (сеть)|Байт/с|Среднее|Пропускная способность чтения сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|NICWriteThroughput|Пропускная способность записи (сеть)|Байт/с|Среднее|Пропускная способность записи сетевого интерфейса на устройстве в отчетном периоде для всех томов в шлюзе.|InstanceName|
|CloudReadThroughputPerShare|Пропускная способность скачивания из облака (общий ресурс)|Байт/с|Среднее|Пропускная способность загрузки в Azure из общей папки в течение отчетного периода.|Поделиться|
|CloudUploadThroughputPerShare|Пропускная способность отправки в облако (общий ресурс)|Байт/с|Среднее|Пропускная способность передачи в Azure из общей папки в течение отчетного периода.|Поделиться|
|BytesUploadedToCloudPerShare|Отправлено байт в облако (общий ресурс)|Байты|Среднее|Общее число байтов, отправленных в Azure из общей папки в течение отчетного периода.|Поделиться|
|TotalCapacity|Общая емкость|Байты|Среднее|Общая емкость|Отсутствуют|
|AvailableCapacity|Доступная емкость|Байты|Среднее|Доступная емкость в байтах за отчетный период.|Отсутствуют|
|CloudUploadThroughput|Пропускная способность отправки в облако|Байт/с|Среднее|Пропускная способность облачной передачи в Azure за период отчетного периода.|Отсутствуют|
|CloudReadThroughput|Пропускная способность скачивания из облака|Байт/с|Среднее|Пропускная способность скачивания облака в Azure в течение отчетного периода.|Отсутствуют|
|BytesUploadedToCloud|Отправлено байт в облако (устройство)|Байты|Среднее|Общее число байтов, отправленных в Azure с устройства за отчетный период.|Отсутствуют|
|HyperVVirtualProcessorUtilization|Пограничные вычисления — процент загрузки ЦП|Процент|Среднее|Процент использования ЦП|InstanceName|
|HyperVMemoryUtilization|Пограничные вычисления — использование памяти|Процент|Среднее|Объем используемой оперативной памяти|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Каталог Microsoft.

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ассетдистрибутионбиклассификатион|Распределение ресурсов по классификации|Count|Итог|Указывает число активов с определенной классификацией, т. е. они классифицируются с этой меткой.|Классификация, источник|
|ассетдистрибутионбисторажетипе|Распределение ресурсов по типу хранилища|Count|Итог|Указывает число активов с определенным типом хранилища.|StorageType|
|нумберофассетсвисклассификатионс|Количество активов по крайней мере с одной классификацией|Count|Среднее|Указывает количество ресурсов по крайней мере с одной классификацией тегов.|Отсутствуют|
|сканканцеллед|Сканирование отменено|Count|Итог|Указывает число отмененных просмотров.|Отсутствуют|
|сканкомплетед|Сканирование завершено|Count|Итог|Указывает количество успешно выполненных проверок.|Отсутствуют|
|сканфаилед|Сбой сканирования|Count|Итог|Указывает количество ошибок сканирования.|Отсутствуют|
|скантиметакен|Затраченное время сканирования|Секунды|Итог|Указывает общее время сканирования в секундах.|Отсутствуют|
|каталогактивеусерс|Активные Ежедневные Пользователи|Count|Итог|Ежедневное число активных пользователей|Отсутствуют|
|каталогусаже|Распределение использования по операциям|Count|Итог|Указывает число операций, выполняемых пользователем операции в каталоге, т. е. доступ, поиск, глоссарий.|Операция|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Count|Итог||Пипелиненаме, activityName|
|SuccessfulRuns.|успешные циклы выполнения.|Count|Итог||Пипелиненаме, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Count|Итог||FailureType, имя|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Count|Итог||FailureType, имя|
|PipelineCancelledRuns|Метрики отмененных выполнений конвейеров|Count|Итог||FailureType, имя|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Count|Итог||ActivityType, Пипелиненаме, FailureType, имя|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Count|Итог||ActivityType, Пипелиненаме, FailureType, имя|
|ActivityCancelledRuns|Метрики отмененных выполнений действий|Count|Итог||ActivityType, Пипелиненаме, FailureType, имя|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Count|Итог||Имя, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Count|Итог||Имя, FailureType|
|TriggerCancelledRuns|Метрики отмененных запусков триггеров|Count|Итог||Имя, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Процент|Среднее||Интегратионрунтименаме, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Среднее||Интегратионрунтименаме, NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Длительность очереди среды выполнения интеграции|Секунды|Среднее||интегратионрунтименаме|
|IntegrationRuntimeQueueLength|Длина очереди среды выполнения интеграции|Count|Среднее||интегратионрунтименаме|
|интегратионрунтимеаваилабленоденумбер|Число доступных узлов в среде выполнения интеграции|Count|Среднее||интегратионрунтименаме|
|MaxAllowedResourceCount|Максимальное допустимое число сущностей|Count|Максимальная||Отсутствуют|
|MaxAllowedFactorySizeInGbUnits|Максимально допустимый размер фабрики (в GB)|Count|Максимальная||Отсутствуют|
|ResourceCount|Всего сущностей|Count|Максимальная||Отсутствуют|
|FactorySizeInGbUnits|Общий размер фабрики (в ГБ)|Count|Максимальная||Отсутствуют|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Итог|Количество успешно выполненных заданий.|Отсутствуют|
|JobEndedFailure|Failed Jobs|Count|Итог|Количество невыполненных заданий.|Отсутствуют|
|JobEndedCancelled|Cancelled Jobs|Count|Итог|Количество отмененных заданий.|Отсутствуют|
|JobAUEndedSuccess|Successful AU Time|Секунды|Итог|Общее время AU успешно выполненных заданий.|Отсутствуют|
|JobAUEndedFailure|Failed AU Time|Секунды|Итог|Общее время AU невыполненных заданий.|Отсутствуют|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Итог|Общее время AU отмененных заданий.|Отсутствуют|
|жобстаже|Задания на этапе|Count|Итог|Количество заданий на каждом этапе.|Отсутствуют|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|Отсутствуют|
|DataWritten|Записанные данные|Байты|Итог|Общий объем данных, записанных в учетной записи.|Отсутствуют|
|DataRead|Данных прочитано|Байты|Итог|Общий объем данных, прочитанных в учетной записи.|Отсутствуют|
|WriteRequests|Запросы на запись|Count|Итог|Количество запросов на запись данных в учетную запись.|Отсутствуют|
|ReadRequests|Запросы на чтение|Count|Итог|Количество запросов на чтение данных для учетной записи.|Отсутствуют|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|шарекаунт|Отправленные общие папки|Count|Максимальная|Число отправленных общих ресурсов в учетной записи|Сетев|
|шаресубскриптионкаунт|Полученные общие ресурсы|Count|Максимальная|Число полученных общих ресурсов в учетной записи|шаресубскриптионнаме|
|сукцеедедшаресинчронизатионс|Отправлено моментальных снимков общего ресурса|Count|Count|Число отправленных моментальных снимков общих ресурсов в учетной записи|Отсутствуют|
|фаиледшаресинчронизатионс|Отправленные общие моментальные снимки с ошибками|Count|Count|Число отправленных моментальных снимков с ошибками общего доступа в учетной записи|Отсутствуют|
|сукцеедедшаресубскриптионсинчронизатионс|Получено моментальных снимков общего ресурса|Count|Count|Число полученных моментальных снимков общего ресурса в учетной записи|Отсутствуют|
|фаиледшаресубскриптионсинчронизатионс|Получено неудачных поступающих моментальных снимков|Count|Count|Число полученных общих моментальных снимков с ошибками в учетной записи|Отсутствуют|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Отсутствуют|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Отсутствуют|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Отсутствуют|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Отсутствуют|
|storage_used|Storage used|Байты|Среднее|Storage used|Отсутствуют|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|Отсутствуют|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Среднее|Максимальный объем хранилища для журнала сервера|Отсутствуют|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|Отсутствуют|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|Отсутствуют|
|seconds_behind_master|Задержка репликации в секундах|Count|Максимальная|Задержка репликации в секундах|Отсутствуют|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|Отсутствуют|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|Отсутствуют|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|Отсутствуют|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Отсутствуют|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Отсутствуют|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Отсутствуют|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Отсутствуют|
|storage_used|Storage used|Байты|Среднее|Storage used|Отсутствуют|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|Отсутствуют|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальная|Максимальный объем хранилища для журнала сервера|Отсутствуют|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|Отсутствуют|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|Отсутствуют|
|seconds_behind_master|Задержка репликации в секундах|Count|Максимальная|Задержка репликации в секундах|Отсутствуют|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|Отсутствуют|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|Отсутствуют|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|Отсутствуют|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Отсутствуют|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Отсутствуют|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Отсутствуют|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Отсутствуют|
|storage_used|Storage used|Байты|Среднее|Storage used|Отсутствуют|
|storage_limit|Storage limit|Байты|Максимальная|Storage limit|Отсутствуют|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Среднее|Процент хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Среднее|Используемый объем хранилища для журнала сервера|Отсутствуют|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальная|Максимальный объем хранилища для журнала сервера|Отсутствуют|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|Отсутствуют|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|Отсутствуют|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Среднее|Используемое хранилище резервных копий|Отсутствуют|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|Отсутствуют|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|Отсутствуют|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|Максимальная|Запаздывание реплики в секундах|Отсутствуют|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|Максимальная|Запаздывание в байтах самой задержкой реплики|Отсутствуют|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Отсутствуют|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Отсутствуют|
|iops|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Count|Среднее|Операций ввода-вывода в секунду|Отсутствуют|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Отсутствуют|
|storage_used|Storage used|Байты|Среднее|Storage used|Отсутствуют|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|Отсутствуют|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|Отсутствуют|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|Отсутствуют|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. Дбфорпостгрескл/синглесерверс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Среднее|Нагрузка ЦП|Отсутствуют|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Отсутствуют|
|iops|ОПЕРАЦИЙ ВВОДА-ВЫВОДА|Count|Среднее|Операций ввода-вывода в секунду|Отсутствуют|
|storage_percent|Процент хранилища|Процент|Среднее|Процент хранилища|Отсутствуют|
|storage_used|Storage used|Байты|Среднее|Storage used|Отсутствуют|
|active_connections|Активные подключения|Count|Среднее|Активные подключения|Отсутствуют|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Итог|Исходящий сетевой трафик по активным подключениям|Отсутствуют|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Итог|Входящий сетевой трафик по активным подключениям|Отсутствуют|
|connections_failed|Неудачные подключения|Count|Итог|Неудачные подключения|Отсутствуют|
|connections_succeeded|Успешные подключения|Count|Итог|Успешные подключения|Отсутствуют|
|maximum_used_transactionIDs|Максимальное число используемых идентификаторов транзакций|Count|Среднее|Максимальное число используемых идентификаторов транзакций|Отсутствуют|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Итог|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Отсутствуют|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Итог|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Отсутствуют|
|c2d.commands.egress.complete.success|Доставленные сообщения из облака на устройство (C2D)|Count|Итог|Число попыток доставки сообщений, отправленных из облака на устройство, успешно завершено устройством|Отсутствуют|
|c2d.commands.egress.abandon.success|Отброшенные сообщения из облака на устройство (C2D)|Count|Итог|Число сообщений, отправленных из облака на устройство, которые отброшены устройством|Отсутствуют|
|c2d.commands.egress.reject.success|Отклоненные сообщения из облака на устройство (C2D)|Count|Итог|Число сообщений, отправленных из облака на устройство, отклоненных устройством|Отсутствуют|
|C2DMessagesExpired|Сообщения из облака на устройство (C2D) с истекшим сроком действия (предварительная версия)|Count|Итог|Число сообщений, отправленных из облака на устройство с истекшим сроком действия|Отсутствуют|
|devices.totalDevices|Total devices (deprecated) (Всего устройств (не рекомендуется))|Count|Итог|Число устройств, зарегистрированных в Центре Интернета вещей.|Отсутствуют|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) (Подключенные устройства (не рекомендуется)) |Count|Итог|Число устройств, подключенных к Центру Интернета вещей.|Отсутствуют|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)|Count|Итог|Количество раз, когда сообщения были успешно доставлены на все конечные точки с помощью маршрутизации Центра Интернета вещей. Если сообщение направляется на несколько конечных точек, это значение увеличивается на единицу для каждой успешной доставки. Если сообщение доставлено несколько раз на одну конечную точку, это значение увеличивается на единицу для каждой успешной доставки.|Отсутствуют|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии) |Count|Итог|Количество раз, когда сообщения удалялись подсистемой маршрутизации Центра Интернета вещей из-за неработоспособности конечных точек. В этом значении не учитываются сообщения, доставленные через резервный маршрут, так как отброшенные сообщения туда не доставляются.|Отсутствуют|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии) |Count|Итог|Количество раз, когда сообщения были потеряны подсистемой маршрутизации Центра Интернета вещей из-за того, что они не соответствуют никаким правилам маршрутизации (включая резервное правило). |Отсутствуют|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)|Count|Итог|Количество раз, когда подсистеме маршрутизации Центра Интернета вещей не удалось доставить сообщения из-за несовместимости с конечной точкой. В это значение не входят повторные попытки.|Отсутствуют|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставляла сообщения на конечную точку, связанную с резервным маршрутом.|Отсутствуют|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки концентратора событий.|Отсутствуют|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Отсутствуют|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки очереди служебной шины.|Отсутствуют|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку очереди служебной шины.|Отсутствуют|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки раздела служебной шины.|Отсутствуют|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку раздела служебной шины.|Отсутствуют|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на встроенную конечную точку (messages/events).|Отсутствуют|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на встроенную конечную точку (messages/events).|Отсутствуют|
|d2c.Endpoints.egress.Storage|Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей успешно доставила сообщения на конечные точки хранилища.|Отсутствуют|
|d2c.Endpoints.latency.Storage|Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в Центр Интернета вещей и поступлением сообщения телеметрии на конечную точку хранилища.|Отсутствуют|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)|Байты|Итог|Объем данных (в байтах), доставленных подсистемой маршрутизации Центра Интернета вещей на конечные точки хранилища.|Отсутствуют|
|d2c.Endpoints.egress.Storage.BLOBs|Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)|Count|Итог|Количество раз, когда подсистема маршрутизации Центра Интернета вещей доставила BLOB-объекты на конечные точки хранилища.|Отсутствуют|
|EventGridDeliveries|Сетка событий: доставок (Предварительная версия)|Count|Итог|Число событий центра Интернета вещей, опубликованных в сетке событий. Используйте измерение результат для количества успешных и неудачных запросов. Измерение EventType показывает тип события ( https://aka.ms/ioteventgrid) .|ResourceId, result, EventType|
|EventGridLatency|Задержка сетки событий (предварительная версия)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) от момента создания события центра Интернета вещей до публикации события в сетке событий. Это число является средним значением между всеми типами событий. Используйте измерение EventType для просмотра задержки определенного типа события.|ResourceId, EventType|
|раутингделивериес|Маршрутизация поставок (Предварительная версия)|Миллисекунды|Итог|Число попыток доставки сообщений центром Интернета вещей всем конечным точкам с помощью маршрутизации. Чтобы просмотреть количество успешных или неудачных попыток, используйте измерение результат. Чтобы просмотреть причину сбоя, например недопустимый, удаленный или потерянный, используйте измерение Фаилуререасонкатегори. Вы также можете использовать измерения EndpointName и EndpointType, чтобы понять, сколько сообщений было доставлено в разные конечные точки. Значение метрики увеличивается на единицу при каждой попытке доставки, в том числе если сообщение доставляется в несколько конечных точек или если сообщение доставляется в одну и ту же конечную точку несколько раз.|ResourceId, EndpointType, EndpointName, Фаилуререасонкатегори, result, Раутингсаурце|
|раутингделиверилатенци|Задержка доставки маршрутизации (Предварительная версия)|Миллисекунды|Среднее|Средняя задержка (в миллисекундах) между поступлением сообщения в центр Интернета вещей и входящим сообщением в конечную точку. Вы можете использовать измерения EndpointName и EndpointType для анализа задержки в различных конечных точках.|ResourceId, EndpointType, EndpointName, Раутингсаурце|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Итог|Число всех успешных операций чтения с двойников, инициированных устройством.|Отсутствуют|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Итог|Число всех неудачных операций чтения с двойников, инициированных устройством.|Отсутствуют|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Отсутствуют|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Итог|Число всех успешных обновлений двойников, инициированных устройством.|Отсутствуют|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Итог|Число всех неудачных обновлений двойников, инициированных устройством.|Отсутствуют|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Отсутствуют|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Итог|Число всех успешных вызовов прямых методов.|Отсутствуют|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Итог|Число всех неудачных вызовов прямых методов.|Отсутствуют|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Отсутствуют|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Отсутствуют|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Итог|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Отсутствуют|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Итог|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Отсутствуют|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Отсутствуют|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Итог|Число всех успешных обновлений двойников, инициированных из серверной части.|Отсутствуют|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Итог|Число всех неудачных обновлений двойников, инициированных из серверной части.|Отсутствуют|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Среднее|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Отсутствуют|
|twinQueries.success|Успешные запросы двойников.|Count|Итог|Число всех успешных запросов двойников.|Отсутствуют|
|twinQueries.failure|Неудачные запросы двойников.|Count|Итог|Количество всех неудачных запросов двойников.|Отсутствуют|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Среднее|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Отсутствуют|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Итог|Количество всех успешных созданий заданий обновления двойников.|Отсутствуют|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Итог|Количество всех неудачных операций создания заданий обновления двойников.|Отсутствуют|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Итог|Количество всех успешных операций создания заданий вызова прямых методов.|Отсутствуют|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Итог|Количество всех неудачных операций создания заданий вызова прямых методов.|Отсутствуют|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Итог|Количество всех успешных вызовов для получения списка заданий.|Отсутствуют|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Итог|Количество всех неудачных вызовов для получения списка заданий.|Отсутствуют|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Итог|Количество всех успешных вызовов для отмены заданий.|Отсутствуют|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Итог|Количество всех неудачных вызовов для отмены заданий.|Отсутствуют|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Итог|Количество всех успешных вызовов для запроса заданий.|Отсутствуют|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Итог|Количество всех неудачных вызовов для запроса заданий.|Отсутствуют|
|jobs.completed|Завершенные задания|Count|Итог|Количество всех выполненных заданий.|Отсутствуют|
|jobs.failed|Неудачные задания.|Count|Итог|Количество всех неудачных заданий.|Отсутствуют|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Итог|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Отсутствуют|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Среднее|Количество сообщений, использованных сегодня|Отсутствуют|
|deviceDataUsage|Общий объем использования данных устройствами|Байты|Итог|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Отсутствуют|
|deviceDataUsageV2|Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))|Байты|Итог|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Отсутствуют|
|totalDeviceCount|Total devices (preview) (Всего устройств (предварительная версия))|Count|Среднее|Число устройств, зарегистрированных в Центре Интернета вещей.|Отсутствуют|
|connectedDeviceCount|Connected devices (preview) (Подключенных устройств (предварительная версия))|Count|Среднее|Число устройств, подключенных к Центру Интернета вещей.|Отсутствуют|
|конфигурации|Configuration Metrics (Метрики конфигурации)|Count|Итог|Метрики для операций конфигурации|Отсутствуют|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Count|Итог|Количество попыток регистрации устройств|Провисионингсервиценаме, IotHubName, состояние|
|DeviceAssignments|Назначенные устройства|Count|Итог|Количество устройств, назначенных Центру Интернета вещей|Провисионингсервиценаме, IotHubName|
|AttestationAttempts|Попытки аттестации|Count|Итог|Количество попыток аттестации устройств|Провисионингсервиценаме, состояние, протокол|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|аддрегион|Регион добавлен|Count|Count|Регион добавлен|Регион|
|AvailableStorage|Доступная служба хранилища|Байты|Итог|Общее Доступное хранилище получено по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|CassandraConnectionClosures|Отключение связи Cassandra|Count|Итог|Число закрытых подключений Cassandra, отправленных в отчет с детализацией по 1 минуте|Апитипе, регион, Клосуререасон|
|кассандракэйспацеделете|Cassandra пространства ключей удален|Count|Count|Cassandra пространства ключей удален|ResourceName, типа API, Апикиндресаурцетипе, OperationType|
|кассандракэйспацесраугхпутупдате|Пропускная способность Cassandra пространства ключей обновлена|Count|Count|Пропускная способность Cassandra пространства ключей обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|кассандракэйспацеупдате|Cassandra пространства ключей обновлен|Count|Count|Cassandra пространства ключей обновлен|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|CassandraRequestCharges|Расходы запросов по Cassandra|Count|Итог|Для запросов Cassandra используется параметр "RUs"|Апитипе, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Запросы по Cassandra|Count|Count|Число выполненных запросов Cassandra|Апитипе, DatabaseName, CollectionName, регион, OperationType, ResourceType, ErrorCode|
|кассандратабледелете|Таблица Cassandra удалена|Count|Count|Таблица Cassandra удалена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, OperationType|
|кассандратаблесраугхпутупдате|Пропускная способность таблицы Cassandra обновлена|Count|Count|Пропускная способность таблицы Cassandra обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|кассандратаблеупдате|Таблица Cassandra обновлена|Count|Count|Таблица Cassandra обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|креатеаккаунт|Учетная запись создана|Count|Count|Учетная запись создана|Отсутствуют|
|DataUsage|Использование данных|Байты|Итог|Общая степень использования данных в 5 минут|CollectionName, DatabaseName, регион|
|делетеаккаунт|Учетная запись удалена|Count|Count|Учетная запись удалена|Отсутствуют|
|DocumentCount|Число документов|Count|Итог|Общее число документов, о которых сообщило 5 минут|CollectionName, DatabaseName, регион|
|DocumentQuota|Квота на документы|Байты|Итог|Общая квота хранилища, полученная на уровне 5 минут|CollectionName, DatabaseName, регион|
|гремлиндатабаседелете|База данных Gremlin удалена|Count|Count|База данных Gremlin удалена|ResourceName, типа API, Апикиндресаурцетипе, OperationType|
|гремлиндатабасесраугхпутупдате|Пропускная способность базы данных Gremlin обновлена|Count|Count|Пропускная способность базы данных Gremlin обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|гремлиндатабасеупдате|База данных Gremlin обновлена|Count|Count|База данных Gremlin обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|гремлинграфделете|Граф Gremlin удален|Count|Count|Граф Gremlin удален|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, OperationType|
|гремлинграфсраугхпутупдате|Пропускная способность графа Gremlin обновлена|Count|Count|Пропускная способность графа Gremlin обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|гремлинграфупдате|Граф Gremlin обновлен|Count|Count|Граф Gremlin обновлен|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|IndexUsage|Использование индексов|Байты|Итог|Общее использование индекса, полученное по степени гранулярности в 5 минут|CollectionName, DatabaseName, регион|
|MetadataRequests|Запросы метаданных|Count|Count|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|DatabaseName, CollectionName, регион, StatusCode, роль|
|монгоколлектионделете|Коллекция Mongo удалена|Count|Count|Коллекция Mongo удалена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, OperationType|
|монгоколлектионсраугхпутупдате|Пропускная способность коллекции Mongo обновлена|Count|Count|Пропускная способность коллекции Mongo обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|монгоколлектионупдате|Коллекция Mongo обновлена|Count|Count|Коллекция Mongo обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|монгодбдатабасеупдате|База данных Mongo обновлена|Count|Count|База данных Mongo обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|монгодатабаседелете|База данных Mongo удалена|Count|Count|База данных Mongo удалена|ResourceName, типа API, Апикиндресаурцетипе, OperationType|
|монгодатабасесраугхпутупдате|Пропускная способность базы данных Mongo обновлена|Count|Count|Пропускная способность базы данных Mongo обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|MongoRequestCharge|Запросить оплату Mongo|Count|Итог|Использованные единицы запросов Mongo|DatabaseName, CollectionName, регион, CommandName, ErrorCode, состояние|
|MongoRequests|Запросы Mongo|Count|Count|Количество выполненных запросов Mongo|DatabaseName, CollectionName, регион, CommandName, ErrorCode, состояние|
|MongoRequestsCount|Частота запросов Mongo|Число/с|Среднее|Число запросов Mongo в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|MongoRequestsDelete|Частота запросов Mongo на удаление|Число/с|Среднее|Mongoных запросов на удаление в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|MongoRequestsInsert|Частота запросов Mongo на вставку|Число/с|Среднее|Число вставок Mongo в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|MongoRequestsQuery|Частота запросов Mongo на чтение|Число/с|Среднее|Mongo запросов в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|MongoRequestsUpdate|Частота запросов Mongo на обновление|Число/с|Среднее|Mongo запросов на обновление в секунду|DatabaseName, CollectionName, регион, CommandName, ErrorCode|
|нормализедруконсумптион|Нормализованное потребление единиц запросов|Процент|Максимальная|Максимальный процент потребления единиц запросов в минуту|CollectionName, DatabaseName, регион|
|ProvisionedThroughput|Подготовленная пропускная способность|Count|Максимальная|Подготовленная пропускная способность|DatabaseName, CollectionName|
|регионфаиловер|Отработка отказа региона|Count|Count|Отработка отказа региона|Отсутствуют|
|ремоверегион|Регион удален|Count|Count|Регион удален|Регион|
|ReplicationLatency|Задержка репликации P99|MilliSeconds|Среднее|Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи|Саурцерегион, Таржетрегион|
|серверсиделатенци|Задержка на стороне сервера|MilliSeconds|Среднее|Задержка на стороне сервера|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, Публикапитипе|
|ServiceAvailability|Доступность службы|Процент|Среднее|Доступность запросов учетной записи за один час, с детализацией дня или месяца|Отсутствуют|
|склконтаинерделете|Контейнер SQL удален|Count|Count|Контейнер SQL удален|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, OperationType|
|склконтаинерсраугхпутупдате|Пропускная способность контейнера SQL обновлена|Count|Count|Пропускная способность контейнера SQL обновлена|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|склконтаинерупдате|Контейнер SQL обновлен|Count|Count|Контейнер SQL обновлен|ResourceName, Чилдресаурценаме, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|склдатабаседелете|База данных SQL удалена|Count|Count|База данных SQL удалена|ResourceName, типа API, Апикиндресаурцетипе, OperationType|
|склдатабасесраугхпутупдате|Пропускная способность базы данных SQL обновлена|Count|Count|Пропускная способность базы данных SQL обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|склдатабасеупдате|База данных SQL обновлена|Count|Count|База данных SQL обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|таблетабледелете|Таблица AzureTable удалена|Count|Count|Таблица AzureTable удалена|ResourceName, типа API, Апикиндресаурцетипе, OperationType|
|таблетаблесраугхпутупдате|Пропускная способность таблицы AzureTable обновлена|Count|Count|Пропускная способность таблицы AzureTable обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|таблетаблеупдате|Таблица AzureTable обновлена|Count|Count|Таблица AzureTable обновлена|ResourceName, типа API, Апикиндресаурцетипе, Иссраугхпутрекуест|
|TotalRequestUnits|Общее количество единиц запросов|Count|Итог|Использованные единицы запросов|DatabaseName, CollectionName, регион, StatusCode, OperationType, состояние|
|TotalRequests|Общее количество запросов|Count|Count|Количество выполненных запросов|DatabaseName, CollectionName, регион, StatusCode, OperationType, состояние|
|упдатеаккаунткэйс|Ключи учетной записи обновлены|Count|Count|Ключи учетной записи обновлены|KeyType|
|упдатеаккаунтнетворксеттингс|Параметры сети учетной записи обновлены|Count|Count|Параметры сети учетной записи обновлены|Отсутствуют|
|упдатеаккаунтрепликатионсеттингс|Параметры репликации учетной записи обновлены|Count|Count|Параметры репликации учетной записи обновлены|Отсутствуют|
|упдатедиагностикссеттингс|Параметры диагностики учетной записи обновлены|Count|Count|Параметры диагностики учетной записи обновлены|Диагностиксеттингснаме, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TransactionCount|Число транзакций|Count|Count|Общее число транзакций|TransactionCount|
|SuccessCount|Количество успешных событий|Count|Count|Число выполненных транзакций|SuccessCount|
|FailureCount|Количество сбоев|Count|Count|Число невыполненных транзакций|FailureCount|
|SuccessLatency|Задержка успешных событий|MilliSeconds|Среднее|Задержка успешных транзакций|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|Раздел|
|PublishFailCount|События с ошибками публикации|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|Раздел, ErrorType, ошибка|
|PublishSuccessLatencyInMs|Задержка успешной публикации|Миллисекунды|Итог|Задержка успешной публикации в миллисекундах|Отсутствуют|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Error, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Дропреасон|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|Раздел, Евентсубскриптионнаме, Домаиневентсубскриптионнаме, Деадлеттерреасон|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|Отсутствуют|
|PublishFailCount|События с ошибками публикации|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Отсутствуют|
|PublishSuccessLatencyInMs|Задержка успешной публикации|Миллисекунды|Итог|Задержка успешной публикации в миллисекундах|Отсутствуют|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|евентсубскриптионнаме|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка, ErrorType, Евентсубскриптионнаме|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|евентсубскриптионнаме|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|евентсубскриптионнаме|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|Дропреасон, Евентсубскриптионнаме|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|Деадлеттерреасон, Евентсубскриптионнаме|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/Системтопикс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|Отсутствуют|
|PublishFailCount|События с ошибками публикации|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Отсутствуют|
|PublishSuccessLatencyInMs|Задержка успешной публикации|Миллисекунды|Итог|Задержка успешной публикации в миллисекундах|Отсутствуют|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|евентсубскриптионнаме|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка, ErrorType, Евентсубскриптионнаме|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|евентсубскриптионнаме|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|евентсубскриптионнаме|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|Дропреасон, Евентсубскриптионнаме|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|Деадлеттерреасон, Евентсубскриптионнаме|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|MatchedEventCount|Соответствующие события|Count|Итог|Всего событий, которые соответствуют этой подписке на события|Отсутствуют|
|DeliveryAttemptFailCount|Delivery Failed Events (Недоставленные события)|Count|Итог|Всего событий, которые не удалось доставить в эту подписку на события|Ошибка, ErrorType|
|DeliverySuccessCount|Delivered Events (Доставленные события)|Count|Итог|Всего событий, доставленных в эту подписку на события|Отсутствуют|
|DestinationProcessingDurationInMs|Destination Processing Duration (Длительность обработки назначения)|Миллисекунды|Среднее|Длительность обработки назначения в миллисекундах|Отсутствуют|
|DroppedEventCount|Удаленные события|Count|Итог|Всего удаленных событий, которые соответствуют этой подписке на события|дропреасон|
|DeadLetteredCount|Dead Lettered Events (Невостребованные события)|Count|Итог|Всего невостребованных событий, которые соответствуют этой подписке на события|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events (Опубликованные события)|Count|Итог|Всего событий, опубликованных в этом разделе|Отсутствуют|
|PublishFailCount|События с ошибками публикации|Count|Итог|Всего событий, которые не удалось опубликовать в этом разделе|ErrorType, ошибка|
|UnmatchedEventCount|Unmatched Events (Несоответствующие события)|Count|Итог|Всего событий, не соответствующих ни одной из подписок на события в этом разделе|Отсутствуют|
|PublishSuccessLatencyInMs|Задержка успешной публикации|Миллисекунды|Итог|Задержка успешной публикации в миллисекундах|Отсутствуют|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Выполненные запросы для Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Итог|Ошибки превышения квоты для Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.EventHub.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.EventHub.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.EventHub.|EntityName|
|IncomingBytes|Входящие байты.|Байты|Итог|Входящие байты для Microsoft.EventHub.|EntityName|
|OutgoingBytes|Исходящие байты.|Байты|Итог|Исходящие байты для Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Среднее|Число активных подключений для Microsoft.EventHub.|Отсутствуют|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.EventHub.|EntityName|
|CaptureBacklog|Невыполненная работа записи.|Count|Итог|Невыполненная работа записи для Microsoft.EventHub.|EntityName|
|CapturedMessages|Записанные сообщения.|Count|Итог|Записанные сообщения для Microsoft.EventHub.|EntityName|
|CapturedBytes|Записанные байты.|Байты|Итог|Записанные байты для Microsoft.EventHub.|EntityName|
|Размер|Размер|Байты|Среднее|Размер EventHub в байтах.|EntityName|
|INREQS|Входящие запросы (не рекомендуется)|Count|Итог|Всего входящих запросов на отправку для пространства имен (не рекомендуется)|Отсутствуют|
|SUCCREQ|Успешные запросы (не рекомендуется)|Count|Итог|Общее количество успешных запросов для пространства имен (не рекомендуется)|Отсутствуют|
|FAILREQ|Неудачные запросы (не рекомендуется)|Count|Итог|Всего неудачных запросов для пространства имен (не рекомендуется)|Отсутствуют|
|SVRBSY|Ошибки из-за занятости сервера (не рекомендуется)|Count|Итог|Общее число ошибок "сервер занят" для пространства имен (не рекомендуется)|Отсутствуют|
|INTERR|Внутренние ошибки сервера (не рекомендуется)|Count|Итог|Общее количество внутренних ошибок сервера для пространства имен (не рекомендуется)|Отсутствуют|
|MISCERR|Прочие ошибки (не рекомендуется)|Count|Итог|Всего неудачных запросов для пространства имен (не рекомендуется)|Отсутствуют|
|INMSGS|Входящие сообщения (устаревшие) (не рекомендуется)|Count|Итог|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих сообщений (не рекомендуется).|Отсутствуют|
|EHINMSGS|Входящие сообщения (не рекомендуется)|Count|Итог|Всего входящих сообщений для пространства имен (не рекомендуется)|Отсутствуют|
|OUTMSGS|Исходящие сообщения (устаревшие) (не рекомендуется)|Count|Итог|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих сообщений (не рекомендуется).|Отсутствуют|
|EHOUTMSGS|Исходящие сообщения (не рекомендуется)|Count|Итог|Всего исходящих сообщений для пространства имен (не рекомендуется)|Отсутствуют|
|EHINMBS|Входящие байты (устаревшие) (не рекомендуется)|Байты|Итог|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику входящих байт (не рекомендуется).|Отсутствуют|
|EHINBYTES|Входящие байты (не рекомендуется)|Байты|Итог|Пропускная способность входящего сообщения концентратора событий для пространства имен (не рекомендуется)|Отсутствуют|
|EHOUTMBS|Исходящие байты (устаревшие) (не рекомендуется)|Байты|Итог|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику исходящих байт (не рекомендуется).|Отсутствуют|
|EHOUTBYTES|Исходящие байты (не рекомендуется)|Байты|Итог|Пропускная способность исходящего сообщения концентратора событий для пространства имен (не рекомендуется)|Отсутствуют|
|EHABL|Архивные сообщения невыполненной работы (не рекомендуется)|Count|Итог|Архивные сообщения концентратора событий в невыполненной работе для пространства имен (не рекомендуется)|Отсутствуют|
|EHAMSGS|Архивные сообщения (не рекомендуется)|Count|Итог|Архивные сообщения концентратора событий в пространстве имен (не рекомендуется)|Отсутствуют|
|EHAMBS|Пропускная способность архивных сообщений (не рекомендуется)|Байты|Итог|Пропускная способность архивного сообщения концентратора событий в пространстве имен (не рекомендуется)|Отсутствуют|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Выполненные запросы для Microsoft.EventHub.|OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.EventHub.|OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Ошибки превышения квоты.|Count|Итог|Ошибки превышения квоты для Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.EventHub.|OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.EventHub.|Отсутствуют|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.EventHub.|Отсутствуют|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.EventHub.|Отсутствуют|
|IncomingBytes|Входящие байты.|Байты|Итог|Входящие байты для Microsoft.EventHub.|Отсутствуют|
|OutgoingBytes|Исходящие байты.|Байты|Итог|Исходящие байты для Microsoft.EventHub.|Отсутствуют|
|ActiveConnections|ActiveConnections|Count|Среднее|Число активных подключений для Microsoft.EventHub.|Отсутствуют|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.EventHub.|Отсутствуют|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.EventHub.|Отсутствуют|
|CaptureBacklog|Невыполненная работа записи.|Count|Итог|Невыполненная работа записи для Microsoft.EventHub.|Отсутствуют|
|CapturedMessages|Записанные сообщения.|Count|Итог|Записанные сообщения для Microsoft.EventHub.|Отсутствуют|
|CapturedBytes|Записанные байты.|Байты|Итог|Записанные байты для Microsoft.EventHub.|Отсутствуют|
|ЦП|ЦП|Процент|Максимальная|Использование ЦП для кластера концентратора событий в процентах|Роль|
|AvailableMemory|Доступная память|Процент|Максимальная|Объем доступной памяти для кластера концентраторов событий в процентах от общего объема памяти.|Роль|
|Размер|Размер EventHub в байтах.|Байты|Среднее|Размер EventHub в байтах.|Роль|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Count|Итог|Число запросов к шлюзу|HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Count|Итог|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|HttpStatus|
|NumActiveWorkers|Число активных рабочих ролей|Count|Максимальная|Число активных рабочих ролей|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Count|Среднее|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Count|Среднее|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Count|Среднее|Емкость, передаваемая для автомасштабирования при выполнении.|Отсутствуют|
|ScaleActionsInitiated|Инициированные действия масштабирования|Count|Итог|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Доступность|Процент|Среднее|Процент успешно завершенных тестов доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location|
|availabilityResults/duration|Тесты доступности|Count|Count|Число тестов доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location, Аваилабилитиресулт/Success|
|availabilityResults/duration|Длительность теста доступности|MilliSeconds|Среднее|Длительность теста доступности|Аваилабилитиресулт/Name, Аваилабилитиресулт/Location, Аваилабилитиресулт/Success|
|browserTimings/networkDuration|Время подключения к сети при загрузке страницы|MilliSeconds|Среднее|Время между запросом пользователя и сетевым подключением. Включает время поиска DNS и транспортного подключения.|Отсутствуют|
|browserTimings/processingDuration|Время обработки клиента|MilliSeconds|Среднее|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.|Отсутствуют|
|browserTimings/receiveDuration|Время получения ответа|MilliSeconds|Среднее|Время от первого до последнего байта или до отключения.|Отсутствуют|
|browserTimings/sendDuration|Время отправки запроса|MilliSeconds|Среднее|Время от установки сетевого подключения до получения первого байта.|Отсутствуют|
|browserTimings/totalDuration|Время загрузки страницы в браузере|MilliSeconds|Среднее|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.|Отсутствуют|
|dependencies/count|Вызовы зависимостей|Count|Count|Число вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, зависимость/resultCode, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|dependencies/duration|Длительность зависимости|MilliSeconds|Среднее|Длительность вызовов, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, зависимость/resultCode, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|dependencies/failed|Сбои при вызове зависимости|Count|Count|Число завершившихся сбоем вызовов зависимостей, отправленных приложением к внешним ресурсам.|зависимость/тип, зависимость/Перформанцебуккет, зависимость/успешное завершение, зависимость/целевой объект, зависимость/resultCode, операция/искусственный, Облако/roleInstance, Cloud/roleName|
|pageViews/count|Просмотры страниц|Count|Count|Число просмотров страниц.|операция, искусственная, облачная/roleName|
|pageViews/duration|Время загрузки страницы для просмотра|MilliSeconds|Среднее|Время загрузки страницы для просмотра|операция, искусственная, облачная/roleName|
|performanceCounters/requestExecutionTime|Время выполнения HTTP-запроса|MilliSeconds|Среднее|Время выполнения самого последнего запроса.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue (HTTP-запросы в очереди приложений)|Count|Среднее|Длина очереди запросов приложений.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Скорость HTTP-запроса|Число/с|Среднее|Частота всех запросов из ASP.NET к приложению в секунду.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Частота исключений|Число/с|Среднее|Количество обработанных и необработанных исключений, о которых сообщается в Windows, включая исключения .NET и неуправляемые исключения, которые преобразованы в исключения .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Скорость ввода-вывода процесса|Байт/с|Среднее|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Обработка ЦП|Процент|Среднее|Процент времени, в течение которого все потоки процесса использовали процессор для выполнения инструкций. Значение может варьироваться в диапазоне от 0 до 100. Эта метрика показывает только производительность процесса w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Процессорное время|Процент|Среднее|Процент времени, затраченного процессором на активные потоки.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Объем доступной памяти|Байты|Среднее|Физическая память, доступная для немедленного использования процессами или системой.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Количество байтов исключительного использования процессов|Байты|Среднее|Память, выделенная исключительно для процессов наблюдаемого приложения.|cloud/roleInstance|
|requests/duration|Время ответа от сервера|MilliSeconds|Среднее|Время с момента получения HTTP-запроса до завершения отправки ответа.|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|requests/count|Запросы сервера|Count|Count|Число выполненных запросов HTTP.|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|requests/failed|Failed requests (Неудачные запросы)|Count|Count|Число HTTP-запросов, помеченных как невыполненные. В большинстве случаев это запросы с кодами отклика >= 400, кроме 401.|запрос/Перформанцебуккет, запрос/resultCode, запрос/успешно, операция, искусственный, облако или roleInstance, Облако/roleName|
|requests/rate|Частота запросов к серверу|Число/с|Среднее|Частота запросов сервера в секунду|запрос/Перформанцебуккет, запрос/resultCode, операция, искусственный, облако, roleInstance, запрос/успешно, Облако/roleName|
|exceptions/count|Исключения|Count|Count|Общее число всех неперехваченных исключений.|Облако/roleName, Cloud/roleInstance, клиент/тип|
|exceptions/browser|Исключения браузера|Count|Count|Число необработанных исключений в браузере.|клиент/сервер, Облако/roleName|
|exceptions/server|Исключения сервера|Count|Count|Число неперехваченных исключений, созданных в серверном приложении.|клиент/сервер, Облако/roleName, Cloud/roleInstance|
|traces/count|Трассировки|Count|Count|Число документов трассировки|Trace/Северитилевел, Operation/синтетическая, облачная/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. Иотцентрал/Иотаппс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|connectedDeviceCount|Всего подключенных устройств|Count|Среднее|Число устройств, подключенных к IoT Central|Отсутствуют|
|C2D. свойство. Read. Success|Успешное чтение свойства устройства из IoT Central|Count|Итог|Число всех успешных операций чтения свойств, инициированных из IoT Central|Отсутствуют|
|C2D. свойство. Read. Failure|Сбой чтения свойства устройства из IoT Central|Count|Итог|Число всех неудачных операций чтения свойств, инициированных из IoT Central|Отсутствуют|
|D2C. свойство. Read. Success|Успешное чтение свойств устройства с устройств|Count|Итог|Число всех успешных операций чтения свойств, инициированных с устройств|Отсутствуют|
|D2C. свойство. Read. Failure|Сбой чтения свойств устройства с устройств|Count|Итог|Число всех неудачных операций чтения свойств, инициированных с устройств|Отсутствуют|
|C2D. свойство. Update. Success|Успешное обновление свойств устройства с IoT Central|Count|Итог|Число всех успешных обновлений свойств, инициированных из IoT Central|Отсутствуют|
|C2D. свойство. обновление. сбой|Не удалось обновить свойства устройства с IoT Central|Count|Итог|Число всех неудачных обновлений свойств, инициированных из IoT Central|Отсутствуют|
|D2C. свойство. Update. Success|Успешное обновление свойств устройства с устройств|Count|Итог|Число всех успешных обновлений свойств, инициированных с устройств|Отсутствуют|
|D2C. свойство. обновление. сбой|Не удалось обновить свойства устройства с устройств|Count|Итог|Число всех неудачных обновлений свойств, инициированных с устройств|Отсутствуют|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Count|Count|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунды|Среднее|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode, Статускодекласс|
|ServiceApiResult|Всего результатов для API службы|Count|Count|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode, Статускодекласс|
|сатуратионшоебокс|Общая насыщенность хранилища|Процент|Среднее|Используемая емкость хранилища|ActivityType, ActivityName, Трансактионтипе|
|Доступность|Общая доступность хранилища|Процент|Среднее|Доступность запросов хранилища|ActivityType, ActivityName, StatusCode, Статускодекласс|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|CacheUtilization|Использование кэша|Процент|Среднее|Уровень загрузки в области кластера|Отсутствуют|
|QueryDuration|Query duration (Длительность запросов)|Миллисекунды|Среднее|Длительность запросов в секундах|QueryStatus|
|IngestionUtilization|Использование приема|Процент|Среднее|Доля использованных слотов приема данных в кластере|Отсутствуют|
|KeepAlive|Проверка активности|Count|Среднее|Проверка работоспособности показывает, что кластер отвечает на запросы|Отсутствуют|
|IngestionVolumeInMB|Объем приема (в МБ)|Count|Итог|Общий объем данных, принятых в кластере (в МБ)|База данных|
|IngestionLatencyInSeconds|Задержка приема (в секундах)|Секунды|Среднее|Время приема данных из источника, например из концентратора событий, в кластер (в секундах)|Отсутствуют|
|EventsProcessedForEventHubs|Обработанные события (для концентраторов событий и Центров Интернета вещей)|Count|Итог|Число событий, обработанных кластером при приеме из события или центра Интернета вещей|EventStatus|
|IngestionResult|Результат приема|Count|Count|Число операций приема данных|IngestionResultDetails|
|ЦП|ЦП|Процент|Среднее|Уровень загрузки ЦП|Отсутствуют|
|ContinuousExportNumOfRecordsExported|Непрерывный экспорт — число экспортированных записей|Count|Итог|Число экспортированных записей, которое срабатывает для каждого артефакта хранилища, записанного во время операции экспорта|Континуаусекспортнаме, база данных|
|ExportUtilization|Использование экспорта|Процент|Максимальная|Использование экспорта|Отсутствуют|
|ContinuousExportPendingCount|Число ожидающих непрерывного экспорта|Count|Максимальная|Число ожидающих заданий непрерывного экспорта, готовых к выполнению|Отсутствуют|
|ContinuousExportMaxLatenessMinutes|Максимальная длина непрерывного экспорта|Count|Максимальная|Задержку (в минутах), сообщаемую заданиями непрерывного экспорта в кластере|Отсутствуют|
|ContinuousExportResult|Результат непрерывного экспорта|Count|Count|Указывает, завершился ли непрерывный экспорт успешно или нет|Континуаусекспортнаме, результат, база данных|
|StreamingIngestDuration|Длительность потокового приема|Миллисекунды|Среднее|Длительность приема потоковой передачи в миллисекундах|Отсутствуют|
|StreamingIngestDataRate|Скорость данных потокового приема|Count|Среднее|Скорость приема данных потоковой передачи (МБ в секунду)|Отсутствуют|
|SteamingIngestRequestRate|Частота запросов потокового приема|Count|RateRequestsPerSecond|Частота запросов приема потоковой передачи (запросов в секунду)|Отсутствуют|
|StreamingIngestResults|Результат потокового приема|Count|Среднее|Поток приема результатов|Результат|
|тоталнумберофконкурренткуериес|Общее число одновременных запросов|Count|Итог|Общее число одновременных запросов|Отсутствуют|
|тоталнумберофсроттледкуериес|Общее число регулируемых запросов|Count|Итог|Общее число регулируемых запросов|Отсутствуют|
|тоталнумберофсроттледкоммандс|Общее число регулируемых команд|Count|Итог|Общее число регулируемых команд|CommandType|
|тоталнумберофекстентс|Общее число экстентов|Count|Итог|Общее число экстентов данных|Отсутствуют|
|InstanceCount|Число экземпляров|Count|Среднее|Общее число экземпляров|Отсутствуют|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итог|Число запущенных выполнений рабочего процесса.|Отсутствуют|
|RunsCompleted|Runs Completed|Count|Итог|Число завершенных выполнений рабочего процесса.|Отсутствуют|
|RunsSucceeded|Runs Succeeded|Count|Итог|Число успешно завершенных выполнений рабочего процесса.|Отсутствуют|
|RunsFailed|Runs Failed|Count|Итог|Число выполнений рабочего процесса, завершившихся сбоем.|Отсутствуют|
|RunsCancelled|Runs Cancelled|Count|Итог|Число отмененных выполнений рабочего процесса.|Отсутствуют|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|Отсутствуют|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|Отсутствуют|
|RunThrottledEvents|Run Throttled Events|Count|Итог|Число событий регулирования действия или триггера рабочего процесса.|Отсутствуют|
|RunStartThrottledEvents|События регулировки начала запуска|Count|Итог|Число перерегулируемых событий запуска рабочего процесса.|Отсутствуют|
|RunFailurePercentage|Run Failure Percentage|Процент|Итог|Процент выполнений рабочего процесса, завершившихся сбоем.|Отсутствуют|
|ActionsStarted|Actions Started |Count|Итог|Число запущенных действий рабочего процесса.|Отсутствуют|
|ActionsCompleted|Actions Completed |Count|Итог|Число завершенных действий рабочего процесса.|Отсутствуют|
|ActionsSucceeded|Actions Succeeded |Count|Итог|Число успешно выполненных действий рабочего процесса.|Отсутствуют|
|ActionsFailed|Actions Failed |Count|Итог|Число действий рабочего процесса, завершившихся сбоем.|Отсутствуют|
|ActionsSkipped|Actions Skipped |Count|Итог|Число пропущенных действий рабочего процесса.|Отсутствуют|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|Отсутствуют|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|Отсутствуют|
|ActionThrottledEvents|Action Throttled Events|Count|Итог|Число событий регулирования действия рабочего процесса.|Отсутствуют|
|TriggersStarted|Triggers Started |Count|Итог|Число запущенных триггеров рабочего процесса.|Отсутствуют|
|TriggersCompleted|Triggers Completed |Count|Итог|Число завершенных триггеров рабочего процесса.|Отсутствуют|
|TriggersSucceeded|Triggers Succeeded |Count|Итог|Число успешно выполненных триггеров рабочего процесса.|Отсутствуют|
|TriggersFailed|Triggers Failed |Count|Итог|Число триггеров рабочего процесса, завершившихся со сбоем.|Отсутствуют|
|TriggersSkipped|Triggers Skipped|Count|Итог|Число пропущенных триггеров рабочего процесса.|Отсутствуют|
|TriggersFired|Triggers Fired |Count|Итог|Число активированных триггеров рабочего процесса.|Отсутствуют|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|Отсутствуют|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|Отсутствуют|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|Отсутствуют|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итог|Число событий регулирования триггера рабочего процесса.|Отсутствуют|
|BillableActionExecutions|Billable Action Executions|Count|Итог|Число выполненных действий рабочего процесса, за которые выставляется счет.|Отсутствуют|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Итог|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Отсутствуют|
|TotalBillableExecutions|Total Billable Executions|Count|Итог|Число выполнений рабочего процесса, за которые выставляется счет.|Отсутствуют|
|BillingUsageNativeOperation|Выставление счетов за внутренние операции|Count|Итог|Число выполнений внутренних операций, за которые выставляется счет.|Отсутствуют|
|BillingUsageStandardConnector|Выставление счетов за операции стандартного соединителя|Count|Итог|Число операций стандартного соединителя, за которые выставляется счет.|Отсутствуют|
|BillingUsageStorageConsumption|Выставление счетов за операции с использованием хранилища|Count|Итог|Число операций с использованием хранилища, за которые выставляется счет.|Отсутствуют|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Итог|Число запущенных выполнений рабочего процесса.|Отсутствуют|
|RunsCompleted|Runs Completed|Count|Итог|Число завершенных выполнений рабочего процесса.|Отсутствуют|
|RunsSucceeded|Runs Succeeded|Count|Итог|Число успешно завершенных выполнений рабочего процесса.|Отсутствуют|
|RunsFailed|Runs Failed|Count|Итог|Число выполнений рабочего процесса, завершившихся сбоем.|Отсутствуют|
|RunsCancelled|Runs Cancelled|Count|Итог|Число отмененных выполнений рабочего процесса.|Отсутствуют|
|RunLatency|Run Latency|Секунды|Среднее|Задержка завершенных выполнений рабочего процесса.|Отсутствуют|
|RunSuccessLatency|Run Success Latency|Секунды|Среднее|Задержка успешно завершенных выполнений рабочего процесса.|Отсутствуют|
|RunThrottledEvents|Run Throttled Events|Count|Итог|Число событий регулирования действия или триггера рабочего процесса.|Отсутствуют|
|RunStartThrottledEvents|События регулировки начала запуска|Count|Итог|Число перерегулируемых событий запуска рабочего процесса.|Отсутствуют|
|RunFailurePercentage|Run Failure Percentage|Процент|Итог|Процент выполнений рабочего процесса, завершившихся сбоем.|Отсутствуют|
|ActionsStarted|Actions Started |Count|Итог|Число запущенных действий рабочего процесса.|Отсутствуют|
|ActionsCompleted|Actions Completed |Count|Итог|Число завершенных действий рабочего процесса.|Отсутствуют|
|ActionsSucceeded|Actions Succeeded |Count|Итог|Число успешно выполненных действий рабочего процесса.|Отсутствуют|
|ActionsFailed|Actions Failed |Count|Итог|Число действий рабочего процесса, завершившихся сбоем.|Отсутствуют|
|ActionsSkipped|Actions Skipped |Count|Итог|Число пропущенных действий рабочего процесса.|Отсутствуют|
|ActionLatency|Action Latency |Секунды|Среднее|Задержка завершенных действий рабочего процесса.|Отсутствуют|
|ActionSuccessLatency|Action Success Latency |Секунды|Среднее|Задержка успешно выполненных действий рабочего процесса.|Отсутствуют|
|ActionThrottledEvents|Action Throttled Events|Count|Итог|Число событий регулирования действия рабочего процесса.|Отсутствуют|
|TriggersStarted|Triggers Started |Count|Итог|Число запущенных триггеров рабочего процесса.|Отсутствуют|
|TriggersCompleted|Triggers Completed |Count|Итог|Число завершенных триггеров рабочего процесса.|Отсутствуют|
|TriggersSucceeded|Triggers Succeeded |Count|Итог|Число успешно выполненных триггеров рабочего процесса.|Отсутствуют|
|TriggersFailed|Triggers Failed |Count|Итог|Число триггеров рабочего процесса, завершившихся со сбоем.|Отсутствуют|
|TriggersSkipped|Triggers Skipped|Count|Итог|Число пропущенных триггеров рабочего процесса.|Отсутствуют|
|TriggersFired|Triggers Fired |Count|Итог|Число активированных триггеров рабочего процесса.|Отсутствуют|
|TriggerLatency|Trigger Latency |Секунды|Среднее|Задержка завершенных триггеров рабочего процесса.|Отсутствуют|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Среднее|Задержка активированных триггеров рабочего процесса.|Отсутствуют|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|Отсутствуют|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Итог|Число событий регулирования триггера рабочего процесса.|Отсутствуют|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Загрузка процессора рабочего процесса для среды службы интеграции|Процент|Среднее|Использование процессора рабочих процессов для среды службы интеграции.|Отсутствуют|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Использование памяти рабочего процесса для среды службы интеграции|Процент|Среднее|Использование памяти рабочего процесса для среды службы интеграции.|Отсутствуют|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Загрузка процессора соединителя для среды службы интеграции|Процент|Среднее|Использование процессора соединителя для среды службы интеграции.|Отсутствуют|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Использование памяти соединителя для среды службы интеграции|Процент|Среднее|Использование памяти соединителя для среды службы интеграции.|Отсутствуют|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Отмененные запуски|Отмененные запуски|Count|Итог|Число отмененных выполнений для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Отменить запрошенные запуски|Отменить запрошенные запуски|Count|Итог|Число запусков, в которых была запрошена отмена для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Завершенные запуски|Завершенные запуски|Count|Итог|Число выполнений, успешно завершенных для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|циклы выполнения со сбоем;|циклы выполнения со сбоем;|Count|Итог|Число запусков, завершившихся с этой рабочей областью|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Завершение выполнения|Завершение выполнения|Count|Итог|Число выполнений, вошедших в состояние "завершено" для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запуски не отвечают|Запуски не отвечают|Count|Итог|Число запусков, не отвечающих в этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запуски не запущены|Запуски не запущены|Count|Итог|Число запусков в состоянии "не запущено" для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Подготовка запусков|Подготовка запусков|Count|Итог|Число запусков, которые подготавливаются для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запуски подготовки|Запуски подготовки|Count|Итог|Число запусков, которые подготавливается для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запуски в очереди|Запуски в очереди|Count|Итог|Число запусков, поставленных в очередь для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Начатые запуски|Начатые запуски|Count|Итог|Число запусков, запущенных для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Запуск выполнений|Запуск выполнений|Count|Итог|Число запусков, запущенных для этой рабочей области|Сценарий, Рунтипе, Публишедпипелинеид, Компутетипе, Пипелинестептипе|
|Ошибки|ошибки|Count|Итог|Число ошибок запуска в этой рабочей области|Сценарий|
|Предупреждения|Предупреждения|Count|Итог|Число предупреждений о выполнении в этой рабочей области|Сценарий|
|Успешные регистрации модели|Успешные регистрации модели|Count|Итог|Число регистраций моделей, которые были успешной в этой рабочей области|Сценарий|
|Неудачные регистрации модели|Неудачные регистрации модели|Count|Итог|Число неудачных регистраций моделей в этой рабочей области|Сценарий, StatusCode|
|Начатые развертывания модели|Начатые развертывания модели|Count|Итог|Число развертываний моделей, запущенных в этой рабочей области|Сценарий|
|Успешные развертывания модели|Успешные развертывания модели|Count|Итог|Число развертываний моделей, завершившихся в этой рабочей области|Сценарий|
|Неудачные развертывания модели|Неудачные развертывания модели|Count|Итог|Число развертываний моделей, завершившихся сбоем в этой рабочей области|Сценарий, StatusCode|
|Всего узлов|Всего узлов|Count|Среднее|Общее число узлов. Сюда входят некоторые активные узлы, бездействующие узлы, неиспользуемые узлы, узлы Премептед и узлы.|Сценарий, имя_кластера|
|Активные узлы|Активные узлы|Count|Среднее|Число узлов Acitve. Это узлы, активно выполняющие задание.|Сценарий, имя_кластера|
|Бездействующие узлы|Бездействующие узлы|Count|Среднее|Количество бездействующих узлов. Бездействующие узлы — это узлы, на которых не выполняются никакие задания, но могут принимать новые задания, если они доступны.|Сценарий, имя_кластера|
|Недоступные для использования узлы|Недоступные для использования узлы|Count|Среднее|Количество неиспользуемых узлов. Неиспользуемые узлы не работают из-за неразрешимой проблемы. Azure выполнит повторный запуск этих узлов.|Сценарий, имя_кластера|
|Замещенные узлы|Замещенные узлы|Count|Среднее|Число узлов с вытеснением. Эти узлы являются узлами с низким приоритетом, взятыми из пула доступных узлов.|Сценарий, имя_кластера|
|Освобождаемые узлы|Освобождаемые узлы|Count|Среднее|Количество покидает узлов. Узлы — это узлы, которые только что завершили обработку задания и будут переходить в состояние простоя.|Сценарий, имя_кластера|
|Всего ядер|Всего ядер|Count|Среднее|Общее число ядер|Сценарий, имя_кластера|
|Активные ядра|Активные ядра|Count|Среднее|Число активных ядер|Сценарий, имя_кластера|
|Бездействующие ядра|Бездействующие ядра|Count|Среднее|Число бездействующих ядер|Сценарий, имя_кластера|
|Недоступные для использования ядра|Недоступные для использования ядра|Count|Среднее|Число неиспользуемых ядер|Сценарий, имя_кластера|
|Замещенные ядра|Замещенные ядра|Count|Среднее|Число ядер с вытеснением|Сценарий, имя_кластера|
|Освобождаемые ядра|Освобождаемые ядра|Count|Среднее|Число покидает ядер|Сценарий, имя_кластера|
|Процент использования квоты|Процент использования квоты|Count|Среднее|Процент использования квоты|Сценарий, имя_кластера, Вмфамилинаме, Вмприорити|
|CpuUtilization|CpuUtilization|Count|Среднее|CPU (Preview) (ЦП (предварительная версия))|Сценарий, runId, NodeId, время создания|
|гпуутилизатион|гпуутилизатион|Count|Среднее|GPU (Предварительная версия)|Сценарий, runId, NodeId, время создания, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Использование|Использование|Count|Count|Число вызовов API|Апикатегори, ApiName, ResultType, ResponseCode|
|Доступность|Доступность|Процент|Среднее|Доступность API-интерфейсов|Апикатегори, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Исходящие|Исходящие|Байты|Итог|Объем выходных данных в байтах.|OutputFormat|
|SuccessE2ELatency|Сквозная задержка успешного запроса|Миллисекунды|Среднее|Средняя задержка для успешных запросов в миллисекундах.|OutputFormat|
|Requests|Requests|Count|Итог|Запросы к конечной точке потоковой передачи.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|AssetQuota|Квота ресурсов|Count|Среднее|Сколько ресурсов разрешено для текущей учетной записи службы мультимедиа|Отсутствуют|
|AssetCount|Число ресурсов|Count|Среднее|Сколько ресурсов уже создано в текущей учетной записи службы мультимедиа|Отсутствуют|
|AssetQuotaUsedPercentage|Процент используемой квоты ресурсов|Процент|Среднее|Процент использования ресурса в текущей учетной записи службы мультимедиа|Отсутствуют|
|ContentKeyPolicyQuota|Квота политик ключей содержимого|Count|Среднее|Сколько политик ключей содержимого разрешено для текущей учетной записи службы мультимедиа|Отсутствуют|
|ContentKeyPolicyCount|Число политик ключей содержимого|Count|Среднее|Сколько политик ключей содержимого уже создано в текущей учетной записи службы мультимедиа|Отсутствуют|
|ContentKeyPolicyQuotaUsedPercentage|Процент использования квоты политик ключей содержимого|Процент|Среднее|Процент использования политики ключа содержимого в текущей учетной записи службы мультимедиа|Отсутствуют|
|StreamingPolicyQuota|Квота политик потоковой передачи|Count|Среднее|Сколько политик потоковой передачи разрешено для текущей учетной записи службы мультимедиа|Отсутствуют|
|StreamingPolicyCount|Число политик потоковой передачи|Count|Среднее|Сколько политик потоковой передачи уже созданы в текущей учетной записи службы мультимедиа|Отсутствуют|
|StreamingPolicyQuotaUsedPercentage|Процент использования квоты политик потоковой передачи|Процент|Среднее|Процент использования политики потоковой передачи в текущей учетной записи службы мультимедиа|Отсутствуют|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. Микседреалити/Ремотерендерингаккаунтс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ассетсконвертед|Активы преобразованы|Count|Итог|Общее число преобразованных активов|AppId, ResourceId, Сдкверсион|
|активерендерингсессионс|Активные сеансы подготовки к просмотру|Count|Итог|Общее число активных сеансов подготовки к просмотру|AppId, ResourceId, SessionType, Сдкверсион|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|AverageReadLatency|Средняя задержка чтения|MilliSeconds|Среднее|Средняя задержка чтения в миллисекундах на операцию|Отсутствуют|
|AverageWriteLatency|Средняя задержка записи|MilliSeconds|Среднее|Средняя задержка записи в миллисекундах на операцию|Отсутствуют|
|VolumeLogicalSize|Объем использованного объема тома|Байты|Среднее|Логический размер тома (используемые байты)|Отсутствуют|
|VolumeSnapshotSize|Volume snapshot size (Размер моментальных снимков в томе)|Байты|Среднее|Размер всех моментальных снимков в томе|Отсутствуют|
|ReadIops|Read iops (Число операций ввода-вывода в секунду при чтении)|Число/с|Среднее|Число операций ввода-вывода в секунду при чтении|Отсутствуют|
|WriteIops|Write iops (Количество операций ввода-вывода в секунду при записи)|Число/с|Среднее|Число операций ввода-вывода в секунду при записи|Отсутствуют|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Пул, выделенный для размера тома|Байты|Среднее|Используемый выделенный размер пула|Отсутствуют|
|VolumePoolTotalLogicalSize|Размер использованного пула|Байты|Среднее|Сумма логического размера всех томов, входящих в пул|Отсутствуют|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Байты|Итог|Число байт, отправленных сетевым интерфейсом|Отсутствуют|
|BytesReceivedRate|Получено байт|Байты|Итог|Число байт, полученных сетевым интерфейсом|Отсутствуют|
|PacketsSentRate|Отправлено пакетов|Count|Итог|Число пакетов, отправленных сетевым интерфейсом|Отсутствуют|
|PacketsReceivedRate|Получено пакетов|Count|Итог|Число пакетов, полученных сетевым интерфейсом|Отсутствуют|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Среднее|Средняя доступность пути к данным подсистемы балансировки нагрузки за период времени|Фронтендипаддресс, FrontendPort|
|DipAvailability|Health Probe Status (Состояние пробы работоспособности)|Count|Среднее|Среднее состояние пробы работоспособности подсистемы балансировки нагрузки за период времени|ProtocolType, Баккендпорт, Фронтендипаддресс, FrontendPort, Баккендипаддресс|
|ByteCount|Количество байтов|Count|Итог|Общее количество байтов, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|PacketCount|Количество пакетов|Count|Итог|Общее количество пакетов, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|SYNCount|Количество пакетов SYN|Count|Итог|Общее количество пакетов SYN, переданных за период времени|Фронтендипаддресс, FrontendPort, направление|
|SnatConnectionCount|Количество подключений SNAT|Count|Итог|Общее количество подключений SNAT, созданных за период времени|Фронтендипаддресс, Баккендипаддресс, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))|Count|Итог|Общее число портов SNAT, выделенных за период времени|Фронтендипаддресс, Баккендипаддресс, ProtocolType, Исаваитингремовал|
|UsedSnatPorts|Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))|Count|Итог|Общее количество портов SNAT, использованных за период времени|Фронтендипаддресс, Баккендипаддресс, ProtocolType, Исаваитингремовал|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Итог|Количество запросов, выполненных для зоны DNS|Отсутствуют|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Количество наборов записей в зоне DNS|Отсутствуют|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|Отсутствуют|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PacketsInDDoS|Входящие пакеты (атака DDoS)|Число/с|Максимальная|Входящие пакеты (атака DDoS)|Отсутствуют|
|PacketsDroppedDDoS|Удаленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты (атака DDoS)|Отсутствуют|
|PacketsForwardedDDoS|Перенаправленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты (атака DDoS)|Отсутствуют|
|TCPPacketsInDDoS|Входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Входящие пакеты TCP (атака DDoS)|Отсутствуют|
|TCPPacketsDroppedDDoS|Удаленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты TCP (атака DDoS)|Отсутствуют|
|TCPPacketsForwardedDDoS|Перенаправленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты TCP (атака DDoS)|Отсутствуют|
|UDPPacketsInDDoS|Входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Входящие пакеты UDP (атака DDoS)|Отсутствуют|
|UDPPacketsDroppedDDoS|Удаленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты UDP (атака DDoS)|Отсутствуют|
|UDPPacketsForwardedDDoS|Перенаправленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты UDP (атака DDoS)|Отсутствуют|
|BytesInDDoS|Входящие байты (атака DDoS)|Байт/с|Максимальная|Входящие байты (атака DDoS)|Отсутствуют|
|BytesDroppedDDoS|Удаленные входящие байты (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты (атака DDoS)|Отсутствуют|
|BytesForwardedDDoS|Перенаправленные входящие байты (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты (атака DDoS)|Отсутствуют|
|TCPBytesInDDoS|Входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Входящие байты TCP (атака DDoS)|Отсутствуют|
|TCPBytesDroppedDDoS|Удаленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты TCP (атака DDoS)|Отсутствуют|
|TCPBytesForwardedDDoS|Перенаправленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты TCP (атака DDoS)|Отсутствуют|
|UDPBytesInDDoS|Входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Входящие байты UDP (атака DDoS)|Отсутствуют|
|UDPBytesDroppedDDoS|Удаленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты UDP (атака DDoS)|Отсутствуют|
|UDPBytesForwardedDDoS|Перенаправленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты UDP (атака DDoS)|Отсутствуют|
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Count|Максимальная|Выполняется ли атака DDoS|Отсутствуют|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|Отсутствуют|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|Отсутствуют|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|Отсутствуют|
|VipAvailability|Data Path Availability (Доступность пути к данным)|Count|Среднее|Средняя доступность IP-адреса за период времени|Port|
|ByteCount|Количество байтов|Count|Итог|Общее количество байтов, переданных за период времени|Порт, направление|
|PacketCount|Количество пакетов|Count|Итог|Общее количество пакетов, переданных за период времени|Порт, направление|
|SynCount|Количество пакетов SYN|Count|Итог|Общее количество пакетов SYN, переданных за период времени|Порт, направление|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Время кругового пути для проверки связи с виртуальной машиной|MilliSeconds|Среднее|Время кругового пути для проверки связи, отправленной на целевую виртуальную машину|Саурцекустомераддресс, Дестинатионкустомераддресс|
|PingMeshProbesFailedPercent|Неудачные проверки связи с виртуальной машиной|Процент|Среднее|Процент числа неудачных проверок связи для общего числа отправленных сообщений ping для целевой виртуальной машины|Саурцекустомераддресс, Дестинатионкустомераддресс|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ApplicationRuleHit|Число попаданий в правила приложения|Count|Итог|Число попаданий в правилах приложения|Состояние, причина, протокол|
|NetworkRuleHit|Число попаданий в сетевые правила|Count|Итог|Число попаданий сетевых правил|Состояние, причина, протокол|
|FirewallHealth|Состояние работоспособности брандмауэра|Процент|Среднее|Состояние работоспособности брандмауэра|Состояние, причина|
|DataProcessed|Обработанные данные|Байты|Итог|Общий объем данных, обработанных брандмауэром|Отсутствуют|
|SNATPortUtilization|Использование портов SNAT|Процент|Среднее|Использование портов SNAT|Отсутствуют|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Среднее|Количество байтов в секунду, обрабатываемых шлюзом приложений|Отсутствуют|
|UnhealthyHostCount|Количество неработоспособных узлов|Count|Среднее|Количество неработоспособных узлов серверной части|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Count|Среднее|Количество работоспособных узлов серверной части|BackendSettingsPool|
|TotalRequests|Общее количество запросов|Count|Итог|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Количество запросов в минуту на исправный узел|Count|Среднее|Среднее число запросов в минуту за каждый работоспособный хост-сервер в пуле|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Count|Итог|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Count|Итог|Состояние ответа HTTP, возвращенное шлюзом приложений|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Count|Итог|Число текущих установленных подключений к шлюзу приложений|Отсутствуют|
|невконнектионсперсеконд|Новых подключений в секунду|Число/с|Среднее|Новых подключений в секунду, установленных с помощью шлюза приложений|Отсутствуют|
|CpuUtilization|Загрузка ЦП|Процент|Среднее|Текущая загрузка ЦП шлюзом приложений|Отсутствуют|
|CapacityUnits|Текущее число единиц емкости|Count|Среднее|Потребленные единицы мощности|Отсутствуют|
|фикседбиллаблекапаЦитюнитс|Фиксированные оплачиваемые единицы емкости|Count|Среднее|Единицы с минимальной емкостью, которые будут заряжены|Отсутствуют|
|естиматедбилледкапаЦитюнитс|Расчетные единицы пропускной способности|Count|Среднее|Предполагаемые единицы мощности, которые будут заряжены|Отсутствуют|
|ComputeUnits|Текущее число единиц вычислений|Count|Среднее|Потребленные единицы вычислений|Отсутствуют|
|BackendResponseStatus|Состояние ответа серверной части|Count|Итог|Количество кодов HTTP-ответов, создаваемых внутренними элементами. Сюда не входят коды ответов, созданные шлюзом приложений.|Баккендсервер, неработоспособных, BackendHttpSetting, Хттпстатусграуп|
|TlsProtocol|Клиентский протокол TLS|Count|Итог|Количество запросов TLS и не-TLS, инициированных клиентом, который установил подключение к шлюзу приложений. Чтобы просмотреть распределение протокола TLS, отфильтруйте его по протоколу измерения TLS.|Прослушиватель, Тлспротокол|
|BytesSent|Отправлено байт|Байты|Итог|Общее число байтов, отправленных шлюзом приложений клиентам|Прослушиватель|
|BytesReceived|Получено байт|Байты|Итог|Общее число байтов, полученных шлюзом приложений от клиентов|Прослушиватель|
|ClientRtt|Время кругового пути (RTT) клиента|MilliSeconds|Среднее|Среднее время кругового пути между клиентами и шлюзом приложений. Эта метрика показывает, сколько времени требуется для установления подключений и возврата подтверждений.|Прослушиватель|
|ApplicationGatewayTotalTime|Общее время шлюза приложений|MilliSeconds|Среднее|Среднее время, затрачиваемое на обработку запроса и отправку ответа. Это значение вычисляется как среднее по интервалу времени, когда шлюз приложений получает первый байт HTTP-запроса до момента завершения операции отправки ответа. Важно отметить, что это обычно включает время обработки шлюза приложений, время, в течение которого пакеты запросов и ответов перемещаются по сети, а также время ответа внутреннего сервера.|Прослушиватель|
|BackendConnectTime|Время соединения с серверной частью|MilliSeconds|Среднее|Время, затраченное на установление соединения с внутренним сервером|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|BackendFirstByteResponseTime|Время получения первого байта ответа от серверной части|MilliSeconds|Среднее|Интервал времени между началом установления соединения с внутренним сервером и получением первого байта заголовка ответа, приблизительного времени обработки внутреннего сервера|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|BackendLastByteResponseTime|Время получения последнего байта ответа от серверной части|MilliSeconds|Среднее|Интервал времени между началом установления соединения с внутренним сервером и получения последнего байта текста ответа|Прослушиватель, Баккендсервер, неработоспособных, BackendHttpSetting|
|MatchedCount|Общее распространение правила брандмауэра веб-приложения v1|Count|Итог|Общее распределение правил брандмауэра веб-приложения v1 для входящего трафика|Группу правил, RuleId|
|BlockedCount|Распределение правила заблокированных запросов брандмауэра веб-приложения v1|Count|Итог|Распределение правила заблокированных запросов брандмауэра веб-приложения v1|Группу правил, RuleId|
|BlockedReqCount|Число заблокированных запросов для брандмауэра веб-приложения v1|Count|Итог|Число заблокированных запросов для брандмауэра веб-приложения v1|Отсутствуют|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth (Пропускная способность шлюза S2S)|Байт/с|Среднее|Средняя пропускная способность подключения "сеть — сеть" для шлюза в байтах в секунду|Отсутствуют|
|P2SBandwidth|Gateway P2S Bandwidth (Пропускная способность шлюза P2S)|Байт/с|Среднее|Средняя пропускная способность подключения "точка — сеть" для шлюза в байтах в секунду|Отсутствуют|
|P2SConnectionCount|P2S Connection Count (Количество подключений P2S)|Count|Максимальная|Число подключений "точка — сеть" для шлюза|Протокол|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Среднее|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Итог|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Итог|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Count|Итог|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Count|Итог|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Count|Итог|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Count|Итог|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Среднее|Уровень источника RX в dBm|Связь, дорожка|
|TxLightLevel|TxLightLevel|Count|Среднее|Уровень источника TX в dBm|Связь, дорожка|
|AdminState|AdminState|Count|Среднее|Состояние администратора порта|Ссылка|
|LineProtocol|LineProtocol|Count|Среднее|Состояние протокола строки для порта|Ссылка|
|PortBitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Ссылка|
|PortBitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Ссылка|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|пирингтипе|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|пирингтипе|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|пиредЦиркуитскэй|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|пиредЦиркуитскэй|
|BgpAvailability|Доступность BGP|Процент|Среднее|Доступность BGP от MSEE к всем одноранговым узлам.|Пирингтипе, одноранговая|
|ArpAvailability|Доступность ARP|Процент|Среднее|Доступность ARP из MSEE для всех узлов.|Пирингтипе, одноранговая|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|Число/с|Среднее|Количество входящих бит данных, отбрасываемых в секунду|Отсутствуют|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|Число/с|Среднее|Число отброшенных битов данных, отбрасываемых в секунду|Отсутствуют|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Отсутствуют|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Отсутствуют|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|Отсутствуют|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|Отсутствуют|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|Число/с|Среднее|Входящий трафик Azure в секунду (в битах)|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|Число/с|Среднее|Исходящий трафик Azure в секунду (в битах)|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Count|Итог|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Count|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Процент|Среднее|Доля проб мониторинга подключения с ошибками, в процентах|Отсутствуют|
|AverageRoundtripMs|Среднее Время приема-передачи (мс)|MilliSeconds|Среднее|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|Отсутствуют|
|ChecksFailedPercent|Процент неудачных проверок (предварительная версия)|Процент|Среднее|% сбоев проверки подключения|Саурцеаддресс, SourceName, значение sourceresourceid, SourceType, Protocol, Дестинатионаддресс, имя назначения, Дестинатионресаурцеид, DestinationType, Дестинатионпорт, Тестграупнаме, TestConfigurationName|
|RoundTripTimeMs|Время кругового пути (мс) (предварительная версия)|MilliSeconds|Среднее|Время приема-передачи в миллисекундах для проверок мониторинга подключения|Саурцеаддресс, SourceName, значение sourceresourceid, SourceType, Protocol, Дестинатионаддресс, имя назначения, Дестинатионресаурцеид, DestinationType, Дестинатионпорт, Тестграупнаме, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|RequestCount|Число запросов|Count|Итог|Число клиентских запросов, обслуженных прокси-сервером HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|RequestSize|Размер запроса|Байты|Итог|Число байт, отправленных в качестве запросов от клиентов на прокси-сервер HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|ResponseSize|Размер ответа|Байты|Итог|Число байт, отправленных клиентам в качестве ответов от прокси-сервера HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|BillableResponseSize|Оплачиваемый размер ответа|Байты|Итог|Число оплачиваемых байтов (минимальное 2 КБ на запрос), отправленных в качестве ответов от прокси-сервера HTTP/S клиентам.|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|BackendRequestCount|Число запросов к серверному компоненту|Count|Итог|Число запросов, отправленных от прокси-сервера HTTP/S к серверным частям|Состоянию HTTP, Хттпстатусграуп, серверная часть|
|BackendRequestLatency|Backend Request Latency (Задержка запросов к серверным частям)|MilliSeconds|Среднее|Время от отправки запроса прокси-сервером HTTP/S к серверной части до получения прокси-сервером HTTP/S последнего байта ответа от серверной части|Серверная часть|
|TotalLatency|Total Latency (Общая задержка)|MilliSeconds|Среднее|Время от получения клиентского запроса прокси-сервером HTTP/S до подтверждения клиентом последнего байта ответа от прокси-сервера HTTP/S|Состоянию HTTP, Хттпстатусграуп, Клиентрегион, Клиенткаунтри|
|BackendHealthPercentage|Работоспособность серверного компонента (в процентах)|Процент|Среднее|Процент успешных проб работоспособности от прокси-сервера HTTP/S к серверным частям|Серверная часть, неработоспособных|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)|Count|Итог|Количество клиентских запросов, обрабатываемых брандмауэром веб-приложения|PolicyName, RuleName, действие|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/Приватеднсзонес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Итог|Число запросов, выполненных для зоны Частная зона DNS|Отсутствуют|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Число наборов записей в Частная зона DNSной зоне|Отсутствуют|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Процент использования емкости набора записей, используемой зоной Частная зона DNS|Отсутствуют|
|виртуалнетворклинккаунт|Число ссылок на виртуальную сеть|Count|Максимальная|Число виртуальных сетей, связанных с зоной Частная зона DNS|Отсутствуют|
|виртуалнетворклинккапаЦитютилизатион|Использование емкости для связи виртуальной сети|Процент|Максимальная|Процент емкости виртуальной сети, используемой зоной Частная зона DNS|Отсутствуют|
|виртуалнетворквисрегистратионлинккаунт|Число ссылок для регистрации виртуальной сети|Count|Максимальная|Число виртуальных сетей, связанных с зоной Частная зона DNS с включенной автоматической регистрацией|Отсутствуют|
|виртуалнетворквисрегистратионкапаЦитютилизатион|Использование емкости канала регистрации виртуальной сети|Процент|Максимальная|Процент связи виртуальной сети с емкостью автоматической регистрации, используемой Частная зона DNS зоной|Отсутствуют|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Итог|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Отсутствуют|
|registration.create|Операции создания регистрации|Count|Итог|Число всех успешных операций создания регистраций.|Отсутствуют|
|registration.update|Операции обновления регистрации|Count|Итог|Число всех успешных операций обновления регистраций.|Отсутствуют|
|registration.get|Операции чтения регистрации|Count|Итог|Число всех успешных запросов регистрации.|Отсутствуют|
|registration.delete|Операции удаления регистрации|Count|Итог|Число всех успешных операций удаления регистраций.|Отсутствуют|
|incoming|Входящие сообщения|Count|Итог|Число всех успешно отправленных вызовов API. |Отсутствуют|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Итог|Отменено запланированных push-уведомлений|Отсутствуют|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Count|Итог|Отменено запланированных push-уведомлений|Отсутствуют|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Итог|Запланированных уведомлений в состоянии ожидания|Отсутствуют|
|installation.all|Операции управления установкой|Count|Итог|Операции управления установкой|Отсутствуют|
|installation.get|Операции получения установки|Count|Итог|Операции получения установки|Отсутствуют|
|installation.upsert|Операции создания или обновления установки|Count|Итог|Операции создания или обновления установки|Отсутствуют|
|installation.patch|Операции исправления установки|Count|Итог|Операции исправления установки|Отсутствуют|
|installation.delete|Операции удаления установки|Count|Итог|Операции удаления установки|Отсутствуют|
|outgoing.allpns.success|Успешные уведомления|Count|Итог|Общее число успешных уведомлений.|Отсутствуют|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Итог|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Отсутствуют|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Итог|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Отсутствуют|
|outgoing.allpns.channelerror|Ошибки канала|Count|Итог|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Отсутствуют|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Итог|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Отсутствуют|
|outgoing.wns.success|Успешные уведомления WNS|Count|Итог|Общее число успешных уведомлений.|Отсутствуют|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Отсутствуют|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Отсутствуют|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Итог|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Отсутствуют|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Отсутствуют|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Итог|Windows Live недоступна.|Отсутствуют|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Итог|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Отсутствуют|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Итог|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Отсутствуют|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Итог|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Отсутствуют|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Итог|Полезные данные уведомления слишком большие (состояние WNS: 413).|Отсутствуют|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Отсутствуют|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|Отсутствуют|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Итог|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Отсутствуют|
|outgoing.wns.pnserror|Ошибки WNS|Count|Итог|Уведомление не доставлено из-за ошибок связи с WNS.|Отсутствуют|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Итог|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Отсутствуют|
|outgoing.apns.success|Успешные уведомления APNS|Count|Итог|Общее число успешных уведомлений.|Отсутствуют|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Отсутствуют|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Итог|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|Отсутствуют|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Итог|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Отсутствуют|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|Отсутствуют|
|outgoing.apns.pnserror|Ошибки APNS|Count|Итог|Количество неудачных отправлений из-за ошибок связи с APNS.|Отсутствуют|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Итог|Общее число успешных уведомлений.|Отсутствуют|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Отсутствуют|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Отсутствуют|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Итог|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Отсутствуют|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Отсутствуют|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Итог|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Отсутствуют|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Отсутствуют|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Итог|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Отсутствуют|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Итог|Количество неудачных отправлений из-за ошибок связи с GCM.|Отсутствуют|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Отсутствуют|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Итог|Общее число успешных уведомлений.|Отсутствуют|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Отсутствуют|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Итог|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Отсутствуют|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Итог|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Отсутствуют|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Итог|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Отсутствуют|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Итог|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Отсутствуют|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Итог|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Отсутствуют|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Итог|Количество неудачных отправлений из-за ошибок связи с MPNS.|Отсутствуют|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Итог|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Отсутствуют|
|notificationhub.pushes|Все исходящие уведомления|Count|Итог|Все исходящие уведомления из центра уведомлений.|Отсутствуют|
|incoming.all.requests|Все входящие запросы|Count|Итог|Общее количество входящих запросов для концентратора уведомлений|Отсутствуют|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Итог|Общее количество неудачных входящих запросов для концентратора уведомлений|Отсутствуют|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Процент свободных индексных дескрипторов|Count|Среднее|Average_% Free Inodes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Free Space|Процент свободного места|Count|Среднее|Average_% Free Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Inodes|Процент используемых индексных дескрипторов|Count|Среднее|Average_% Used Inodes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Space|Процент используемого места|Count|Среднее|Average_% Used Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Read Bytes/sec|Скорость чтения с диска (байт/с)|Count|Среднее|Average_Disk Read Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Reads/sec|Операций чтения с диска в секунду|Count|Среднее|Average_Disk Reads/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Transfers/sec|Обращений к диску в секунду|Count|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Write Bytes/sec|Скорость записи на диск (байт/с)|Count|Среднее|Average_Disk Write Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Disk Writes/sec|Операций записи на диск в секунду|Count|Среднее|Average_Disk Writes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Megabytes|Свободно мегабайт|Count|Среднее|Average_Free Megabytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Logical Disk Bytes/sec|Скорость обмена с логическим диском (байт/с)|Count|Среднее|Average_Logical Disk Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Available Memory|Процент доступной памяти|Count|Среднее|Average_% Available Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Available Swap Space|Процент доступной области подкачки|Count|Среднее|Average_% Available Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Memory|Процент используемой памяти|Count|Среднее|Average_% Used Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Used Swap Space|Процент используемой области подкачки|Count|Среднее|Average_% Used Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes Memory|Доступный объем памяти в МБ|Count|Среднее|Average_Available MBytes Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes Swap|Доступный объем подкачки в МБ|Count|Среднее|Average_Available MBytes Swap|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Page Reads/sec|Операций чтения со страницы в секунду|Count|Среднее|Average_Page Reads/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Page Writes/sec|Операций записи на страницу в секунду|Count|Среднее|Average_Page Writes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pages/sec|Страниц в секунду|Count|Среднее|Average_Pages/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used MBytes Swap Space|Используемая области подкачки в МБ|Count|Среднее|Average_Used MBytes Swap Space|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used Memory MBytes|Используемый объем памяти в МБ|Count|Среднее|Average_Used Memory MBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes Transmitted|Всего передано байт|Count|Среднее|Average_Total Bytes Transmitted|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes Received|Всего получено байт|Count|Среднее|Average_Total Bytes Received|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Bytes|Всего байт|Count|Среднее|Average_Total Bytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Packets Transmitted|Всего передано пакетов|Count|Среднее|Average_Total Packets Transmitted|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Packets Received|Всего получено пакетов|Count|Среднее|Average_Total Packets Received|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Rx Errors|Всего ошибок Rx|Count|Среднее|Average_Total Rx Errors|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Tx Errors|Всего ошибок Tx|Count|Среднее|Average_Total Tx Errors|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Total Collisions|Всего конфликтов|Count|Среднее|Average_Total Collisions|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время чтения с диска (с)|Среднее время чтения с диска (с)|Count|Среднее|Average_Avg. время чтения с диска (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время обращения к диску (с)|Среднее время обращения к диску (с)|Count|Среднее|Average_Avg. время обращения к диску (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Avg. время записи на диск (с)|Среднее время записи на диск (с)|Count|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Physical Disk Bytes/sec|Скорость обмена с физическим диском (байт/с)|Count|Среднее|Average_Physical Disk Bytes/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pct Privileged Time|Процент времени работы в привилегированном режиме|Count|Среднее|Average_Pct Privileged Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Pct User Time|Процент времени пользователя|Count|Среднее|Average_Pct User Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Used Memory kBytes|Используемая память в КБ|Count|Среднее|Average_Used Memory kBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Virtual Shared Memory|Виртуальная общая память|Count|Среднее|Average_Virtual Shared Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% DPC Time|Процент времени DPC|Count|Среднее|Average_% DPC Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Idle Time|Процент времени простоя|Count|Среднее|Average_% Idle Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Interrupt Time|Процент времени прерывания|Count|Среднее|Average_% Interrupt Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% IO Wait Time|Процент времени ожидания ввода-вывода|Count|Среднее|Average_% IO Wait Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Nice Time|Процент времени работы с низким приоритетом|Count|Среднее|Average_% Nice Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Privileged Time|Процент времени работы в привилегированном режиме|Count|Среднее|Average_% Privileged Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Processor Time|% загруженности процессора|Count|Среднее|Average_% Processor Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% User Time|Процент времени пользователя|Count|Среднее|Average_% User Time|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Physical Memory|Объем свободной физической памяти|Count|Среднее|Average_Free Physical Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Space in Paging Files|Объем свободного места в файлах подкачки|Count|Среднее|Average_Free Space in Paging Files|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Free Virtual Memory|Объем свободной виртуальной памяти|Count|Среднее|Average_Free Virtual Memory|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Processes|Процессы|Count|Среднее|Average_Processes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Size Stored In Paging Files|Объем, сохраненный в файлах подкачки|Count|Среднее|Average_Size Stored In Paging Files|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Uptime|Время доступности|Count|Среднее|Average_Uptime|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Users|Пользователи|Count|Среднее|Average_Users|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Current Disk Queue Length|Текущая длина очереди диска|Count|Среднее|Average_Current Disk Queue Length|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Available MBytes|Доступный объем в МБ|Count|Среднее|Average_Available MBytes|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_% Committed Bytes In Use|Использование выделенной памяти (в байтах), %|Count|Среднее|Average_% Committed Bytes In Use|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Received/sec|Полученных байтов/с|Count|Среднее|Average_Bytes Received/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Sent/sec|Отправленных байтов/с|Count|Среднее|Average_Bytes Sent/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Bytes Total/sec|Всего байтов/с|Count|Среднее|Average_Bytes Total/sec|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Average_Processor Queue Length|Длина очереди процессора|Count|Среднее|Average_Processor Queue Length|Computer, ObjectName, имя_экземпляра, Каунтерпас, Sourcesystem имеет значение|
|Пульс|Пульс|Count|Итог|Пульс|Computer, OSType, Version, Саурцекомпутерид|
|Update|Update|Count|Среднее|Update|Компьютер, продукт, классификация, Упдатестате, необязательный, утвержденный|
|событие|Событие|Count|Среднее|событие|Source, EventLog, Computer, Евенткатегори, Евентлевел, него eventlevelname, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. пиринг/Пирингсервицес

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|префикслатенци|Задержка префикса|Миллисекунды|Среднее|Средняя задержка префикса|префикснаме|

## <a name="microsoftpeeringpeerings"></a>Microsoft. пиринг/пиринг

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Доступность сеанса v4|Процент|Среднее|Доступность сеанса v4|ConnectionId|
|SessionAvailabilityV6|Доступность сеанса V6|Процент|Среднее|Доступность сеанса V6|ConnectionId|
|ингресстраффикрате|Скорость входящего трафика|битсперсеконд|Среднее|Скорость входящего трафика в битах в секунду|ConnectionId|
|егресстраффикрате|Скорость исходящего трафика|битсперсеконд|Среднее|Скорость исходящего трафика в битах в секунду|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунды|Среднее|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Среднее|Число заданий в очереди пула потоков запросов.|Нет измерений|
|qpu_high_utilization_metric|Высокая загрузка QPU|Count|Итог|Высокая загрузка QPU за последнюю минуту; 1 означает высокую загрузку QPU; в противном случае 0|Нет измерений|
|memory_metric|Память|Байты|Среднее|Память. Диапазон 0–3 ГБ для A1, 0–5 ГБ для A2, 0–10 ГБ для A3, 0–25 ГБ для A4, 0–50 ГБ для A5 и 0–100 ГБ для A6|Нет измерений|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|Нет измерений|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. Прожектбабилон/учетные записи

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ассетдистрибутионбиклассификатион|Распределение ресурсов по классификации|Count|Итог|Указывает число активов с определенной классификацией, т. е. они классифицируются с этой меткой.|Классификация, источник, ResourceId|
|ассетдистрибутионбисторажетипе|Распределение ресурсов по типу хранилища|Count|Итог|Указывает число активов с определенным типом хранилища.|StorageType, ResourceId|
|каталогактивеусерс|Активные Ежедневные Пользователи|Count|Итог|Ежедневное число активных пользователей|ResourceId|
|каталогусаже|Распределение использования по операциям|Count|Итог|Указывает число операций, выполняемых пользователем операции в каталоге, т. е. доступ, поиск, глоссарий.|Операция, ResourceId|
|нумберофассетсвисклассификатионс|Количество активов по крайней мере с одной классификацией|Count|Среднее|Указывает количество ресурсов по крайней мере с одной классификацией тегов.|ResourceId|
|сканканцеллед|Сканирование отменено|Count|Итог|Указывает число отмененных просмотров.|ResourceId|
|сканкомплетед|Сканирование завершено|Count|Итог|Указывает количество успешно выполненных проверок.|ResourceId|
|сканфаилед|Сбой сканирования|Count|Итог|Указывает количество ошибок сканирования.|ResourceId|
|скантиметакен|Затраченное время сканирования|Секунды|Итог|Указывает общее время сканирования в секундах.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Итог|Выполненные ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Итог|ClientError в ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Итог|ServerError в ListenerConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|Count|Итог|Выполненные SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Итог|ClientError в SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Итог|ServerError в SenderConnections для Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Итог|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Итог|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Итог|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Итог|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Итог|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Итог|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Итог|Всего SenderDisconnects для Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Среднее|Среднее время задержки поиска для службы поиска.|Отсутствуют|
|SearchQueriesPerSecond|Search queries per second|Число/с|Среднее|Число поисковых запросов в секунду для службы поиска.|Отсутствуют|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Среднее|Процент отрегулированных поисковых запросов для службы поиска.|Отсутствуют|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы|Count|Итог|Общее количество успешных запросов для пространства имен|EntityName, OperationResult|
|ServerErrors|Ошибки сервера.|Count|Итог|Ошибки на сервере для Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Ошибки пользователей.|Count|Итог|Ошибки пользователей для Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Регулируемые запросы.|Count|Итог|Регулируемые запросы для Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Входящих запросов|Count|Итог|Входящие запросы для Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Входящие сообщения|Count|Итог|Входящие сообщения для Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Исходящие сообщения|Count|Итог|Исходящие сообщения для Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Count|Итог|Число активных подключений для Microsoft.ServiceBus.|Отсутствуют|
|ConnectionsOpened|Открытые подключения.|Count|Среднее|Открытые подключения для Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Закрытые подключения.|Count|Среднее|Закрытые подключения для Microsoft.ServiceBus.|EntityName|
|Размер|Размер|Байты|Среднее|Размер очереди или раздела в байтах.|EntityName|
|Сообщения|Число сообщений в очереди или разделе.|Count|Среднее|Число сообщений в очереди или разделе.|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе.|Count|Среднее|Число активных сообщений в очереди или разделе.|EntityName|
|DeadletteredMessages|Число недоставленных сообщений в очереди или разделе.|Count|Среднее|Число недоставленных сообщений в очереди или разделе.|EntityName|
|ScheduledMessages|Число запланированных сообщений в очереди или разделе.|Count|Среднее|Число запланированных сообщений в очереди или разделе.|EntityName|
|NamespaceCpuUsage|ЦП|Процент|Максимальная|Метрика использования ЦП для пространства имен Premium служебной шины.|Реплика|
|NamespaceMemoryUsage|Использование памяти|Процент|Максимальная|Метрика использования памяти пространства имен Premium служебной шины.|Реплика|
|CPUXNS|ЦП (не рекомендуется)|Процент|Максимальная|Метрика использования ЦП для пространства имен Premium служебной шины. Эта метрика — поддерживается. Используйте вместо этого метрику ЦП (Намеспацекпуусаже).|Реплика|
|WSXNS|Использование памяти (не рекомендуется)|Процент|Максимальная|Метрика использования памяти пространства имен Premium служебной шины. Использовать эту метрику не рекомендуется. Используйте вместо этого метрику "использование памяти" (Намеспацемеморюсаже).|Реплика|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Среднее|ЦП, выделенный этому контейнеру в миллисекундах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|AllocatedMemory|AllocatedMemory|Байты|Среднее|Память, выделенная для этого контейнера, в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|ActualCpu|ActualCpu|Count|Среднее|Фактическое использование ЦП в миллисекундах|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|ActualMemory|ActualMemory|Байты|Среднее|Фактическое использование памяти в МБ|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|CpuUtilization|CpuUtilization|Процент|Среднее|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|MemoryUtilization|MemoryUtilization|Процент|Среднее|Использование ЦП для этого контейнера в процентах от Аллокатедкпу|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме|
|ApplicationStatus|ApplicationStatus|Count|Среднее|Состояние Service Fabric приложения сетки|ApplicationName, состояние|
|ServiceStatus|ServiceStatus|Count|Среднее|Состояние работоспособности службы в Service Fabric приложении для сетки|ApplicationName, состояние, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Среднее|Состояние работоспособности реплики службы в Service Fabric приложении сети|ApplicationName, состояние, ServiceName, Сервицерепликанаме|
|ContainerStatus|ContainerStatus|Count|Среднее|Состояние контейнера в Service Fabric приложении сетки|ApplicationName, ServiceName, Кодепаккаженаме, Сервицерепликанаме, состояние|
|RestartCount|RestartCount|Count|Среднее|Число перезапусков контейнера в Service Fabric приложении сетки|ApplicationName, Status, ServiceName, Сервицерепликанаме, Кодепаккаженаме|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ConnectionCount|Число подключений|Count|Максимальная|Количество пользовательских подключений.|Конечная точка|
|MessageCount|Количество сообщений|Count|Итог|Общее количество сообщений.|Отсутствуют|
|InboundTraffic|Inbound Traffic (Входящий трафик)|Байты|Итог|Входящий трафик службы|Отсутствуют|
|OutboundTraffic|Outbound Traffic (Исходящий трафик)|Байты|Итог|Исходящий трафик службы|Отсутствуют|
|UserErrors|Ошибки пользователей|Процент|Максимальная|Процент ошибок пользователей|Отсутствуют|
|SystemErrors|Системные ошибки|Процент|Максимальная|Процент системных ошибок|Отсутствуют|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Отсутствуют|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|Отсутствуют|
|log_write_percent|Log IO percentage|Процент|Среднее|Процент операций ввода-вывода журнала. Неприменимо к хранилищам данных.|Отсутствуют|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент DTU. Применяется к базам данных на основе DTU.|Отсутствуют|
|носителей.|Используемое пространство данных|Байты|Максимальная|Используемое пространство данных. Неприменимо к хранилищам данных.|Отсутствуют|
|connection_successful|Успешные подключения|Count|Итог|Успешные подключения|Отсутствуют|
|connection_failed|Неудачные подключения|Count|Итог|Неудачные подключения|Отсутствуют|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Итог|Заблокировано брандмауэром|Отсутствуют|
|взаимоблокировка|Взаимоблокировки|Count|Итог|Взаимоблокировок. Неприменимо к хранилищам данных.|Отсутствуют|
|storage_percent|Процент использования пространства данных|Процент|Максимальная|Процент использования пространства данных. Неприменимо к хранилищам данных или базам данных с масштабированием.|Отсутствуют|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранения выполняющейся в памяти OLTP. Неприменимо к хранилищам данных.|Отсутствуют|
|workers_percent|Workers percentage|Процент|Среднее|Процент рабочих ролей. Неприменимо к хранилищам данных.|Отсутствуют|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент сеансов. Неприменимо к хранилищам данных.|Отсутствуют|
|dtu_limit|Лимит DTU|Count|Среднее|Предел DTU. Применяется к базам данных на основе DTU.|Отсутствуют|
|dtu_used|DTU used|Count|Среднее|Использованные DTU. Применяется к базам данных на основе DTU.|Отсутствуют|
|cpu_limit|Ограничение загрузки ЦП|Count|Среднее|Ограничение ЦП. Применяется к базам данных на основе виртуальное ядро.|Отсутствуют|
|cpu_used|Загрузка ЦП|Count|Среднее|ЦП используется. Применяется к базам данных на основе виртуальное ядро.|Отсутствуют|
|dwu_limit|Лимит DWU|Count|Максимальная|Ограничение DWU. Применяется только к хранилищам данных.|Отсутствуют|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU в процентах. Применяется только к хранилищам данных.|Отсутствуют|
|dwu_used|DWU used|Count|Максимальная|Используется DWU. Применяется только к хранилищам данных.|Отсутствуют|
|cache_hit_percent|Cache hit percentage (Процент попаданий в кэш)|Процент|Максимальная|Процент попаданий в кэш. Применяется только к хранилищам данных.|Отсутствуют|
|cache_used_percent|Cache used percentage (Процент использования кэша)|Процент|Максимальная|Процент использованного кэша. Применяется только к хранилищам данных.|Отсутствуют|
|sqlserver_process_core_percent<sup>1</sup> |Процент ядра процесса SQL Server|Процент|Максимальная|Процент использования ЦП для SQL Server процесса, измеряемый операционной системой.|Отсутствуют|
|sqlserver_process_memory_percent<sup>1</sup> |Процент памяти процесса SQL Server|Процент|Максимальная|Процент использования памяти для процесса SQL Server, измеряемый операционной системой.|Отсутствуют|
|tempdb_data_size<sup>2</sup> |Размер файла данных tempdb в КБ|Count|Максимальная|Размер файла данных tempdb в килобайтах.|Отсутствуют|
|tempdb_log_size<sup>2</sup> |Размер файла журнала tempdb в КБ|Count|Максимальная|Размер файла журнала tempdb в килобайтах.|Отсутствуют|
|tempdb_log_used_percent<sup>2</sup> |Процент использования журнала tempdb|Процент|Максимальная|Используемый журнал в процентах tempdb.|Отсутствуют|
|local_tempdb_usage_percent|Local tempdb percentage (Процент использования локальной базы данных tempdb)|Процент|Среднее|Процент локальной базы данных tempdb. Применяется только к хранилищам данных.|Отсутствуют|
|app_cpu_billed|Выставлен счет за ЦП для приложения|Count|Итог|Выплата за ЦП приложения. Применяется к бессерверным базам данных.|Отсутствуют|
|app_cpu_percent|Процент загрузки ЦП приложением|Процент|Среднее|Процент загрузки ЦП приложения. Применяется к бессерверным базам данных.|Отсутствуют|
|app_memory_percent|Процент памяти приложения|Процент|Среднее|Процент памяти приложения. Применяется к бессерверным базам данных.|Отсутствуют|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное хранилище данных. Неприменимо к хранилищам данных.|Отсутствуют|
|memory_usage_percent|Процент использования памяти|Процент|Максимальная|Процент памяти. Применяется только к хранилищам данных.|Отсутствуют|
|dw_backup_size_gb|Размер хранилища данных|Count|Итог|Размер хранилища данных состоит из размера данных и журнала транзакций. Метрика учитывается в части "хранилище" счета. Применяется только к хранилищам данных.|Отсутствуют|
|dw_snapshot_size_gb|Размер хранилища моментальных снимков|Count|Итог|Размер хранилища моментальных снимков — это размер добавочных изменений, захваченных моментальными снимками для создания пользовательских и автоматических точек восстановления. Метрика учитывается в части "хранилище" счета. Применяется только к хранилищам данных.|Отсутствуют|
|dw_geosnapshot_size_gb|Размер хранилища аварийного восстановления|Count|Итог|Размер хранилища аварийного восстановления отражается в счете как "хранилище аварийного восстановления". Применяется только к хранилищам данных.|Отсутствуют|
|wlg_allocation_relative_to_system_percent|Распределение группы рабочей нагрузки по процентам системы|Процент|Максимальная|Выделенный процент ресурсов относительно всей системы на каждую группу рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Распределение группы рабочей нагрузки по проценту ресурсов закрепления|Процент|Максимальная|Выделенный процент ресурсов относительно указанных ресурсов Cap на одну группу рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|wlg_active_queries|Активные запросы группы рабочей нагрузки|Count|Итог|Активные запросы в группе рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|wlg_queued_queries|Запросы в очереди группы рабочей нагрузки|Count|Итог|Запросы в очереди в группе рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|active_queries|Активные запросы|Count|Итог|Активные запросы ко всем группам рабочей нагрузки. Применяется только к хранилищам данных.|Отсутствуют|
|queued_queries|Запросы в очереди|Count|Итог|Запросы в очереди по всем группам рабочей нагрузки. Применяется только к хранилищам данных.|Отсутствуют|
|wlg_active_queries_timeouts|Время ожидания запросов группы рабочей нагрузки|Count|Итог|Запросы, для которых истекло время ожидания для группы рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|wlg_effective_min_resource_percent|Действующий минимальный процент ресурсов|Процент|Максимальная|Минимальный процент ресурсов, зарезервированных и изолированных для группы рабочей нагрузки, принимая во внимание минимальный уровень обслуживания. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|wlg_effective_cap_resource_percent|Действующее ограничение процента ресурсов|Процент|Максимальная|Жесткое ограничение в процентах ресурсов, допустимых для группы рабочей нагрузки, с учетом эффективных процентных долей ресурсов, выделенных для других групп рабочей нагрузки. Применяется только к хранилищам данных.|Ворклоадграупнаме, Исусердефинед|
|full_backup_size_bytes|Полный размер хранилища резервных копий|Байты|Максимальная|Совокупный размер хранилища полного резервного копирования. Применяется к базам данных на основе виртуальное ядро. Неприменимо к базам данных с масштабированием.|Отсутствуют|
|diff_backup_size_bytes|Разностный размер хранилища резервных копий|Байты|Максимальная|Совокупный разностный размер хранилища резервных копий. Применяется к базам данных на основе виртуальное ядро. Неприменимо к базам данных с масштабированием.|Отсутствуют|
|log_backup_size_bytes|Размер хранилища резервных копий журналов|Байты|Максимальная|Совокупный размер хранилища резервных копий журналов. Применяется к базам данных на основе виртуальное ядро и Scale.|Отсутствуют|
|snapshot_backup_size_bytes|Размер хранилища резервных копий моментальных снимков|Байты|Максимальная|Размер хранилища совокупного моментального снимка. Применяется к базам данных с масштабированием.|Отсутствуют|
|base_blob_size_bytes|Базовый размер хранилища BLOB-объектов|Байты|Максимальная|Базовый размер хранилища BLOB-объектов. Применяется к базам данных с масштабированием.|Отсутствуют|

<sup>1</sup> эта метрика доступна для баз данных, использующих модель приобретения Виртуальное ядро с 2 виртуальных ядер и выше, или 200 DTU и выше для моделей приобретения на основе DTU. 

<sup>2</sup> эта метрика доступна для баз данных, использующих модель приобретения Виртуальное ядро с 2 виртуальных ядер и выше, или 200 DTU и выше для моделей приобретения на основе DTU. Эта метрика в настоящее время недоступна для масштабируемых баз данных или хранилищ данных.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Отсутствуют|
|database_cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|DatabaseResourceId|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|Отсутствуют|
|database_physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Среднее|Процент операций ввода/вывода данных|DatabaseResourceId|
|log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|Отсутствуют|
|database_log_write_percent|Log IO percentage|Процент|Среднее|Log IO percentage|DatabaseResourceId|
|dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент DTU. Применяется к эластичным пулам на основе DTU.|Отсутствуют|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Среднее|Процент использования DTU|DatabaseResourceId|
|storage_percent|Процент использования пространства данных|Процент|Среднее|Процент использования пространства данных|Отсутствуют|
|workers_percent|Workers percentage|Процент|Среднее|Workers percentage|Отсутствуют|
|database_workers_percent|Workers percentage|Процент|Среднее|Workers percentage|DatabaseResourceId|
|sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|Отсутствуют|
|database_sessions_percent|Процент использования сеансов|Процент|Среднее|Процент использования сеансов|DatabaseResourceId|
|eDTU_limit|eDTU limit|Count|Среднее|предел eDTU. Применяется к эластичным пулам на основе DTU.|Отсутствуют|
|storage_limit|Максимальный размер данных|Байты|Среднее|Максимальный размер данных|Отсутствуют|
|eDTU_used|eDTU used|Count|Среднее|используемые eDTU. Применяется к эластичным пулам на основе DTU.|Отсутствуют|
|database_eDTU_used|eDTU used|Count|Среднее|eDTU used|DatabaseResourceId|
|storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|Отсутствуют|
|database_storage_used|Используемое пространство данных|Байты|Среднее|Используемое пространство данных|DatabaseResourceId|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранилища выполняющейся в памяти OLTP|Отсутствуют|
|cpu_limit|Ограничение загрузки ЦП|Count|Среднее|Ограничение ЦП. Применяется к эластичным пулам на основе виртуальное ядро.|Отсутствуют|
|database_cpu_limit|Ограничение загрузки ЦП|Count|Среднее|Ограничение загрузки ЦП|DatabaseResourceId|
|cpu_used|Загрузка ЦП|Count|Среднее|ЦП используется. Применяется к эластичным пулам на основе виртуальное ядро.|Отсутствуют|
|database_cpu_used|Загрузка ЦП|Count|Среднее|Загрузка ЦП|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Процент ядра процесса SQL Server|Процент|Максимальная|Процент использования ЦП для SQL Server процесса, измеряемый операционной системой. Применяется к эластичным пулам. |Отсутствуют|
|sqlserver_process_memory_percent<sup>1</sup>|Процент памяти процесса SQL Server|Процент|Максимальная|Процент использования памяти для процесса SQL Server, измеряемый операционной системой. Применяется к эластичным пулам. |Отсутствуют|
|tempdb_data_size<sup>2</sup>|Размер файла данных tempdb в КБ|Count|Максимальная|Размер файла данных tempdb в килобайтах.|Отсутствуют|
|tempdb_log_size<sup>2</sup>|Размер файла журнала tempdb в КБ|Count|Максимальная|Размер файла журнала tempdb в килобайтах. |Отсутствуют|
|tempdb_log_used_percent<sup>2</sup>|Процент использования журнала tempdb|Процент|Максимальная|Используемый журнал в процентах tempdb.|Отсутствуют|
|allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|Отсутствуют|
|database_allocated_data_storage|Выделенное пространство данных|Байты|Среднее|Выделенное пространство данных|DatabaseResourceId|
|allocated_data_storage_percent|Выделенное пространство данных в процентах|Процент|Максимальная|Выделенное пространство данных в процентах|Отсутствуют|

<sup>1</sup> эта метрика доступна для баз данных, использующих модель приобретения Виртуальное ядро с 2 виртуальных ядер и выше, или 200 DTU и выше для моделей приобретения на основе DTU. 

<sup>2</sup> эта метрика доступна для баз данных, использующих модель приобретения Виртуальное ядро с 2 виртуальных ядер и выше, или 200 DTU и выше для моделей приобретения на основе DTU. Эта метрика в настоящее время недоступна для баз данных с масштабированием.

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|virtual_core_count|Virtual core count (Число виртуальных ядер)|Count|Среднее|Virtual core count (Число виртуальных ядер)|Отсутствуют|
|avg_cpu_percent|Average CPU percentage (Средний процент использования ЦП)|Процент|Среднее|Average CPU percentage (Средний процент использования ЦП)|Отсутствуют|
|reserved_storage_mb|Зарезервированное дисковое пространство|Count|Среднее|Зарезервированное дисковое пространство|Отсутствуют|
|storage_space_used_mb|Использованное дисковое пространство|Count|Среднее|Использованное дисковое пространство|Отсутствуют|
|io_requests|IO requests count (Количество запросов ввода-вывода)|Count|Среднее|IO requests count (Количество запросов ввода-вывода)|Отсутствуют|
|io_bytes_read|Количество считанных байт ввода-вывода|Байты|Среднее|Количество считанных байт ввода-вывода|Отсутствуют|
|io_bytes_written|Количество записанных байт ввода-вывода|Байты|Среднее|Количество записанных байт ввода-вывода|Отсутствуют|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Среднее|Используемая емкость учетной записи|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Среднее|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType, уровень|
|BlobCount|Количество больших двоичных объектов|Count|Среднее|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType, уровень|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Отсутствуют|
|IndexCapacity|Емкость индекса|Байты|Среднее|Объем хранилища, используемый ADLS 2-го поколения (иерархический) индекс в байтах.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Среднее|Объем хранилища, используемый Хранилищем таблиц учетной записи хранения, в байтах.|Отсутствуют|
|TableCount|Количество таблиц|Count|Среднее|Количество таблиц в Хранилище таблиц учетной записи хранения.|Отсутствуют|
|TableEntityCount|Количество сущностей таблиц|Count|Среднее|Количество сущностей таблиц в Хранилище таблиц учетной записи хранения.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения в байтах.|FileShare|
|FileCount|Количество файлов|Count|Среднее|Число файлов в файловой службе учетной записи хранения.|FileShare|
|FileShareCount|Количество общих файловых ресурсов|Count|Среднее|Число общих файловых ресурсов в файловой службе учетной записи хранения.|Отсутствуют|
|FileShareSnapshotCount|Число моментальных снимков файлового ресурса|Count|Среднее|Количество моментальных снимков, имеющихся в службе файлов учетной записи хранения в общей папке.|FileShare|
|FileShareSnapshotSize|Размер моментального снимка файлового ресурса|Байты|Среднее|Объем хранилища, используемый моментальными снимками в файловой службе учетной записи хранения в байтах.|FileShare|
|FileShareQuota|Размер квоты файлового ресурса|Байты|Среднее|Верхний предел объема хранилища, который может использоваться службой файлов Azure в байтах.|FileShare|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности, Общая папка|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности, Общая папка|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности, Общая папка|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Отсутствуют|
|QueueCount|Количество очередей|Count|Среднее|Количество очередей в службе очередей учетной записи хранения.|Отсутствуют|
|QueueMessageCount|Количество сообщений очереди|Count|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Отсутствуют|
|Transactions|Transactions|Count|Итог|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, геотип, ApiName, проверка подлинности|
|Входящий трафик|Входящий трафик|Байты|Итог|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|Геотипизированный, ApiName, проверка подлинности|
|Исходящие|Исходящие|Байты|Итог|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|Геотипизированный, ApiName, проверка подлинности|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|Геотипизированный, ApiName, проверка подлинности|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|Геотипизированный, ApiName, проверка подлинности|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|Геотипизированный, ApiName, проверка подлинности|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ClientIOPS|Всего операций ввода-вывода в секунду для клиента|Count|Среднее|Частота операций с клиентскими файлами, обрабатываемых кэшем.|Отсутствуют|
|ClientLatency|Средняя задержка клиента|Миллисекунды|Среднее|Средняя задержка операций файлового файла клиента в кэш хранилища.|Отсутствуют|
|ClientReadIOPS|Операций ввода-вывода в секунду при чтении для клиента|Число/с|Среднее|Количество операций чтения клиента в секунду.|Отсутствуют|
|ClientReadThroughput|Средняя пропускная способность чтения кэша|Байт/с|Среднее|Скорость передачи данных при чтении клиента.|Отсутствуют|
|ClientWriteIOPS|Операций ввода-вывода в секунду при записи для клиента|Число/с|Среднее|Количество операций записи клиента в секунду.|Отсутствуют|
|ClientWriteThroughput|Средняя пропускная способность записи кэша|Байт/с|Среднее|Скорость передачи данных для клиента.|Отсутствуют|
|ClientMetadataReadIOPS|Операций ввода-вывода в секунду для чтения метаданных клиента|Число/с|Среднее|Частота операций с клиентскими файлами, отправленных в кэш, за исключением операций чтения данных, которые не изменяют постоянное состояние.|Отсутствуют|
|ClientMetadataWriteIOPS|Операций ввода-вывода в секунду для записи метаданных клиента|Число/с|Среднее|Частота операций с клиентскими файлами, отправляемых в кэш, за исключением операций записи данных, которые изменяют постоянное состояние.|Отсутствуют|
|ClientLockIOPS|Операций ввода-вывода в секунду для блокировок клиента|Число/с|Среднее|Количество операций блокировки файлов клиента в секунду.|Отсутствуют|
|StorageTargetHealth|Работоспособность целевого расположения хранилища|Count|Среднее|Логические результаты проверки подключения между целевыми объектами хранилища и кэша.|Отсутствуют|
|Время доступности|Время доступности|Count|Среднее|Логические результаты проверки подключения между системой кэша и мониторинга.|Отсутствуют|
|StorageTargetIOPS|Всего операций ввода-вывода в секунду для StorageTarget|Count|Среднее|Частота операций с файлами, которые кэш отправляет определенному Сторажетаржету.|сторажетаржет|
|StorageTargetWriteIOPS|Операций ввода-вывода в секунду при записи StorageTarget|Count|Среднее|Частота операций записи в файл, отправляемых кэшем в определенный Сторажетаржет.|сторажетаржет|
|StorageTargetAsyncWriteThroughput|Пропускная способность асинхронной записи StorageTarget|Байт/с|Среднее|Скорость, с которой кэш асинхронно записывает данные в определенный Сторажетаржет. Это уступающей операции записи, которые не приводят к блокировке клиентов.|сторажетаржет|
|StorageTargetSyncWriteThroughput|Пропускная способность синхронной записи StorageTarget|Байт/с|Среднее|Скорость, с которой кэш синхронно записывает данные в определенный Сторажетаржет. Это операции записи, которые приводят к блокировке клиентов.|сторажетаржет|
|StorageTargetTotalWriteThroughput|Общая пропускная способность записи StorageTarget|Байт/с|Среднее|Общая частота, с которой кэш записывает данные в определенный Сторажетаржет.|сторажетаржет|
|StorageTargetLatency|Задержка StorageTarget|Миллисекунды|Среднее|Средняя задержка кругового пути для всех операций с файлами, которые кэш отправляет в партрикулар Сторажетаржет.|сторажетаржет|
|StorageTargetMetadataReadIOPS|Операций ввода-вывода в секунду при чтении метаданных StorageTarget|Число/с|Среднее|Частота операций с файлами, которые не изменяют постоянное состояние и не выполняют операцию чтения, которые кэш отправляет определенному Сторажетаржет.|сторажетаржет|
|StorageTargetMetadataWriteIOPS|Операций ввода-вывода в секунду при записи метаданных StorageTarget|Число/с|Среднее|Частота операций с файлами, которые выполняют изменение постоянного состояния и исключают операцию записи, которые кэшируются в определенный Сторажетаржет.|сторажетаржет|
|StorageTargetReadIOPS|Операций ввода-вывода в секунду при чтении StorageTarget|Число/с|Среднее|Частота операций чтения файлов, которые кэш отправляет определенному Сторажетаржету.|сторажетаржет|
|StorageTargetReadAheadThroughput|Пропускная способность упреждающего чтения StorageTarget|Байт/с|Среднее|Скорость, с которой оппортунистикли кэш считывает данные из Сторажетаржет.|сторажетаржет|
|StorageTargetFillThroughput|Пропускная способность заполнения StorageTarget|Байт/с|Среднее|Скорость, с которой кэш считывает данные из Сторажетаржет для управления промахом кэша.|сторажетаржет|
|StorageTargetTotalReadThroughput|Общая пропускная способность чтения StorageTarget|Байт/с|Среднее|Общая скорость, с которой кэш считывает данные из определенного Сторажетаржет.|сторажетаржет|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Результат сеанса синхронизации|Count|Среднее|Метрика, которая регистрирует значение 1 каждый раз, когда конечная точка сервера успешно завершает сеанс синхронизации с конечной точкой облака.|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|StorageSyncSyncSessionAppliedFilesCount|Синхронизировано файлов|Count|Итог|Число синхронизированных файлов|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|StorageSyncSyncSessionPerItemErrorsCount|Несинхронизирующиеся файлы|Count|Итог|Число файлов, которые не удалось синхронизировать|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|StorageSyncBatchTransferredFileBytes|Синхронизировано байт|Байты|Итог|Общий размер файлов, переданных для сеансов синхронизации|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|StorageSyncServerHeartbeat|Состояние сервера в сети|Count|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер ресигтеред успешно записывает пульс в облачную конечную точку.|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итог|Общий размер данных, отозванных сервером|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Размер отзыва уровней в облаке|Байты|Итог|размер отозванных данных;|Синкграупнаме, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Пропускная способность отзыва уровней в облаке|Байт/с|Среднее|Размер пропускной способности при отзыве данных|Синкграупнаме, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Размер отзыва уровней в облаке по приложениям|Байты|Итог|Размер данных, отозванных приложением|Синкграупнаме, ServerName, имяПриложения|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Синхронизировано файлов|Count|Итог|Число синхронизированных файлов|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|SyncGroupSyncSessionPerItemErrorsCount|Несинхронизирующиеся файлы|Count|Итог|Число файлов, которые не удалось синхронизировать|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|
|SyncGroupBatchTransferredFileBytes|Синхронизировано байт|Байты|Итог|Общий размер файлов, переданных для сеансов синхронизации|Синкграупнаме, Серверендпоинтнаме, Синкдиректион|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Синхронизировано файлов|Count|Итог|Число синхронизированных файлов|Серверендпоинтнаме, Синкдиректион|
|ServerEndpointSyncSessionPerItemErrorsCount|Несинхронизирующиеся файлы|Count|Итог|Число файлов, которые не удалось синхронизировать|Серверендпоинтнаме, Синкдиректион|
|ServerEndpointBatchTransferredFileBytes|Синхронизировано байт|Байты|Итог|Общий размер файлов, переданных для сеансов синхронизации|Серверендпоинтнаме, Синкдиректион|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ServerHeartbeat|Состояние сервера в сети|Count|Максимальная|Метрика, которая регистрирует значение 1 каждый раз, когда сервер ресигтеред успешно записывает пульс в облачную конечную точку.|Серверресаурцеид, ServerName|
|ServerRecallIOTotalSizeBytes|Отзыв уровней в облаке|Байты|Итог|Общий размер данных, отозванных сервером|Серверресаурцеид, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|LogicalName, PartitionId|
|InputEvents|Входные события|Count|Итог|Входные события|LogicalName, PartitionId|
|InputEventBytes|Байты входного события|Байты|Итог|Байты входного события|LogicalName, PartitionId|
|LateInputEvents|События позднего ввода|Count|Итог|События позднего ввода|LogicalName, PartitionId|
|OutputEvents|Выходные события|Count|Итог|Выходные события|LogicalName, PartitionId|
|ConversionErrors|Ошибки преобразования данных|Count|Итог|Ошибки преобразования данных|LogicalName, PartitionId|
|ошибки|Ошибки среды выполнения|Count|Итог|Ошибки среды выполнения|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Неупорядоченные события|Count|Итог|Неупорядоченные события|LogicalName, PartitionId|
|AMLCalloutRequests|Запросы функций|Count|Итог|Запросы функций|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Итог|Неудачные запросы функций|LogicalName, PartitionId|
|AMLCalloutInputEvents|События функций|Count|Итог|События функций|LogicalName, PartitionId|
|DeserializationError|Ошибки десериализации входа|Count|Итог|Ошибки десериализации входа|LogicalName, PartitionId|
|EarlyInputEvents|Ранние входные события|Count|Итог|Ранние входные события|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Предельная задержка|Секунды|Максимальная|Предельная задержка|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Необработанные входные события|Count|Максимальная|Необработанные входные события|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Полученные входные источники|Count|Итог|Полученные входные источники|LogicalName, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|орчестратионпипелинерунсендед|Выполнение конвейера завершено|Count|Итог|Число успешно выполненных, неудачных или отмененных конвейеров оркестрации|Result, FailureType, конвейер|
|орчестратионактивитирунсендед|Выполнение действий завершено|Count|Итог|Число успешно выполненных, невыполненных или отмененных действий оркестрации|Result, FailureType, действие, ActivityType, конвейер|
|орчестратионтригжерсендед|Триггеры завершены|Count|Итог|Число успешно выполненных, неудачных или отмененных триггеров оркестрации|Result, FailureType, триггер|
|склондемандлогинаттемптс|Попытки входа|Count|Итог|Число попыток входа, которые выполнено или завершились сбоем|Результат|
|склондемандкуериесендед|Завершенные запросы|Count|Итог|Число успешно выполненных, невыполненных или отмененных запросов|Результат|
|склондемандкуерипроцесседбитес|Обработанные данные|Байты|Итог|Объем данных, обработанных запросами|Отсутствуют|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. синапсе/workspaces/Бигдатапулс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|спаркжобсендед|Завершенные приложения|Count|Итог|Число завершенных приложений|JobType, JobResult|
|корескапаЦити|Емкость ядер|Count|Максимальная|Емкость ядер|Отсутствуют|
|меморикапаЦитигб|Емкость памяти (ГБ)|Count|Максимальная|Емкость памяти (ГБ)|Отсутствуют|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. синапсе/workspaces/Склпулс

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|двулимит|Лимит DWU|Count|Максимальная|Цель уровня обслуживания для пула SQL|Отсутствуют|
|двуусед|DWU used|Count|Максимальная|Представляет высокоуровневое представление использования в пуле SQL. Измеряется ограничением DWU * процент DWU|Отсутствуют|
|двууседперцент|Процент использованных DWU|Процент|Максимальная|Представляет высокоуровневое представление использования в пуле SQL. Измеряется путем получения максимального значения в процентах между ЦП и операциями ввода-вывода данных|Отсутствуют|
|коннектионсблоккедбифиревалл|Подключения, заблокированные брандмауэром|Count|Итог|Число подключений, заблокированных правилами брандмауэра. Повторное посещение политик управления доступом для пула SQL и отслеживание этих подключений при высоком числе|Отсутствуют|
|адаптивекачехитперцент|Процент попаданий адаптивного кэша|Процент|Максимальная|Измеряет, насколько хорошо работают рабочие нагрузки с адаптивным кэшем. Используйте эту метрику с метрикой Процент попадания в кэш, чтобы определить, следует ли масштабировать дополнительные ресурсы или перезапускать рабочие нагрузки для расконсервации кэша.|Отсутствуют|
|адаптивекачеуседперцент|Используемый адаптивный кэш в процентах|Процент|Максимальная|Измеряет, насколько хорошо работают рабочие нагрузки с адаптивным кэшем. Используйте эту метрику в процентах от метрики для определения необходимости масштабирования для дополнительной емкости или повторного запуска рабочих нагрузок для расконсервации кэша.|Отсутствуют|
|локалтемпдбуседперцент|Процент использования локальной базы данных tempdb|Процент|Максимальная|Использование локальной базы данных tempdb для всех вычислений-узлов — значения создаются каждые пять минут.|Отсутствуют|
|меморюседперцент|Процент используемой памяти|Процент|Максимальная|Использование памяти на всех узлах в пуле SQL|Отсутствуют|
|Соединения|Соединения|Count|Итог|Общее число входов в пул SQL|Результат|
|влгактивекуериес|Активные запросы группы рабочей нагрузки|Count|Итог|Активные запросы в группе рабочей нагрузки. Использование этой метрики без фильтрации и без разделения отображает все активные запросы, выполняющиеся в системе|Исусердефинед, WorkloadGroup|
|влгактивекуериестимеаутс|Время ожидания запросов группы рабочей нагрузки|Count|Итог|Запросы для группы рабочей нагрузки, время ожидания которых истекло. Время ожидания запросов, сообщаемое этой метрикой, происходит только после начала выполнения запроса (он не включает время ожидания в связи с блокировкой или ожиданиями ресурсов).|Исусердефинед, WorkloadGroup|
|влгаллокатионбисистемперцент|Распределение группы рабочей нагрузки по процентам системы|Процент|Максимальная|Процент выделения ресурсов относительно всей системы|Исусердефинед, WorkloadGroup|
|влгаллокатионбимаксресаурцеперцент|Распределение группы рабочей нагрузки по максимальному проценту ресурсов|Процент|Максимальная|Отображает процент выделения ресурсов относительно эффективного процента ресурсов на группу рабочей нагрузки. Эта метрика обеспечивает эффективное использование группы рабочей нагрузки.|Исусердефинед, WorkloadGroup|
|влжеффективекапресаурцеперцент|Действующее ограничение процента ресурсов|Процент|Максимальная|Процент эффективных ресурсов ограничения для группы рабочей нагрузки. Если имеются другие группы рабочей нагрузки с min_percentage_resource > 0, effective_cap_percentage_resource меньше пропорционально|Исусердефинед, WorkloadGroup|
|wlg_effective_min_resource_percent|Действующий минимальный процент ресурсов|Процент|Минимальные требования|Допустимый параметр в процентах для минимального ресурса min, учитывая уровень обслуживания и параметры группы рабочей нагрузки. Эффективный min_percentage_resource можно изменить выше на более низких уровнях обслуживания.|Исусердефинед, WorkloadGroup|
|влгкуеуедкуериес|Запросы в очереди группы рабочей нагрузки|Count|Итог|Совокупное количество запросов, поставленных в очередь после достижения максимального предела параллелизма|Исусердефинед, WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итог|Количество сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Отсутствуют|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итог|Количество недопустимых сообщений, полученных от всех источников событий (концентраторов событий Azure и Центров Интернета вещей Azure)|Отсутствуют|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итог|Количество байтов, полученных от всех источников событий|Отсутствуют|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итог|Общий размер событий, успешно обработанных и доступных для запросов|Отсутствуют|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итог|Количество сведенных событий, успешно обработанных и доступных для запросов|Отсутствуют|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Отсутствуют|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Среднее|Разница между порядковым номером последней поставленной в очередь сообщения в секции источника событий и порядковым номером сообщений, обрабатываемых в входящих данных|Отсутствуют|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Count|Максимальная|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|Отсутствуют|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Count|Максимальная|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|Отсутствуют|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|IngressReceivedMessages|Получено сообщений входящих данных|Count|Итог|Количество сообщений, считанных из источника событий|Отсутствуют|
|IngressReceivedInvalidMessages|Получено недопустимых сообщений во входящих данных|Count|Итог|Количество недопустимых сообщений, считанных из источника событий|Отсутствуют|
|IngressReceivedBytes|Получено байт входящих данных|Байты|Итог|Количество байтов, считанных из источника событий|Отсутствуют|
|IngressStoredBytes|Хранится байтов входящих данных|Байты|Итог|Общий размер событий, успешно обработанных и доступных для запросов|Отсутствуют|
|IngressStoredEvents|Хранится событий входящих данных|Count|Итог|Количество сведенных событий, успешно обработанных и доступных для запросов|Отсутствуют|
|IngressReceivedMessagesTimeLag|Интервал задержки для полученных сообщений входящих данных|Секунды|Максимальная|Разница между временем, когда сообщение помещается в очередь источника событий, и временем, когда сообщение обрабатывается во входящих сообщениях|Отсутствуют|
|IngressReceivedMessagesCountLag|Интервал между номерами полученных сообщений во входящих данных|Count|Среднее|Разница между порядковым номером последней поставленной в очередь сообщения в секции источника событий и порядковым номером сообщений, обрабатываемых в входящих данных|Отсутствуют|
|вармсторажемакспропертиес|Максимальное число свойств для горячего хранения|Count|Максимальная|Максимальное число свойств, разрешенное средой для SKU S1/S2, и максимальное количество свойств, разрешенных в "горячий" магазин для SKU PAYG|Отсутствуют|
|вармсторажеуседпропертиес|Свойства использования теплого хранилища |Count|Максимальная|Число свойств, используемых средой для SKU S1/S2, и количество свойств, используемых в "горячем" магазине для SKU PAYG|Отсутствуют|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Байт/с|Среднее|Средняя пропускная способность диска из-за операций чтения за период выборки.|Отсутствуют|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Байт/с|Среднее|Средняя пропускная способность диска из-за операций записи за период выборки.|Отсутствуют|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итог|Общая пропускная способность диска из-за операций чтения за период выборки.|Отсутствуют|
|Disk Write Bytes|Disk Write Bytes|Байты|Итог|Общая пропускная способность диска из-за операций записи за период выборки.|Отсутствуют|
|DiskReadOperations|Операции чтения с диска|Count|Итог|Число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Отсутствуют|
|DiskWriteOperations|Операции записи на диск|Count|Итог|Число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Отсутствуют|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Среднее|Среднее число операций чтения ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Отсутствуют|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Среднее|Среднее число операций записи ввода-вывода в предыдущем периоде выборки. Обратите внимание, что эти операции могут иметь переменный размер.|Отсутствуют|
|DiskReadLatency|Задержка чтения с диска|Миллисекунды|Среднее|Общая задержка чтения. Сумма задержек чтения устройства и ядра.|Отсутствуют|
|DiskWriteLatency|Задержка записи на диск|Миллисекунды|Среднее|Общая задержка записи. Сумма задержек при записи в устройство и ядро.|Отсутствуют|
|NetworkInBytesPerSecond|Сетевых входящих байт/с|Байт/с|Среднее|Средняя пропускная способность сети для полученного трафика.|Отсутствуют|
|NetworkOutBytesPerSecond|Сетевых исходящих байт/с|Байт/с|Среднее|Средняя пропускная способность сети для передаваемого трафика.|Отсутствуют|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итог|Общая пропускная способность сети для полученного трафика.|Отсутствуют|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итог|Общая пропускная способность сети для передаваемого трафика.|Отсутствуют|
|MemoryUsed|Используемая память|Байты|Среднее|Объем памяти компьютера, используемой виртуальной машиной.|Отсутствуют|
|MemoryGranted|Предоставленная память|Байты|Среднее|Объем памяти, предоставленной виртуальной машине узлом. Память не предоставляется узлу до тех пор, пока она не будет затронута один раз и выделенная память может быть переключена или выведена из появления, если Вмкернел нуждается в памяти.|Отсутствуют|
|MemoryActive|Активная память|Байты|Среднее|Объем памяти, используемой виртуальной машиной в прошлом маленьком окне времени. Это "истинное" количество памяти, которое в настоящее время требуется виртуальной машине. Дополнительная неиспользуемая память может быть переключена или повышена, что не влияет на производительность гостевой системы.|Отсутствуют|
|Percentage CPU|Percentage CPU|Процент|Среднее|Загрузка ЦП. Это значение указывается в 100%, представляющем все ядра процессора в системе. Например, 2-сторонняя виртуальная машина, использующая 50% системы из четырех ядер, полностью использует два ядра.|Отсутствуют|
|PercentageCpuReady|Состояние готовности ЦП (CPU Ready)|Миллисекунды|Итог|Время готовности — это время, затрачиваемое на ожидание доступности ЦП (ов) за последний интервал обновления.|Отсутствуют|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Count|Среднее|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Среднее|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|TcpSynSent|Состояние TCP SYN-SENT|Count|Среднее|Состояние TCP SYN-SENT|Экземпляр|
|TcpSynReceived|Состояние TCP SYN-RECEIVED|Count|Среднее|Состояние TCP SYN-RECEIVED|Экземпляр|
|TcpEstablished|Состояние TCP ESTABLISHED|Count|Среднее|Состояние TCP ESTABLISHED|Экземпляр|
|TcpFinWait1|Состояние TCP FIN-WAIT-1|Count|Среднее|Состояние TCP FIN-WAIT-1|Экземпляр|
|TcpFinWait2|Состояние TCP FIN-WAIT-2|Count|Среднее|Состояние TCP FIN-WAIT-2|Экземпляр|
|TcpClosing|Состояние TCP CLOSING|Count|Среднее|Состояние TCP CLOSING|Экземпляр|
|TcpCloseWait|Состояние TCP CLOSE-WAIT|Count|Среднее|Состояние TCP CLOSE-WAIT|Экземпляр|
|TcpLastAck|Состояние TCP LAST-ACK|Count|Среднее|Состояние TCP LAST-ACK|Экземпляр|
|TcpTimeWait|Состояние TCP TIME-WAIT|Count|Среднее|Состояние TCP TIME-WAIT|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций) 

> [!NOTE]
> **Использование файловой системы** — это новая метрика, которая создается глобально. Если вы не список разрешений для закрытой предварительной версии, данные не ожидаются.

> [!IMPORTANT]
> **Среднее время отклика** будет нерекомендуемым, чтобы избежать путаницы с агрегатами метрик. Используйте **время ответа** в качестве замены.

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
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
|HttpResponseTime|Время ответа|Секунды|Итог|Время ответа|Экземпляр|
|AverageResponseTime|Среднее время отклика (не рекомендуется)|Секунды|Среднее|Среднее время ответа:|Экземпляр|
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
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|Отсутствуют|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

> [!NOTE]
> **Использование файловой системы** — это новая метрика, которая создается глобально. Если вы не список разрешений для закрытой предварительной версии, данные не ожидаются.

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байты|Итог|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итог|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Итог|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|МБ/МС|Итог|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Итог|Function Execution Count|Экземпляр|
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
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|Отсутствуют|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
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
|филесистемусаже|Использование файловой системы|Байты|Среднее|Использование файловой системы|Отсутствуют|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
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
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Среднее|Общее число внешних интерфейсов|Отсутствуют|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Count|Среднее|Рабочие роли плана службы приложений уровня "Малый"|Отсутствуют|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Среднее|Рабочие процессы плана службы приложений уровня "Средний"|Отсутствуют|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Count|Среднее|Рабочие процессы плана службы приложений уровня "Крупный"|Отсутствуют|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единицы|Тип статистической обработки|Описание:|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Count|Среднее|Общее количество рабочих процессов|Отсутствуют|
|WorkersAvailable|Доступные рабочие процессы|Count|Среднее|Доступные рабочие процессы|Отсутствуют|
|WorkersUsed|Использованные рабочие процессы|Count|Среднее|Использованные рабочие процессы|Отсутствуют|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|
## <a name="next-steps"></a>Дальнейшие шаги
* [Прочитайте о метриках в Azure Monitor](data-platform.md)
* [Создание оповещений на основе метрик](alerts-overview.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](platform-logs-overview.md)

