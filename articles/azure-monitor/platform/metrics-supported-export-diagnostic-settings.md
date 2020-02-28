---
title: Azure Monitor экспортировать метрики платформы через параметры диагностики
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661368"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor экспортировать метрики платформы через параметры диагностики

Azure Monitor предоставляет [метрики платформы](data-platform-metrics.md) по умолчанию без конфигурации. Она предоставляет несколько способов взаимодействия с метриками платформы, включая их диаграмму на портале, доступ к ним через REST API или выполнение запросов с помощью PowerShell или CLI. Полный список метрик платформы, доступных в настоящее время с помощью конвейера консолидированной метрики Azure Monitor, см. в статье [Поддерживаемые метрики](metrics-supported.md) . Чтобы запросить эти метрики и получить к ним доступ, используйте [версию api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий.

Вы можете экспортировать метрики платформы из конвейера Azure Monitor в другие расположения одним из двух способов.
1. Использование [параметров диагностики](diagnostic-settings.md) для отправки в log Analytics, концентраторы событий или службу хранилища Azure.
2. Использование [метрик REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Из-за тонкостей в Azure Monitor серверной части не все метрики могут быть экспортированы с помощью параметров диагностики. В таблице ниже перечислены параметры, которые можно и нельзя экспортировать с помощью параметров диагностики.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Изменение поведения в отношении значений NULL и нулевых значений 
 
Для метрик платформы, которые можно экспортировать с помощью параметров диагностики, существует несколько метрик, для которых Azure Monitor интерпретирует "0" как "NULLS". Это привело к путанице между реальным "0" (порожденным ресурсом) и интерпретированным "0" (NULLS). Начиная с **1 апреля 2020** метрики платформы, экспортированные с помощью параметров диагностики, больше не будут экспортировать "0", если только они не были действительно переданы базовым ресурсом. Примечание.

1.  При удалении группы ресурсов или определенного ресурса данные метрик из затронутых ресурсов больше не будут отправляться в целевые объекты экспорта параметров диагностики. Это значит, что он больше не будет отображаться в концентраторах событий, учетных записях хранения и Log Analytics рабочих областях.
2.  Это улучшение будет доступно во всех общедоступных и частных облаках.
3.  Это изменение не повлияет на поведение любого из следующих приложений: 
   - Журналы ресурсов платформы, экспортированные с помощью параметров диагностики
   - Диаграмма метрик в обозреватель метрик
   - Предупреждения о метриках платформы
 
## <a name="metrics-exportable-table"></a>Экспортируемая таблица метрик 

В таблице содержатся следующие столбцы. 
- Экспортировать с помощью параметров диагностики? 
- Влияет на значение NULL/0 
- ResourceType 
- Метрика 
- метрикдисплайнаме
- Единицы 
- AggregationType


> [!NOTE]
> В таблице ниже может быть горизонтальная полоса прокрутки внизу. Если вы считаете, что данные отсутствуют, убедитесь, что полоса прокрутки находится слева.  


| Экспортировать с помощью параметров диагностики?  | Выдает значения NULL |  ResourceType  |  Метрика  |  метрикдисплайнаме  |  Единицы  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Память: текущая цена очистки  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Память: несжимаемая память очистки  |  Байт  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Память: сжимаемая память очистки  |  Байт  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Потоки: занятые потоки в пуле команд  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Потоки: бездействующие потоки в пуле команд  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Длина очереди заданий пула команд  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Подключение: текущие подключения  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Текущие пользовательские сеансы  |  Число  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Потоки: занятые потоки продолжительного анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Потоки: бездействующие потоки продолжительного анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Потоки: длина очереди заданий продолжительного анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Память подсистемы M  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Байт исключительного числа модулей M  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU подсистемы M  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Виртуальный байт модуля M  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Память  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Пробуксовка памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Память: твердый лимит памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Память: верхний предел памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Память: нижний предел памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Память: ограничение памяти VertiPaq  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Память: использование памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Байт исключительного пользования  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Потоки: занятые потоки ввода-вывода в пуле обработки заданий  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Потоки: длина очереди заданий ввода-вывода пула обработки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Длина очереди заданий пула обработки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Занятые потоки в пуле запросов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Потоки: бездействующие потоки в пуле запросов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Потоки: длина очереди заданий пула запросов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  Квота  |  Память: квота  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Память: заблокировано квот  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Обработка: преобразовано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Обработка: считано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Обработка: записано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Потоки: занятые потоки быстрого анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Потоки: бездействующие потоки быстрого анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Потоки: длина очереди заданий быстрого анализа  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Число успешных подключений в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Общее число неудачных подключений  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Общее число запросов на подключение  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Память: размер нестраничных данных VertiPaq  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Память: размер страничных данных VertiPaq  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Байт виртуальной памяти  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  баккенддуратион  |  Длительность внутренних запросов  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Длительность  |  Общая длительность запросов шлюза  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубдроппедевентс  |  Удалены события EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубрежектедевентс  |  Отклоненные события EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубсукцессфулевентс  |  Успешные события EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубсроттледевентс  |  Регулируемые события EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтимедаутевентс  |  Истекло время ожидания событий EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталбитессент  |  Размер событий EventHub  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталевентс  |  Всего событий EventHub  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталфаиледевентс  |  События EventHub, завершившиеся сбоем  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Неудачные запросы к шлюзу (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Другие запросы к шлюзу (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Запросы  |  Запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Успешные запросы к шлюзу (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Всего запросов к шлюзу (устарело)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Неавторизованные запросы к шлюзу (устарели)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  аппкпуусажеперцентаже  |  Процент использования ЦП приложением  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  аппмеморикоммиттед  |  Назначенная память приложения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  аппмеморимакс  |  Максимальная память приложения  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  аппмеморюсед  |  Используемая память приложения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  гкпаусетоталкаунт  |  Счетчик приостановки GC  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  гкпаусетоталтиме  |  Общее время приостановки сборки мусора  |  Миллисекунд  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  максолдженмеморипулбитес  |  Максимальный доступный размер данных старого поколения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  олдженмеморипулбитес  |  Размер данных старого поколения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  олдженпромотедбитес  |  Повысить до старого размера данных поколения  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  системкпуусажеперцентаже  |  Процент использования процессора системой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатерроркаунт  |  Глобальная ошибка Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатрецеиведбитес  |  Всего получено байт в Tomcat  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуестмакстиме  |  Максимальное время запроса Tomcat  |  Миллисекунд  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуесттоталкаунт  |  Общее число запросов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуесттоталтиме  |  Общее время запроса Tomcat  |  Миллисекунд  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатреспонсеавгтиме  |  Среднее время запроса Tomcat  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсентбитес  |  Всего отправленных байтов Tomcat  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионактивекурренткаунт  |  Счетчик активности сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионактивемакскаунт  |  Максимальное число активных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионаливемакстиме  |  Максимальное время активности сеанса Tomcat  |  Миллисекунд  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионкреатедкаунт  |  Число созданных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионекспиредкаунт  |  Счетчик с истекшим сроком действия сеанса Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионрежектедкаунт  |  Число отклоненных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Аппплатформ/пружина  |  йоунгженпромотедбитес  |  Превращение в размер данных создания Титов.  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Всего заданий  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  тоталупдатедеплойментмачинерунс  |  Всего запусков компьютера для развертывания обновлений  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Automation/automationAccounts  |  тоталупдатедеплойментрунс  |  Всего запусков развертывания обновлений  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedicated Core Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Creating Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Idle Node Count  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Job Delete Complete Events (События окончания удаления задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Job Delete Start Events (События начала удаления задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Job Disable Complete Events (События окончания отключения задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Job Disable Start Events (События начала отключения задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Job Start Events (События запуска задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Job Terminate Complete Events (События окончания завершения задания)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Job Terminate Start Events (События начала завершения задания)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Leaving Pool Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority Core Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Offline Node Count  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Pool Create Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Pool Delete Complete Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Pool Delete Start Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Pool Resize Complete Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Pool Resize Start Events  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Preempted Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Rebooting Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Reimaging Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Running Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Starting Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Start Task Failed Node Count  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Task Complete Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Task Fail Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Task Start Events  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Low-Priority Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Dedicated Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Unusable Node Count  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Waiting For Start Task Node Count  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Активные ядра  |  Активные ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Активные узлы  |  Активные узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Бездействующие ядра  |  Бездействующие ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Бездействующие узлы  |  Бездействующие узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Задание выполнено  |  Задание выполнено  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Задание Отправлено  |  Задание Отправлено  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Освобождение ядер  |  Освобождение ядер  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Покинуть узлы  |  Покинуть узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Вытесненные ядра  |  Вытесненные ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Прерванные узлы  |  Прерванные узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Процент использования квоты  |  Процент использования квоты  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Всего ядер  |  Всего ядер  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Всего узлов  |  Всего узлов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Непригодные для использования ядра  |  Непригодные для использования ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. BatchAI/рабочие области  |  Неиспользуемые узлы  |  Неиспользуемые узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионакцептед  |  Принятые подключения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионактиве  |  Активные подключения  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионхандлед  |  Обработанные соединения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  кпуусажеперцентажеиндаубле  |  Процент использования ЦП  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  иореадбитес  |  Считанные байты ввода-вывода  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  иовритебитес  |  Число операций записи ввода-вывода  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  MemoryLimit  |  Ограничение памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  MemoryUsage  |  Использование памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  меморюсажеперцентажеиндаубле  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  пендингтрансактионс  |  Ожидающие транзакции  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  процесседблоккс  |  Обработанные блоки  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  процесседтрансактионс  |  Обработанные транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  куеуедтрансактионс  |  Транзакции в очереди  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  рекуессандлед  |  Обработанные запросы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  сторажеусаже  |  Использование хранилища  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits  |  Попаданий в кэш  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheLatency  |  Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses  |  Промахов в кэше  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead  |  Чтение из кэша  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead0  |  Cache Read (Shard 0)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead1  |  Cache Read (Shard 1)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead2  |  Cache Read (Shard 2)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead3  |  Cache Read (Shard 3)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead4  |  Cache Read (Shard 4)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead5  |  Cache Read (Shard 5)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead6  |  Cache Read (Shard 6)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead7  |  Cache Read (Shard 7)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead8  |  Cache Read (Shard 8)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheRead9  |  Cache Read (Shard 9)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite  |  Запись в кэш  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache Write (Shard 0)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache Write (Shard 1)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache Write (Shard 2)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache Write (Shard 3)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache Write (Shard 4)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache Write (Shard 5)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache Write (Shard 6)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache Write (Shard 7)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache Write (Shard 8)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache Write (Shard 9)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients  |  Подключенные клиенты  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients0  |  Connected Clients (Shard 0)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients1  |  Connected Clients (Shard 1)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients2  |  Connected Clients (Shard 2)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients3  |  Connected Clients (Shard 3)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients4  |  Connected Clients (Shard 4)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients5  |  Connected Clients (Shard 5)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients6  |  Connected Clients (Shard 6)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients7  |  Connected Clients (Shard 7)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients8  |  Connected Clients (Shard 8)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  connectedclients9  |  Connected Clients (Shard 9)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  ошибки  |  Ошибки  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys  |  Исключенные ключи  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys0  |  Evicted Keys (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys1  |  Evicted Keys (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys2  |  Evicted Keys (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys3  |  Evicted Keys (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys4  |  Evicted Keys (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys5  |  Evicted Keys (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys6  |  Evicted Keys (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys7  |  Evicted Keys (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys8  |  Evicted Keys (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  evictedkeys9  |  Evicted Keys (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys  |  Ключи с истекшим сроком действия  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys0  |  Expired Keys (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys1  |  Expired Keys (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys2  |  Expired Keys (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys3  |  Expired Keys (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys4  |  Expired Keys (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys5  |  Expired Keys (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys6  |  Expired Keys (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys7  |  Expired Keys (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys8  |  Expired Keys (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  expiredkeys9  |  Expired Keys (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands  |  Возвращает  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond  |  Количество операций в секунду  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Количество операций в секунду (сегмент 0).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Количество операций в секунду (сегмент 1).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Количество операций в секунду (сегмент 2).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Количество операций в секунду (сегмент 3).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Количество операций в секунду (сегмент 4).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Количество операций в секунду (сегмент 5).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Количество операций в секунду (сегмент 6).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Количество операций в секунду (сегмент 7).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Количество операций в секунду (сегмент 8).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Количество операций в секунду (сегмент 9).  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime  |  ЦП  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad  |  Загрузка сервера  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad0  |  Server Load (Shard 0)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad1  |  Server Load (Shard 1)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad2  |  Server Load (Shard 2)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad3  |  Server Load (Shard 3)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad4  |  Server Load (Shard 4)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad5  |  Server Load (Shard 5)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad6  |  Server Load (Shard 6)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad7  |  Server Load (Shard 7)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad8  |  Server Load (Shard 8)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  serverLoad9  |  Server Load (Shard 9)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands  |  Наборы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands1  |  Sets (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands2  |  Sets (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  getcommands5  |  Sets (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Всего операций  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total Operations (Shard 0)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total Operations (Shard 1)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total Operations (Shard 2)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total Operations (Shard 3)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total Operations (Shard 4)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total Operations (Shard 5)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total Operations (Shard 6)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total Operations (Shard 7)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total Operations (Shard 8)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total Operations (Shard 9)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys  |  Всего ключей  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys0  |  Total Keys (Shard 0)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys1  |  Total Keys (Shard 1)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys2  |  Total Keys (Shard 2)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys3  |  Total Keys (Shard 3)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys4  |  Total Keys (Shard 4)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys5  |  Total Keys (Shard 5)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys6  |  Total Keys (Shard 6)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys7  |  Total Keys (Shard 7)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys8  |  Total Keys (Shard 8)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  totalkeys9  |  Total Keys (Shard 9)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory  |  Объем используемой памяти  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory0  |  Used Memory (Shard 0)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory1  |  Used Memory (Shard 1)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory2  |  Used Memory (Shard 2)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory3  |  Used Memory (Shard 3)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory4  |  Used Memory (Shard 4)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory5  |  Used Memory (Shard 5)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory6  |  Used Memory (Shard 6)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory7  |  Used Memory (Shard 7)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory8  |  Used Memory (Shard 8)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemory9  |  Used Memory (Shard 9)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Процент используемой памяти  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS используемой памяти  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Used Memory RSS (Shard 0)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Used Memory RSS (Shard 1)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Used Memory RSS (Shard 2)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Used Memory RSS (Shard 3)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Used Memory RSS (Shard 4)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Used Memory RSS (Shard 5)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Used Memory RSS (Shard 6)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Used Memory RSS (Shard 7)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Used Memory RSS (Shard 8)  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Used Memory RSS (Shard 9)  |  Байт  |  Максимальное значение | 
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.classicСompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  BlobCount  |  Количество больших двоичных объектов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  IndexCapacity  |  Емкость индекса  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileCapacity  |  Емкость файла  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileCount  |  Количество файлов  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileShareCount  |  Количество общих файловых ресурсов  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешарекуота  |  Размер квоты общей папки  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешареснапшоткаунт  |  Число моментальных снимков общей папки  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешареснапшотсизе  |  Размер моментального снимка файлового ресурса  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueCapacity  |  Емкость очереди  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueCount  |  Количество очередей  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueMessageCount  |  Количество сообщений очереди  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableCapacity  |  Емкость таблицы  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableCount  |  Количество таблиц  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableEntityCount  |  Количество сущностей таблиц  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Blocked Calls  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  чарактерстраинед  |  Обученные символы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Преобразованные символы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Client Errors  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Входящие данные:  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Выходные данные  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  Задержка  |  Задержка  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server Errors  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Длительность речи  |  Секунды  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Successful Calls  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Total Calls  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Всего ошибок  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Всего вызовов токенов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Всего транзакций  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Глубина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Всего сети  |  Всего сети  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Глубина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachines  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Глубина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Bytes  |  Disk Write Bytes  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Входящие потоки  |  Входящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Всего сети  |  Всего сети  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Глубина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Исходящие потоки  |  Исходящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Глубина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Всего сети  |  Всего сети  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Глубина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Загрузка ЦП  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Использование памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Получено байтов по сети в секунду  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Передано байт по сети в секунду  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  рундуратион  |  Длительность выполнения  |  Миллисекунд  |  Всего | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  сукцессфулпуллкаунт  |  Число успешных операций извлечения  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  сукцессфулпушкаунт  |  Число успешных push-уведомлений  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  тоталпуллкаунт  |  Общее число броских  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.ContainerRegistry/registries  |  тоталпушкаунт  |  Общее число push-уведомлений  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Общее количество доступных ядер ЦП в управляемом кластере  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Общий объем доступной памяти в управляемом кластере  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Состояния для различных условий узла  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Число модулей pod по фазам  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Число модулей pod в состоянии готовности  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  AvailableCapacity  |  Доступная емкость  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  битесуплоадедтоклауд  |  Отправлено облачных байт (устройство)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  битесуплоадедтоклаудпершаре  |  Отправлено облачных байт (общий ресурс)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудреадсраугхпут  |  Пропускная способность скачивания в облаке  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудреадсраугхпутпершаре  |  Пропускная способность скачивания в облаке (общая папка)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудуплоадсраугхпут  |  Пропускная способность передачи в облаке  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудуплоадсраугхпутпершаре  |  Пропускная способность передачи в облаке (общая папка)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  хипервмеморютилизатион  |  Использование памяти при вычислении граничных ресурсов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  хиперввиртуалпроцессорутилизатион  |  Вычисление ребра — процент ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  никреадсраугхпут  |  Пропускная способность чтения (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  никвритесраугхпут  |  Пропускная способность записи (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  Общая емкость составляет  |  Общая емкость  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/datafactories  |  FailedRuns и  |  циклы выполнения со сбоем;  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns.  |  успешные циклы выполнения.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  активитиканцелледрунс  |  Отмененные метрики выполнения действий  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ActivityFailedRuns  |  Метрики неудачных выполнений действий.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ActivitySucceededRuns  |  Метрики успешных выполнений действий.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  факторисизеингбунитс  |  Общий размер фабрики (ГБ единиц)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeAvailableMemory  |  Доступная память для среды выполнения интеграции  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  интегратионрунтимеаверажетаскпиккупделай  |  Длительность очереди среды выполнения интеграции  |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeCpuPercentage  |  Использование ЦП средой выполнения интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  интегратионрунтимекуеуеленгс  |  Длина очереди среды выполнения интеграции  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  максалловедфакторисизеингбунитс  |  Максимально допустимый размер фабрики (единица GB)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  максалловедресаурцекаунт  |  Максимальное число допустимых сущностей  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  пипелинеканцелледрунс  |  Отмененные метрики запуска конвейера  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  PipelineFailedRuns  |  Метрики неудачных выполнений конвейеров.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  PipelineSucceededRuns  |  Метрики успешных выполнений конвейеров.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  ресаурцекаунт  |  Общее число сущностей  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  тригжерканцелледрунс  |  Отмененные метрики запуска триггера  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  TriggerFailedRuns  |  Метрики неудачных запусков триггеров.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataFactory/factories;  |  TriggerSucceededRuns  |  Метрики успешных запусков триггеров.  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Cancelled AU Time  |  Секунды  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Failed AU Time  |  Секунды  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Successful AU Time  |  Секунды  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Cancelled Jobs  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Failed Jobs  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Successful Jobs  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Данных прочитано  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Записанные данные  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Запросы на чтение  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Общий объем хранилища  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Запросы на запись  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Активные подключения  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Неудачные подключения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Storage limit  |  Байт  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Storage used  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Активные подключения  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Неудачные подключения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байт  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Storage limit  |  Байт  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Storage used  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Активные подключения  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Неудачные подключения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Максимальная задержка между репликами  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Задержка реплики  |  Секунды  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Storage limit  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Storage used  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  active_connections  |  Активные подключения  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  /  |  ОПЕРАЦИЙ ВВОДА-ВЫВОДА  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Дбфорпостгрескл/serversv2  |  storage_used  |  Storage used  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Devices/Account  |  дигиталтвинс. телеметрии. nodes  |  Заполнитель данных телеметрии для узла Digital двойников  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Отброшенных сообщений C2D  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Доставка сообщений C2D завершена  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Отклоненные сообщения C2D  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Неудачные вызовы прямых методов.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Размер запроса вызовов прямых методов.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Размер ответа вызовов прямых методов.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Успешные вызовы прямых методов.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные из серверной части.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных из серверной части.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Успешные операции чтения с двойников, инициированные из серверной части.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Неудачные обновления двойников, инициированные из серверной части.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Размер обновлений двойников, инициированных из серверной части.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Успешные обновления двойников, инициированные из серверной части.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Срок действия сообщений C2D истек (Предварительная версия)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  конфигурации  |  Configuration Metrics (Метрики конфигурации)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Connected devices (preview) (Подключенных устройств (предварительная версия))  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage  |  Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage.BLOBs  |  Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.latency.Storage  |  Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии)   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии)   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Число предпринятых попыток отправки сообщений телеметрии.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Количество ошибок регулирования  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Число отправленных сообщений телеметрии.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные устройством.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных устройством.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Успешные операции чтения с двойников, инициированные устройством.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Неудачные обновления двойников, инициированные устройством.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Размер обновлений двойников, инициированных устройством.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Успешные обновления двойников, инициированные устройством.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Общее количество используемых сообщений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Общее использование данных устройства  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Connected devices (deprecated) (Подключенные устройства (не рекомендуется))   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Total devices (deprecated) (Всего устройств (не рекомендуется))  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  евентгридделивериес  |  Сетка событий: доставок (Предварительная версия)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  евентгридлатенци  |  Задержка сетки событий (Предварительная версия)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Неудачные отмены заданий.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Успешные отмены заданий.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Завершенные задания  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Неудачные операции создания заданий вызова методов.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Успешные операции создания заданий вызова методов.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Неудачные операции создания заданий обновления двойников.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Успешные операции создания заданий обновления двойников.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Неудачные задания.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Неудачные вызовы получения списка заданий.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Успешные вызовы получения списка заданий.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Неудачные запросы заданий.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Успешные запросы заданий.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total devices (preview) (Всего устройств (предварительная версия))  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Неудачные запросы двойников.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Размер результатов запросов двойников.  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Успешные запросы двойников.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Попытки аттестации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Назначенные устройства  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Попытки регистрации  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Доступное хранилище  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Отключение связи Cassandra  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Расходы запросов по Cassandra  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Запросы по Cassandra  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Использование данных  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  делетевиртуалнетворк  |  делетевиртуалнетворк  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Число документов  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Квота на документы  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Использование индексов  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Запросы метаданных  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Запросить оплату Mongo  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Запросы Mongo  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестскаунт  |  Частота запросов Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсделете  |  Частота запросов на удаление Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсинсерт  |  Частота запросов на вставку Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестскуери  |  Частота запросов Mongo запросов  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсупдате  |  Частота запросов на обновление Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Подготовленная пропускная способность  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Задержка репликации P99  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Доступность службы  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Общее количество запросов  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Общее количество единиц запросов  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  FailureCount  |  Число сбоев  |  Число  |  Число | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  SuccessCount  |  Подсчет успешных событий  |  Число  |  Число | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  сукцесслатенци  |  Задержка успешного выполнения  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  трансактионкаунт  |  Число транзакций  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft. EventGrid/Domains  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft. EventGrid/Domains  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DroppedEventCount  |  Удаленные события  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  MatchedEventCount  |  Соответствующие события  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  PublishFailCount  |  Опубликовать события с ошибками  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Удаленные события  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Соответствующие события  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Опубликовать события с ошибками  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Опубликовать события с ошибками  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Доступная память  |  Процент  |  Максимальное значение | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Невыполненная работа записи.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Записанные байты.  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Записанные сообщения.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Закрытые подключения.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Открытые подключения.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ЦП  |  ЦП  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Входящие байты.  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Входящие сообщения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Входящих запросов  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Исходящие байты.  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Исходящие сообщения  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ServerErrors  |  Ошибки сервера.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Выполненные запросы  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Регулируемые запросы.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  UserErrors  |  Ошибки пользователей.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Невыполненная работа записи.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Записанные байты.  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Записанные сообщения.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHABL  |  Архивировать сообщения невыполненной работы (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Пропускная способность сообщений архивации (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Архивирование сообщений (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Входящие байты (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Входящие байты (устарели) (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Входящие сообщения (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Исходящие байты (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Исходящие байты (устарели) (не рекомендуется)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Исходящие сообщения (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Неудачные запросы (устарело)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Входящие байты.  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Входящих запросов  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Входящие сообщения (устаревшие) (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INREQS  |  Входящие запросы (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INTERR  |  Внутренние ошибки сервера (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Другие ошибки (не рекомендуется)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Исходящие байты.  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Исходящие сообщения (устаревшие) (не рекомендуется)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  Размер  |  Размер  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Успешные запросы (устарело)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Ошибки занятости сервера (устарели)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Запросы к шлюзу по категориям  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Запросы к шлюзу  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  нумактивеворкерс  |  Число активных рабочих ролей  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.HDInsight/clusters  |  скалингрекуестс  |  Запросы на масштабирование  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Пороговое значение метрики  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Наблюдаемая емкость  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Наблюдаемое значение метрики  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Инициированные действия масштабирования  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  availabilityResults/Аваилабилитиперцентаже  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  availabilityResults/Count  |  Тесты доступности  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Длительность теста доступности  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Время подключения к сети при загрузке страницы  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Время обработки на стороне клиента  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Время получения ответа  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Время отправки запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Время загрузки страницы в браузере  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/count  |  Вызовы зависимостей  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  dependencies/duration  |  Длительность зависимости  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/failed  |  Сбои при вызове зависимостей  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/browser  |  Исключения браузера  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  exceptions/count  |  Исключения  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/server  |  Исключения сервера  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  pageViews/count  |  Просмотры страниц  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  pageViews/duration  |  Время загрузки страницы для просмотра  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Частота исключений  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Объем доступной памяти  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Обработка ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Скорость ввода-вывода процесса  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Процессорное время  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Количество байтов исключительного использования процессов  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Время выполнения HTTP-запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP requests in application queue (HTTP-запросы в очереди приложений)  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Скорость HTTP-запроса  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/count  |  Запросы сервера  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.Insights/Components  |  requests/duration  |  времени ответа сервера;  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/failed  |  Failed requests (Неудачные запросы)  |  Число  |  Число | 
| Нет  | Нет |  Microsoft.Insights/Components  |  запросов и ставок  |  Частота запросов сервера  |  Число/с  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  traces/count  |  Трассировки  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Всего попаданий в API службы  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Общая задержка API службы  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Всего результатов для API службы  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  качеутилизатион  |  Использование кэша  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортмакслатенессминутес  |  Максимальное время непрерывного экспорта (мин.)  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортнумофрекордсекспортед  |  Непрерывный экспорт-число экспортированных записей  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортпендингкаунт  |  Число ожидающих непрерывного экспорта  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортресулт  |  Результат непрерывного экспорта  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ЦП  |  ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  евентспроцесседфоревенсубс  |  Обработано событий (для концентраторов событий и IoT)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  експортутилизатион  |  Использование экспорта  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионлатенциинсекондс  |  Задержка приема (в секундах)  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Результат приема  |  Число  |  Число | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионутилизатион  |  Использование приема  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионволумеинмб  |  Объем приема (в МБ)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Проверки  |  Срок поддержания активности  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Query duration (Длительность запросов)  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стеамингинжестрекуестрате  |  Частота запросов приема потоковой передачи  |  Число  |  ратерекуестсперсеконд | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестдатарате  |  Скорость приема данных потоковой передачи  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестдуратион  |  Длительность приема потоковой передачи  |  Миллисекунд  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестресултс  |  Поток приема результатов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsCompleted  |  Actions Completed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsFailed  |  Actions Failed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsSkipped  |  Actions Skipped   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsStarted  |  Actions Started   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsSucceeded  |  Actions Succeeded   |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionThrottledEvents  |  Action Throttled Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментконнектормеморюсаже  |  Использование памяти соединителя для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментконнекторпроцессорусаже  |  Использование процессора соединителя для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментворкфловмеморюсаже  |  Использование памяти рабочего процесса для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментворкфловпроцессорусаже  |  Использование процессора рабочих процессов для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsCancelled  |  Выполнение отменено  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsCompleted  |  Runs Completed  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsFailed  |  Runs Failed  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsStarted  |  Runs Started  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsSucceeded  |  Runs Succeeded  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  рунстартсроттледевентс  |  Запустить регулируемые события запуска  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunThrottledEvents  |  Run Throttled Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersCompleted  |  Triggers Completed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersFailed  |  Triggers Failed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersFired  |  Triggers Fired   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersSkipped  |  Triggers Skipped  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersStarted  |  Triggers Started   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersSucceeded  |  Triggers Succeeded   |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Actions Completed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Actions Failed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Actions Skipped   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Actions Started   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Actions Succeeded   |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Action Throttled Events  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Billable Action Executions  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Billable Trigger Executions  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Выполнение отменено  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Runs Completed  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsFailed  |  Runs Failed  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsStarted  |  Runs Started  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Runs Succeeded  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  рунстартсроттледевентс  |  Запустить регулируемые события запуска  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Run Throttled Events  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Total Billable Executions  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Triggers Completed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Triggers Failed   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFired  |  Triggers Fired   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Triggers Skipped  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Triggers Started   |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Triggers Succeeded   |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Активные ядра  |  Активные ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Активные узлы  |  Активные узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Завершенные запуски  |  Завершенные запуски  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  циклы выполнения со сбоем;  |  циклы выполнения со сбоем;  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие ядра  |  Бездействующие ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие узлы  |  Бездействующие узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Освобождение ядер  |  Освобождение ядер  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Покинуть узлы  |  Покинуть узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Сбой развертывание модели  |  Сбой развертывание модели  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  развертывание модели запущено  |  развертывание модели запущено  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  развертывание модели успех  |  развертывание модели успех  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Не удалось зарегистрировать модель  |  Не удалось зарегистрировать модель  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Регистрация модели прошла  |  Регистрация модели прошла  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Вытесненные ядра  |  Вытесненные ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Прерванные узлы  |  Прерванные узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Процент использования квоты  |  Процент использования квоты  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Запущенные запуски  |  Запущенные запуски  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Всего ядер  |  Всего ядер  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Всего узлов  |  Всего узлов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Непригодные для использования ядра  |  Непригодные для использования ядра  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.MachineLearningServices/workspaces  |  Неиспользуемые узлы  |  Неиспользуемые узлы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Maps/accounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Maps/accounts  |  Использование  |  Использование  |  Число  |  Число | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  ассеткаунт  |  Число ресурсов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  ассеткуота  |  Квота ресурса  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  ассеткуотауседперцентаже  |  Процент используемой квоты актива  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  контенткэйполицикаунт  |  Число политик ключей содержимого  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  контенткэйполицикуота  |  Квота политики для ключа содержимого  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  контенткэйполицикуотауседперцентаже  |  Процент использования квоты для ключа содержимого  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  стреамингполицикаунт  |  Число политик потоковой передачи  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  стреамингполицикуота  |  Квота политики потоковой передачи  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices  |  стреамингполицикуотауседперцентаже  |  Процент используемой квоты политики потоковой передачи  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  Запросы  |  Запросы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  SuccessE2ELatency  |  Успешное завершение до окончания задержки  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  гкпаусетоталкаунт  |  Счетчик приостановки GC  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  гкпаусетоталтиме  |  Общее время приостановки сборки мусора  |  Миллисекунд  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  максолдженмеморипулбитес  |  Максимальный доступный размер данных старого поколения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  олдженмеморипулбитес  |  Размер данных старого поколения  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  олдженпромотедбитес  |  Повысить до старого размера данных поколения  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицекпуусажеперцентаже  |  Процент использования ЦП службой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморикоммиттед  |  Назначенная память службы  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморимакс  |  Максимальная память службы  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморюсед  |  Используемая память службы  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  системкпуусажеперцентаже  |  Процент использования процессора системой  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатерроркаунт  |  Глобальная ошибка Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрецеиведбитес  |  Всего получено байт в Tomcat  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуестмакстиме  |  Максимальное время запроса Tomcat  |  Миллисекунд  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуесттоталкаунт  |  Общее число запросов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуесттоталтиме  |  Общее время запроса Tomcat  |  Миллисекунд  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатреспонсеавгтиме  |  Среднее время запроса Tomcat  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсентбитес  |  Всего отправленных байтов Tomcat  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионактивекурренткаунт  |  Счетчик активности сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионактивемакскаунт  |  Максимальное число активных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионаливемакстиме  |  Максимальное время активности сеанса Tomcat  |  Миллисекунд  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионкреатедкаунт  |  Число созданных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионекспиредкаунт  |  Счетчик с истекшим сроком действия сеанса Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионрежектедкаунт  |  Число отклоненных сеансов Tomcat  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Microservices4Spring/Аппклустерс  |  йоунгженпромотедбитес  |  Превращение в размер данных создания Титов.  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Volume pool allocated used (Используемый размер выделенного пула для тома)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Volume pool total logical size (Общий логический размер пула для тома)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  AverageReadLatency  |  Средняя задержка чтения  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  AverageWriteLatency  |  Средняя задержка записи  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  ReadIops  |  Read iops (Число операций ввода-вывода в секунду при чтении)  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  VolumeLogicalSize  |  Volume logical size (Логический размер тома)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  VolumeSnapshotSize  |  Volume snapshot size (Размер моментальных снимков в томе)  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  WriteIops  |  Write iops (Количество операций ввода-вывода в секунду при записи)  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  аппликатионгатевайтоталтиме  |  Общее время шлюза приложений  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  авгрекуесткаунтперхеалсихост  |  Количество запросов в минуту на исправный узел  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендконнекттиме  |  Время соединения серверной части  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендфирстбитереспонсетиме  |  Время ответа первого байта внутреннего сервера  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендластбитереспонсетиме  |  Время последнего ответа серверной части  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  баккендреспонсестатус  |  Состояние ответа серверной части  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  блоккедкаунт  |  Распределение правил блокированных запросов брандмауэра веб-приложения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  блоккедреккаунт  |  Число заблокированных запросов брандмауэра веб-приложения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Получено байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  BytesSent  |  Отправлено байт  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  капаЦитюнитс  |  Текущие единицы мощности  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  клиентртт  |  RTT клиента  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  компутеунитс  |  Текущие единицы вычислений  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Текущие соединения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Невыполненные запросы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Количество работоспособных узлов.  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  матчедкаунт  |  Общее распространение правила брандмауэра веб-приложения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Состояние ответа.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  Пропускная способность  |  Пропускная способность  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  тлспротокол  |  Клиентский протокол TLS  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Общее количество запросов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Количество неработоспособных узлов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  аппликатионрулехит  |  Число попаданий правил приложения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  Обработка обработанных  |  Обработанные данные  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  фиреваллхеалс  |  Состояние работоспособности брандмауэра  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  нетворкрулехит  |  Число попаданий сетевых правил  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/azurefirewalls  |  снатпортутилизатион  |  Использование портов SNAT  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/dnszones  |  QueryVolume  |  Объем запросов  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Использование емкости, доступной для наборов записей  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/dnszones  |  RecordSetCount  |  Количество наборов записей  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits  |  арпаваилабилити  |  Доступность ARP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits  |  бгпаваилабилити  |  Доступность BGP  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  глобалреачбитсинперсеконд  |  глобалреачбитсинперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  глобалреачбитсаутперсеконд  |  глобалреачбитсаутперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  косдропбитсинперсеконд  |  дроппединбитсперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  косдропбитсаутперсеконд  |  дроппедаутбитсперсеконд  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft. Network/Експрессраутегатевайс  |  ергатевайконнектионбитсинперсеконд  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft. Network/Експрессраутегатевайс  |  ергатевайконнектионбитсаутперсеконд  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  админстате  |  админстате  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  линепротокол  |  линепротокол  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  портбитсинперсеконд  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  портбитсаутперсеконд  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  ркслигхтлевел  |  ркслигхтлевел  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Network/Експрессраутепортс  |  ткслигхтлевел  |  ткслигхтлевел  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Работоспособность серверного компонента (в процентах)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Число запросов к серверному компоненту  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Backend Request Latency (Задержка запросов к серверным частям)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  биллаблереспонсесизе  |  Оплачиваемый размер ответа  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestCount  |  Число запросов  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestSize  |  Размер запроса  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Размер ответа  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Total Latency (Общая задержка)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  ByteCount  |  Количество байтов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Health Probe Status (Состояние пробы работоспособности)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  PacketCount  |  Количество пакетов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Количество подключений SNAT  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  SYNCount  |  Количество пакетов SYN  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Получено байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Отправлено байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Получено пакетов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Отправлено пакетов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Среднее время приема-передачи (МС)  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  чекксфаиледперцент  |  Процент неудачных проверок (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Доля проб с ошибками, в процентах  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  раундтриптимемс  |  Время приема-передачи (МС) (Предварительная версия)  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Количество байтов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Удаленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Перенаправленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Входящие байты (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Входящие пакеты SYN для активации защиты от атаки DDoS  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Входящие пакеты TCP для активации защиты от атаки DDoS  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Входящие пакеты UDP для активации защиты от атаки DDoS  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Выполняется ли атака DDoS  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Количество пакетов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Удаленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Перенаправленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Входящие пакеты (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Количество пакетов SYN  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Удаленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Перенаправленные входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Входящие байты TCP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Удаленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Входящие пакеты TCP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Удаленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Перенаправленные входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Входящие байты UDP (атака DDoS)  |  Байт/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Удаленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Перенаправленные входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Входящие пакеты UDP (атака DDoS)  |  Число/с  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Проверка состояния конечной точки с помощью конечной точки  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Запросы, выполняемые возвращаемой конечной точкой  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway S2S Bandwidth (Пропускная способность шлюза S2S)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S Bandwidth (Пропускная способность шлюза P2S)  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S Connection Count (Количество подключений P2S)  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Пропускная способность туннеля  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Исходящие байты туннеля  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Удаленные входящие пакеты туннеля (несоответствие селектора трафика)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Исходящие пакеты туннеля  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Входящие байты туннеля  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Входящие пакеты туннеля  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworks  |  пингмешаверажераундтрипмс  |  Время кругового пути для проверки связи с виртуальной машиной  |  MilliSeconds  |  Среднее | 
| **Да**  | Нет |  Microsoft.Network/virtualNetworks  |  пингмешпробесфаиледперцент  |  Неудачные проверки связи с виртуальной машиной  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  Входящие сообщения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Incoming.all.failedrequests  |  Все неудачные входящие запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Все входящие запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Отправлено запланированных push-уведомлений  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Отменено запланированных push-уведомлений  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Операции управления установкой  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Операции удаления установки  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Операции получения установки  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Операции исправления установки  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Операции создания или обновления установки  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Все исходящие уведомления  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Ошибки из-за поврежденного или просроченного канала  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Ошибки канала  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Ошибки полезных данных  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Ошибки внешней системы уведомлений  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Успешные уведомления  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Ошибка из-за поврежденного канала APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Ошибка из-за просроченного канала APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Ошибки авторизации APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Ошибки APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Успешные уведомления APNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Ошибки проверки подлинности GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Ошибка из-за поврежденного канала GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Ошибка из-за просроченного канала GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Ошибки авторизации GCM (недопустимые учетные данные)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Недопустимый формат уведомления GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Ошибки GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Успешные уведомления GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Регулируемые уведомления GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Ошибка из-за неправильного канала GCM  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Ошибки проверки подлинности MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Ошибка из-за поврежденного канала MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Канал MPNS отсоединен  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  Пропущенные уведомления MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Недопустимые учетные данные MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Недопустимый формат уведомления MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Ошибки MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Успешные уведомления MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Регулируемые уведомления MPNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Ошибки проверки подлинности WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Ошибка из-за поврежденного канала WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Канал WNS отсоединен  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Канал WNS регулируется  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  Пропущенные уведомления WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Ошибка из-за просроченного канала WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Ошибки авторизации WNS (недопустимые учетные данные)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Недопустимый формат уведомления WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Ошибки авторизации WNS (недопустимый маркер)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Ошибки WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Успешные уведомления WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Регулируемые уведомления WNS  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Ошибки авторизации WNS (нет доступа)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Ошибки авторизации WNS (неправильный маркер)  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  Операции регистрации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Операции создания регистрации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Операции удаления регистрации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Операции чтения регистрации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Операции обновления регистрации  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Запланированных уведомлений в состоянии ожидания  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  Объем доступной памяти  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  Объем доступной области подкачки, %  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  Использование выделенной памяти (в байтах), %  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  Процент времени на отложенные вызовы процедур  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  Процент свободных индексных дескрипторов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % свободного места  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % свободного места  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  Процент времени бездействия  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  Процент времени прерываний  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  Процент времени ожидания ввода-вывода  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  Процент времени с низким приоритетом  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % работы в привилегированном режиме;  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  Процент используемых индексных дескрипторов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  Объем используемой памяти, %  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  Используемое пространство, %  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  Объем используемой области подкачки, %  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  Процент времени пользователя  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Доступно Мбайт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Объем доступной памяти, МБ  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Объем доступной области подкачки, МБ  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (сек)"  |  Среднее время чтения с диска (с)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (сек)"  |  Среднее время чтения с диска (с)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Время обращения к диску (сек.)  |  Среднее время обращения к диску (сек.)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (сек)  |  Среднее время записи на диск (сек.)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (сек)  |  Среднее время записи на диск (сек.)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Полученных байтов/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Отправленных байтов/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Всего байтов/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  текущая длина очереди диска  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Скорость чтения с диска (байт/с)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Обращений чтения с диска/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Обращений чтения с диска/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  Скорость записи на диск (байт/с)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Обращений записи на диск/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Обращений записи на диск/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Объем свободной физической памяти  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Свободное пространство в файлах подкачки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Объем свободной виртуальной памяти  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Скорость обмена с логическим диском, байт/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Чтение страниц/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Запись страниц/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Страниц/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Процент времени работы в привилегированном режиме  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Процент времени пользователя  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Скорость обмена с логическим диском, байт/с  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Процессы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  Длина очереди процессора.  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Размер хранения в файлах подкачки  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Общее количество байт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Всего получено байт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Всего передано байт  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Всего конфликтов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Всего получено пакетов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Всего передано пакетов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Всего ошибок Rx  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Всего ошибок Tx  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Время работы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Используемая область подкачки, МБ  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Используемая память в КБ  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Объем используемой памяти, МБ  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Users  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Объем виртуальной общей памяти  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Событие  |  Событие  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.OperationalInsights/workspaces  |  Периодический сигнал  |  Периодический сигнал  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.OperationalInsights/workspaces  |  Обновить  |  Обновить  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Память  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Пробуксовка памяти (наборы данных)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Высокая загрузка QPU  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Длительность запроса (наборы данных)  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Длина очереди заданий пула запросов (наборы данных)  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  SearchLatency  |  Search Latency  |  Секунды  |  Среднее | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Search queries per second  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Throttled search queries percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Число активных сообщений в очереди или разделе.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  ЦП (не рекомендуется)  |  Процент  |  Максимальное значение | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  деадлеттередмессажес  |  Количество недоставленных сообщений в очереди или разделе.  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Входящих запросов  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Сообщения  |  Число сообщений в очереди или разделе.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  намеспацекпуусаже  |  ЦП  |  Процент  |  Максимальное значение | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  намеспацемеморюсаже  |  Использование памяти  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  счедуледмессажес  |  Число запланированных сообщений в очереди или разделе.  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Размер  |  Размер  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Использование памяти (не рекомендуется)  |  Процент  |  Максимальное значение | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  актуалкпу  |  актуалкпу  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  актуалмемори  |  актуалмемори  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аллокатедкпу  |  аллокатедкпу  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аллокатедмемори  |  аллокатедмемори  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аппликатионстатус  |  аппликатионстатус  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  контаинерстатус  |  контаинерстатус  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  График  |  График  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  меморютилизатион  |  меморютилизатион  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  рестарткаунт  |  рестарткаунт  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  сервицерепликастатус  |  сервицерепликастатус  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  сервицестатус  |  сервицестатус  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Число подключений  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Inbound Traffic (Входящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Количество сообщений  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Outbound Traffic (Исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Системные ошибки  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Ошибки пользователей  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Average CPU percentage (Средний процент использования ЦП)  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Количество считанных байт ввода-вывода  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Количество записанных байт ввода-вывода  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  io_requests  |  IO requests count (Количество запросов ввода-вывода)  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Зарезервированное дисковое пространство  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Использованное дисковое пространство  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Virtual core count (Число виртуальных ядер)  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_storage_used  |  Используемое пространство данных  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_used  |  DTU used  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers  |  storage_used  |  Используемое пространство данных  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Выделенное пространство данных  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Выплата за ЦП приложения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Процент загрузки ЦП приложения  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Процент памяти приложения  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Заблокировано брандмауэром  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Cache hit percentage (Процент попаданий в кэш)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Cache used percentage (Процент использования кэша)  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Неудачные подключения  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Успешные подключения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Ограничение ЦП  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Используемый ЦП  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  взаимоблокировка  |  Взаимоблокировки  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Лимит DTU  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU used  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU percentage  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Лимит DWU  |  Число  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU used  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Local tempdb percentage (Процент использования локальной базы данных tempdb)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Процент памяти  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  носителей.  |  Используемое пространство данных  |  Байт  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Размер файла данных tempdb, КБ  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Размер файла журнала tempdb, КБ  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Используемый журнал в процентах tempdb  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Выделенное пространство данных  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Процент выделенного пространства данных  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Ограничение ЦП  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Используемый ЦП  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Выделенное пространство данных  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Ограничение ЦП  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Используемый ЦП  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU used  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Используемое пространство данных  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU limit  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU used  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Максимальный размер данных  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Используемое пространство данных  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Размер файла данных tempdb, КБ  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Размер файла журнала tempdb, КБ  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Используемый журнал в процентах tempdb  |  Процент  |  Максимальное значение | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешарекуота  |  Размер квоты общей папки  |  Байт  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешареснапшоткаунт  |  Число моментальных снимков общей папки  |  Число  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешареснапшотсизе  |  Размер моментального снимка файлового ресурса  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/queueServices  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft.Storage/storageAccounts/tableServices  |  Транзакции  |  Транзакции  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентиопс  |  Всего операций ввода-вывода клиента  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентлатенци  |  Средняя задержка клиента  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентлоккиопс  |  Операции ввода-вывода блокировки клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентметадатареадиопс  |  ОПЕРАЦИЙ чтения метаданных клиента  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентметадатавритеиопс  |  Запись метаданных клиента в операции ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентреадиопс  |  Операции чтения клиента в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентреадсраугхпут  |  Средняя пропускная способность чтения кэша  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентвритеиопс  |  Число операций записи клиента в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  клиентвритесраугхпут  |  Средняя пропускная способность записи в кэш  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетасинквритесраугхпут  |  Пропускная способность асинхронной записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетфиллсраугхпут  |  Пропускная способность заполнения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржесеалс  |  Работоспособность целевого объекта хранилища  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетиопс  |  Всего Сторажетаржет операций ввода-вывода  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетлатенци  |  Задержка Сторажетаржет  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетметадатареадиопс  |  Сторажетаржет чтение метаданных операций ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетметадатавритеиопс  |  Сторажетаржет запись метаданных в секунду  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетреадахеадсраугхпут  |  Пропускная способность чтения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетреадиопс  |  Сторажетаржет чтение операций ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетсинквритесраугхпут  |  Пропускная способность синхронной записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржеттоталреадсраугхпут  |  Общая пропускная способность чтения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржеттоталвритесраугхпут  |  Общая пропускная способность записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетвритеиопс  |  Сторажетаржет запись в секунду  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. Сторажекаче/кэши  |  Время работы  |  Время работы  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  серверсинксессионресулт  |  Результат сеанса синхронизации  |  Число  |  Среднее | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекалледнетворкбитесбяппликатион  |  Размер отзыва по уровням облака по приложениям  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекалледтоталнетворкбитес  |  Размер отзыва при уровне облака  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекаллиототалсизебитес  |  Отзыв уровней в облаке  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекаллсраугхпутбитесперсеконд  |  Пропускная способность отзыва облачного уровня  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксерверхеартбеат  |  Состояние сервера в сети  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс  |  серверхеартбеат  |  Состояние сервера в сети  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс  |  серверрекаллиототалсизебитес  |  Отзыв уровней в облаке  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупсинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупсинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтсинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтсинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Неудачные запросы функций  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  События функций  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Запросы функций  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Ошибки преобразования данных  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Ошибки десериализации входа  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Неупорядоченные события  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Ранние входные события  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  Ошибки  |  Ошибки среды выполнения  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Байты входного события  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Входные события  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  инпутевентссаурцесбакклогжед  |  Необработанные входные события  |  Число  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  инпутевентссаурцесперсеконд  |  Полученные входные источники  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  События позднего ввода  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Выходные события  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Предельная задержка  |  Секунды  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Использование единиц потоковой передачи (%)  |  Процент  |  Максимальное значение | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадбитесперсеконд  |  Disk Read Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадлатенци  |  Задержка чтения с диска  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадоператионс  |  Операции чтения с диска  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дисквритебитесперсеконд  |  Disk Write Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискврителатенци  |  Задержка записи на диск  |  Миллисекунд  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дисквритеоператионс  |  Операции записи на диск  |  Число  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  меморяктиве  |  Память активна  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  меморигрантед  |  Предоставленная память  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  MemoryUsed  |  Используемая память  |  Байт  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байт  |  Всего | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  нетворкинбитесперсеконд  |  Сеть в байтах/с  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  нетворкаутбитесперсеконд  |  Сетевых исходящих байт/с  |  Байт/с  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | Нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  перцентажекпуреади  |  Процент готовности ЦП  |  Миллисекунд  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Активные запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Среднее время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Входящие данные:  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Выходные данные  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Disk Queue Length  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  HTTP 2xx  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP 3xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  HTTP 406:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  HTTP 4xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Ошибки HTTP-сервера:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Длина очереди HTTP:  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Крупный"  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Средний"  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Запросы  |  Запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Рабочие роли плана службы приложений уровня "Малый"  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Общее число внешних интерфейсов  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Доступные рабочие процессы  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Общее количество рабочих процессов  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Использованные рабочие процессы  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Входящие данные:  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesSent  |  Выходные данные  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Disk Queue Length  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Длина очереди HTTP:  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпклосеваит  |  Ожидание TCP-закрытия  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпклосинг  |  TCP-закрытие  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпестаблишед  |  Установлен TCP  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Ожидание TCP FIN 1  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Ожидание TCP FIN 2  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпластакк  |  Последнее подтверждение TCP  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпсинрецеивед  |  Получен TCP SYN  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпсинсент  |  TCP SYN отправлен  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткптимеваит  |  Время ожидания TCP  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AppConnections  |  Подключения  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageResponseTime  |  Среднее время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesReceived  |  Входящие данные:  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesSent  |  Выходные данные  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CpuTime  |  Время ЦП:  |  Секунды  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Текущее число сборок  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Function Execution Count  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Function Execution Units  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen0Collections  |  Сборок мусора поколения 0  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen1Collections  |  Сборок мусора поколения 1  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen2Collections  |  Сборок мусора поколения 2  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Маркеры  |  Число дескрипторов  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  хеалсчеккстатус  |  Состояние проверки работоспособности  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http2xx  |  HTTP 2xx  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http3xx  |  HTTP 3xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http401  |  HTTP 401:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http403  |  HTTP 403:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http404  |  HTTP 404:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http406  |  HTTP 406:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http4xx  |  HTTP 4xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http5xx  |  Ошибки HTTP-сервера:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  хттпреспонсетиме  |  Время отклика  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  PrivateBytes  |  Байт исключительного пользования  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Запросы  |  Запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Потоки  |  Число потоков  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomains  |  Всего доменов приложений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AppConnections  |  Подключения  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Среднее время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Входящие данные:  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesSent  |  Выходные данные  |  Байт  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CpuTime  |  Время ЦП:  |  Секунды  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Текущее число сборок  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Function Execution Count  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Function Execution Units  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Сборок мусора поколения 0  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Сборок мусора поколения 1  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Сборок мусора поколения 2  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Маркеры  |  Число дескрипторов  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  хеалсчеккстатус  |  Состояние проверки работоспособности  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http2xx  |  HTTP 2xx  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http3xx  |  HTTP 3xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http404  |  HTTP 404:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http406  |  HTTP 406:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http4xx  |  HTTP 4xx:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http5xx  |  Ошибки HTTP-сервера:  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  хттпреспонсетиме  |  Время отклика  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Байт исключительного пользования  |  Байт  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Запросы  |  Запросы  |  Число  |  Всего | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Потоки  |  Число потоков  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Всего доменов приложений  |  Число  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Число  |  Среднее | 
